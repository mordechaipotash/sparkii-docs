# /db-functions - Create Database Functions

## Expert: Postgres Function Developer

Create secure, efficient database functions for Supabase.

## Command Usage

```bash
/db-functions <function_name>       # Create new function
/db-functions --trigger <name>      # Create trigger function
/db-functions --rpc <name>          # Create RPC function
/db-functions --auth <name>         # Create auth helper function
/db-functions --update-timestamp    # Create updated_at trigger
```

## Function Templates

### Basic Function Structure
```sql
create or replace function public.function_name(
  param1 type,
  param2 type default value
)
returns return_type
language plpgsql
security definer
set search_path = public
as $$
begin
  -- function logic here
  return result;
end;
$$;

-- grant execute permissions
grant execute on function public.function_name to authenticated;
```

### Common Function Patterns

#### Updated At Trigger
```sql
create or replace function public.update_updated_at_column()
returns trigger
language plpgsql
as $$
begin
  new.updated_at = now();
  return new;
end;
$$;
```

#### Get User ID
```sql
create or replace function public.get_current_user_id()
returns uuid
language sql
stable
security definer
as $$
  select auth.uid();
$$;
```

#### Check User Role
```sql
create or replace function public.is_admin()
returns boolean
language plpgsql
security definer
as $$
begin
  return exists (
    select 1
    from public.user_roles
    where user_id = auth.uid()
    and role = 'admin'
  );
end;
$$;
```

#### Increment Counter
```sql
create or replace function public.increment_counter(
  table_name text,
  row_id uuid,
  column_name text,
  increment_by int default 1
)
returns void
language plpgsql
security definer
as $$
begin
  execute format(
    'update %I set %I = %I + $1 where id = $2',
    table_name, column_name, column_name
  ) using increment_by, row_id;
end;
$$;
```

### RPC Functions for Supabase

```sql
-- rpc function callable from client
create or replace function public.search_profiles(
  search_query text
)
returns setof public.profiles
language plpgsql
stable
as $$
begin
  return query
  select *
  from public.profiles
  where 
    username ilike '%' || search_query || '%'
    or full_name ilike '%' || search_query || '%'
  order by created_at desc
  limit 50;
end;
$$;

-- grant permissions
grant execute on function public.search_profiles to authenticated;
grant execute on function public.search_profiles to anon;
```

### Trigger Functions

```sql
-- audit log trigger
create or replace function public.audit_log_trigger()
returns trigger
language plpgsql
security definer
as $$
begin
  insert into public.audit_logs (
    table_name,
    operation,
    user_id,
    old_data,
    new_data,
    created_at
  ) values (
    tg_table_name,
    tg_op,
    auth.uid(),
    to_jsonb(old),
    to_jsonb(new),
    now()
  );
  return new;
end;
$$;

-- attach to table
create trigger audit_trigger
  after insert or update or delete on public.your_table
  for each row
  execute function public.audit_log_trigger();
```

## Security Best Practices

1. **Use SECURITY DEFINER carefully** - Runs with creator's privileges
2. **Set search_path** - Prevent schema injection
3. **Validate inputs** - Check for SQL injection
4. **Grant minimal permissions** - Only what's needed
5. **Use STABLE/IMMUTABLE** - For query optimization

## Performance Tips

- Use `IMMUTABLE` for deterministic functions
- Use `STABLE` for functions reading database
- Use `VOLATILE` (default) for functions with side effects
- Create indexes for function-based queries
- Use `RETURNS TABLE` for set-returning functions

## Testing Functions

```sql
-- test function execution
select * from public.function_name('param1', 'param2');

-- check permissions
select has_function_privilege('authenticated', 'public.function_name', 'execute');

-- view function definition
select prosrc from pg_proc where proname = 'function_name';
```

## MCP Integration

```bash
mcp: supabase.apply_migration
  params: {
    name: "create_functions",
    query: "<function_sql>"
  }

# Test with
mcp: supabase.execute_sql
  params: {
    query: "select * from function_name()"
  }
```

---

Last Updated: 2025-01-10