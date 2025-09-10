# explore - Interactive Discovery & Brainstorming Command

## Purpose
A discovery-first command that prioritizes understanding, research, and collaborative exploration BEFORE any implementation. This command enforces a dialogue-based workflow where Claude must prove understanding and get explicit approval before writing any code.

## Command Usage
```
/explore [topic/request]
/xp [topic/request]  # Short alias
```

## Behavioral Contract

### Phase 1: UNDERSTANDING (Mandatory)
Claude MUST:
1. **Restate the request** in detail to prove comprehension
2. **Ask clarifying questions** about ambiguous aspects
3. **Identify assumptions** that need validation
4. **Map scope boundaries** - what's included/excluded
5. **Surface hidden complexity** or potential challenges

Output format:
```
📍 What I understand you want:
[Detailed restatement]

❓ Questions to clarify:
- [Question 1]
- [Question 2]

⚠️ Assumptions I'm making:
- [Assumption 1]
- [Assumption 2]

🎯 Scope as I see it:
- Included: [...]
- Excluded: [...]
- Gray areas: [...]
```

### Phase 2: RESEARCH (Active Discovery)
Claude MUST:
1. **Search the repository** for existing patterns/solutions
2. **Check the database** for relevant schemas/data
3. **Research online** for best practices/examples
4. **Analyze dependencies** and technical constraints
5. **Identify similar implementations** in the codebase

Research actions:
- Use Grep/Glob to find existing patterns
- Use Supabase MCP to explore database structure
- Use WebSearch/Exa for external research
- Use Read to understand current implementations
- Check package.json for available libraries

Output format:
```
🔍 Repository findings:
- [Pattern/file found]
- [Existing implementation]

🗄️ Database insights:
- [Relevant tables/schemas]
- [Data relationships]

🌐 External research:
- [Best practice/pattern]
- [Library/tool option]

📦 Technical constraints:
- [Dependencies available]
- [Limitations found]
```

### Phase 3: OPTIONS (Brainstorming)
Claude MUST present AT LEAST 3 actionable approaches:

```
📋 Option 1: [Descriptive Name]
- Approach: [How it works]
- Pros: [Benefits]
- Cons: [Drawbacks]
- Effort: [Low/Medium/High]
- Risk: [Low/Medium/High]

📋 Option 2: [Descriptive Name]
- Approach: [How it works]
- Pros: [Benefits]
- Cons: [Drawbacks]
- Effort: [Low/Medium/High]
- Risk: [Low/Medium/High]

📋 Option 3: [Descriptive Name]
- Approach: [How it works]
- Pros: [Benefits]
- Cons: [Drawbacks]
- Effort: [Low/Medium/High]
- Risk: [Low/Medium/High]

💡 My recommendation:
[Which option and why]
```

### Phase 4: CONFIRMATION (Required Gate)
Claude MUST:
1. **Stop and wait** for explicit user approval
2. **Never proceed** to implementation without confirmation
3. **Accept refinements** and iterate on options

```
🛑 WAITING FOR YOUR DECISION

Ready to proceed with implementation?
- Type the option number (1, 2, 3) to proceed
- Or provide feedback to refine the approach
- Or ask for different options
```

### Phase 5: IMPLEMENTATION (Only After Approval)
Only after explicit approval:
1. Create implementation plan with TodoWrite
2. Begin structured implementation
3. Follow approved approach exactly

## Enforcement Rules

### NEVER:
- ❌ Write code without explicit approval
- ❌ Create files during exploration
- ❌ Skip the research phase
- ❌ Present fewer than 3 options
- ❌ Make implementation decisions unilaterally
- ❌ Assume understanding without verification

### ALWAYS:
- ✅ Prove understanding first
- ✅ Research thoroughly (repo + db + web)
- ✅ Present multiple actionable options
- ✅ Wait for explicit approval
- ✅ Respect "don't implement" signals
- ✅ Iterate based on feedback

## Example Interaction

```
User: /explore add a way to track user sessions

Claude:
📍 What I understand you want:
You want to add session tracking functionality to monitor user activity, likely including session start/end times, duration, and possibly user actions during the session.

❓ Questions to clarify:
- Should this track anonymous or authenticated users?
- What specific session data do you need (page views, clicks, time on page)?
- Do you need real-time tracking or batch processing?
- Should sessions timeout after inactivity?

⚠️ Assumptions I'm making:
- You want persistent storage of session data
- Sessions should be tied to user accounts
- You need basic analytics capabilities

🎯 Scope as I see it:
- Included: Session creation, tracking, storage
- Excluded: Complex analytics, third-party integration
- Gray areas: Privacy compliance, data retention

[Continues with research and options...]

🛑 WAITING FOR YOUR DECISION
```

## Integration with Other Commands

- Use BEFORE `/implement` for complex features
- Complements `/brainstorm` with more structure
- Can chain into `/design` after option selection
- Natural precursor to `/sc:task-panel`

## Quick Aliases
- `/xp` - Short form of /explore
- `/explore --quick` - Faster exploration with 2 options minimum
- `/explore --deep` - Extended research phase
- `/explore --no-web` - Skip external research

## Success Metrics
- Zero unauthorized implementations
- User confirms understanding accuracy >90%
- Options presented are genuinely actionable
- Research uncovers non-obvious insights
- User feels heard and in control