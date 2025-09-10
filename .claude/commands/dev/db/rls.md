# /db-rls - Create Row Level Security Policies

## Expert: Supabase Security Specialist

Implement robust Row Level Security (RLS) for your Supabase tables.

## Command Usage

```bash
/db-rls <table_name>            # Generate RLS for table
/db-rls --public <table>        # Public read access
/db-rls --user-owned <table>    # User owns their data
/db-rls --team-based <table>    # Team/org based access
/db-rls --admin <table>         # Admin override policies
/db-rls --audit                 # Check all RLS policies
```

## RLS Patterns

### 1. Public Read, Authenticated Write
```sql
-- enable rls
alter table public.posts enable row level security;

-- public read
create policy "anyone can read posts"
  on public.posts for select
  to anon, authenticated
  using (published = true);

-- authenticated create
create policy "authenticated can create posts"
  on public.posts for insert
  to authenticated
  with check (auth.uid() = author_id);

-- owner update
create policy "authors can update own posts"
  on public.posts for update
  to authenticated
  using (auth.uid() = author_id)
  with check (auth.uid() = author_id);

-- owner delete
create policy "authors can delete own posts"
  on public.posts for delete
  to authenticated
  using (auth.uid() = author_id);
```

### 2. User-Owned Data
```sql
-- enable rls
alter table public.user_data enable row level security;

-- users see only their data
create policy "users can view own data"
  on public.user_data for select
  to authenticated
  using (auth.uid() = user_id);

-- users create their data
create policy "users can insert own data"
  on public.user_data for insert
  to authenticated
  with check (auth.uid() = user_id);

-- users update their data
create policy "users can update own data"
  on public.user_data for update
  to authenticated
  using (auth.uid() = user_id)
  with check (auth.uid() = user_id);

-- users delete their data
create policy "users can delete own data"
  on public.user_data for delete
  to authenticated
  using (auth.uid() = user_id);
```

### 3. Team/Organization Based
```sql
-- enable rls
alter table public.team_resources enable row level security;

-- helper function for team membership
create or replace function public.is_team_member(team_id uuid)
returns boolean
language plpgsql
security definer
as $$
begin
  return exists (
    select 1
    from public.team_members
    where team_members.team_id = $1
    and team_members.user_id = auth.uid()
    and team_members.status = 'active'
  );
end;
$$;

-- team members can view
create policy "team members can view resources"
  on public.team_resources for select
  to authenticated
  using (is_team_member(team_id));

-- team members can create
create policy "team members can create resources"
  on public.team_resources for insert
  to authenticated
  with check (
    is_team_member(team_id)
    and auth.uid() = created_by
  );

-- team admins can update
create policy "team admins can update resources"
  on public.team_resources for update
  to authenticated
  using (
    exists (
      select 1
      from public.team_members
      where team_members.team_id = team_resources.team_id
      and team_members.user_id = auth.uid()
      and team_members.role in ('admin', 'owner')
    )
  );
```

### 4. Role-Based Access
```sql
-- admin override for all operations
create policy "admins have full access"
  on public.sensitive_data for all
  to authenticated
  using (
    exists (
      select 1
      from public.user_roles
      where user_id = auth.uid()
      and role = 'admin'
    )
  );

-- moderators can read and update
create policy "moderators can read"
  on public.content for select
  to authenticated
  using (
    exists (
      select 1
      from public.user_roles
      where user_id = auth.uid()
      and role in ('moderator', 'admin')
    )
  );
```

### 5. Time-Based Access
```sql
-- access during business hours
create policy "business hours access"
  on public.time_sensitive for select
  to authenticated
  using (
    extract(hour from now() at time zone 'UTC') between 9 and 17
    and extract(dow from now() at time zone 'UTC') between 1 and 5
  );

-- temporary access tokens
create policy "valid token access"
  on public.protected_resources for select
  to authenticated
  using (
    exists (
      select 1
      from public.access_tokens
      where resource_id = protected_resources.id
      and user_id = auth.uid()
      and expires_at > now()
      and revoked_at is null
    )
  );
```

## Advanced Patterns

### Hierarchical Access
```sql
-- recursive organization structure
create policy "hierarchical org access"
  on public.org_data for select
  to authenticated
  using (
    exists (
      with recursive org_tree as (
        select id, parent_id
        from public.organizations
        where id = org_data.org_id
        
        union all
        
        select o.id, o.parent_id
        from public.organizations o
        join org_tree on o.id = org_tree.parent_id
      )
      select 1
      from org_tree
      join public.org_members on org_members.org_id = org_tree.id
      where org_members.user_id = auth.uid()
    )
  );
```

### Composite Conditions
```sql
-- multiple conditions with different permissions
create policy "complex access rules"
  on public.documents for select
  to authenticated
  using (
    -- owner can always see
    owner_id = auth.uid()
    or
    -- public documents
    visibility = 'public'
    or
    -- shared with user
    exists (
      select 1 from public.document_shares
      where document_id = documents.id
      and user_id = auth.uid()
      and revoked_at is null
    )
    or
    -- team member
    exists (
      select 1 from public.team_documents td
      join public.team_members tm on tm.team_id = td.team_id
      where td.document_id = documents.id
      and tm.user_id = auth.uid()
    )
  );
```

## Testing RLS Policies

```sql
-- test as specific user
set role authenticated;
set request.jwt.claim.sub = 'user-uuid-here';

-- verify policy works
select * from public.your_table;

-- reset
reset role;
reset request.jwt.claim.sub;

-- check policy exists
select * from pg_policies 
where tablename = 'your_table';
```

## Common Pitfalls

1. **Forgetting WITH CHECK** - Updates need both USING and WITH CHECK
2. **Missing roles** - Specify both anon and authenticated when needed
3. **Performance** - Complex policies can slow queries
4. **Bypass routes** - Service role bypasses RLS
5. **Testing** - Always test policies with different user contexts

## Performance Optimization

```sql
-- create indexes for policy conditions
create index idx_user_id on public.table(user_id);
create index idx_team_members on public.team_members(user_id, team_id);

-- use functions for complex logic
create function check_access(resource_id uuid)
returns boolean
language sql
stable
security definer
as $$
  -- complex logic here
  select true;
$$;

-- use in policy
create policy "optimized access"
  on public.resources for select
  using (check_access(id));
```

## MCP Integration

```bash
mcp: supabase.apply_migration
  params: {
    name: "add_rls_policies",
    query: "<rls_sql>"
  }

# Verify with
mcp: supabase.get_advisors
  params: { type: "security" }
```

## Security Checklist

- [ ] RLS enabled on all tables
- [ ] Policies for all CRUD operations
- [ ] Separate policies for anon/authenticated
- [ ] WITH CHECK on insert/update policies
- [ ] Tested with different user contexts
- [ ] Indexes on policy condition columns
- [ ] No infinite loops in recursive policies
- [ ] Service role access documented

---

Last Updated: 2025-01-10