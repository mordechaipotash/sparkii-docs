## 🎯 Top 8 Custom Commands to Implement

### 1. `/hyperfocus-mode` - Deep Work Session Manager
```markdown
---
description: "Manage hyperfocus sessions with full context preservation and breakthrough tracking"
argument-hint: "[--start goal] [--checkpoint] [--pause] [--resume] [--complete]"
allowed-tools: ["mcp__supabase_sparkii__execute_sql", "Write", "Read", "TodoWrite"]
---

# Hyperfocus Session Manager

Manage deep work sessions with the Sparkii hyperfocus system:

## Commands:
- `--start <goal>`: Begin focused session with clear objective
- `--checkpoint`: Save current context and progress
- `--pause`: Pause with full context preservation
- `--resume`: Resume from last checkpoint
- `--complete`: End session with summary and insights

## Features:
- Track session in hyperfocus_sessions table
- Preserve context across breaks
- Monitor breakthrough moments
- Calculate focus score
- Generate session summary

## Integration:
- Saves to Supabase for pattern analysis
- Links to relevant conversations
- Tracks revenue opportunities discovered
- Maintains TodoWrite sync
```

### 2. `/component-forge` - Rapid UI Component Creation
```markdown
---
description: "Create production-ready Sparkii components with MCP verification"
argument-hint: "[component-type] [--name] [--schema] [--realtime]"
allowed-tools: ["mcp__shadcn__*", "Write", "MultiEdit", "mcp__supabase_sparkii__*"]
---

# Component Forge - MCP-First UI Creation

Generate production-ready components with mandatory MCP verification:

## Component Types:
- `--venture-card`: Venture monitoring card with health metrics
- `--metric-tile`: Real-time metric display with trends
- `--chart <type>`: Data visualization (line/bar/pie/area)
- `--form <schema>`: Form with Zod validation and Supabase
- `--table <source>`: Data table with sorting/filtering/export

## Process:
1. Check shadcn blocks for existing solutions
2. Get component demo via MCP
3. Generate TypeScript implementation
4. Add Supabase real-time subscriptions
5. Include loading/error states
6. Apply Sparkii theming

## Output:
- Component file with proper typing
- Supabase subscription hook
- Loading/error boundaries
- Dark mode support
- Mobile responsive
```

### 3. `/security-audit` - Comprehensive Security Scanner
```markdown
---
description: "Run comprehensive security audit across code and database"
argument-hint: "[--full] [--rls] [--deps] [--secrets] [--fix]"
allowed-tools: ["mcp__supabase_sparkii__get_advisors", "Grep", "Read", "mcp__github__*"]
---

# Security Audit System

Comprehensive security vulnerability scanning:

## Audit Types:
- `--full`: Complete security audit
- `--rls`: Supabase Row Level Security policies
- `--deps`: Dependency vulnerabilities
- `--secrets`: Scan for exposed API keys
- `--fix`: Generate fix suggestions

## Checks:
1. Supabase security advisors
2. RLS policy coverage
3. API key exposure
4. Dependency vulnerabilities
5. Authentication flows
6. CORS configuration
7. Input validation

## Output:
- Security score (0-100)
- Critical/High/Medium/Low issues
- Remediation steps
- Auto-fix scripts where possible
```

### 4. `/revenue-optimizer` - Revenue Analysis & SHELET Mapping
```markdown
---
description: "Analyze revenue opportunities and create implementation paths"
argument-hint: "[concept/query] [--forecast] [--wotcfy] [--ventures all/specific]"
allowed-tools: ["mcp__supabase_sparkii__execute_sql", "Write", "TodoWrite"]
---

# Revenue Optimizer - Concept to Cash Pipeline

Transform any concept into revenue opportunities:

## Analysis Modes:
- `--opportunities`: Find revenue in conversations
- `--forecast`: Project future revenue
- `--wotcfy`: WOTCFY-specific metrics
- `--ventures`: Map to existing ventures

## SHELET Integration:
1. Search relevant conversations
2. Identify monetization patterns
3. Calculate revenue potential
4. Generate implementation plan
5. Create venture integration strategy

## Output:
- Revenue opportunities (immediate/strategic/long-term)
- Implementation roadmap
- Venture synergy matrix
- ROI projections
- Quick win identification
```

### 5. `/venture-health` - Multi-Venture Status Monitor
```markdown
---
description: "Real-time health monitoring across all Sparkii ventures"
argument-hint: "[venture-name/--all] [--metrics revenue/users/performance] [--alert]"
allowed-tools: ["mcp__supabase_sparkii__execute_sql", "mcp__supabase_sparkii__get_logs"]
---

# Venture Health Monitoring System

Real-time monitoring of all Sparkii ventures:

## Metrics Tracked:
- Revenue (MTD, YTD, growth rate)
- Health score (0-100)
- User engagement metrics
- System performance
- Alert conditions

## Query Options:
- `--all`: Check all ventures
- `--venture <name>`: Specific venture
- `--metrics <type>`: Focus area
- `--alert`: Only show issues

## Output:
- Health dashboard
- Performance trends
- Alert conditions
- Recommendations
- Cross-venture comparisons
```

### 6. `/wiki-promote` - Sandbox to Wiki Pipeline
```markdown
---
description: "Promote mature sandbox concepts to structured wiki entries"
argument-hint: "[sandbox-file/--auto-detect] [--score] [--force]"
allowed-tools: ["Read", "Write", "MultiEdit", "Glob"]
---

# Wiki Promotion System

Automate sandbox to wiki knowledge crystallization:

## Evaluation Criteria:
- Concept clarity (0-10)
- Business validation (0-10)
- Implementation plan (0-10)
- Connection potential (0-10)
- Sovereignty score (0-10)

## Process:
1. Scan sandbox files for maturity
2. Calculate promotion score
3. Auto-promote if score >7.0
4. Restructure for wiki format
5. Create bidirectional links
6. Update connection map

## Options:
- `--auto-detect`: Find ready sandboxes
- `--score`: Show scores only
- `--force`: Promote regardless of score
```

### 7. `/conversation-insights` - Deep Conversation Analysis
```markdown
---
description: "Extract actionable insights from specific conversations"
argument-hint: "[conversation-id/--recent N] [--focus business/technical/creative]"
allowed-tools: ["mcp__supabase_sparkii__execute_sql", "Write"]
---

# Conversation Intelligence Extractor

Mine insights from 11,270+ AI conversations:

## Analysis Types:
- Business opportunities
- Technical patterns
- Creative insights
- Implementation steps
- Knowledge connections

## Query Options:
- `--recent N`: Last N conversations
- `--id <conv_id>`: Specific conversation
- `--focus <type>`: Analysis lens
- `--actionable`: Only actionable insights

## Output:
- Key insights extracted
- Actionability score
- Business relevance
- Implementation steps
- Wiki connection suggestions
```

### 8. `/tool-audit` - Tool Effectiveness Analyzer
```markdown
---
description: "Analyze tool usage patterns and effectiveness from documentation"
argument-hint: "[tool-name/--all] [--metric usage/efficiency/integration]"
allowed-tools: ["Read", "Grep", "TodoWrite"]
---

# Tool Effectiveness Auditor

Analyze your tool documentation for optimization opportunities:

## Analysis Areas:
- Most valuable tools by usage
- Token efficiency gains
- Integration opportunities
- Workflow optimizations
- Tool combination strategies

## Metrics:
- Usage frequency
- Token savings
- Time savings
- Error reduction
- Synergy scores

## Output:
- Tool effectiveness ranking
- Optimization recommendations
- Integration opportunities
- Deprecation candidates
- Workflow improvements
```

---

## 🚫 Commands to SKIP (Overengineered/Redundant)

### Why These Were Rejected:

1. **`/test-generator`** - Tests need human context and understanding
2. **`/perf-monitor`** - Vercel/Railway already provide this
3. **`/mcp-orchestrator`** - Too abstract, existing tools handle coordination
4. **`/knowledge-graph`** - Overlaps with existing wiki connections
5. **`/deploy-check`** - Partially covered by security-audit
6. **`/branch-manager`** - Git workflow is already well-handled

---

## 💡 Key Insights from Analysis

### Strengths to Preserve:
1. **Rich Context**: 11,270 conversations provide immense value
2. **SHELET Framework**: Unique knowledge crystallization pipeline
3. **SuperClaude Integration**: Business panel adds strategic depth
4. **MCP-First Philosophy**: Prevents hallucination and ensures quality

### Improvements Made:
1. **Cleaner YAML formatting** for better readability
2. **Simplified command structure** focusing on practical needs
3. **Merged redundant commands** into more powerful tools
4. **Added session management** with hyperfocus-mode

### Implementation Priority:
1. **Immediate**: `/hyperfocus-mode`, `/component-forge`, `/security-audit`
2. **This Week**: `/revenue-optimizer`, `/venture-health`
3. **Next Sprint**: `/wiki-promote`, `/conversation-insights`, `/tool-audit`

---

## 📊 Expected Impact

### Efficiency Gains:
- **50% reduction** in component development time
- **30-50% token savings** through optimized commands
- **73% faster** searches with parallel agents
- **10x insights** from conversation mining

### Revenue Impact:
- Direct monetization path identification
- Venture health monitoring prevents revenue loss
- SHELET pipeline accelerates idea → revenue cycle
- Cross-venture synergy identification

---

## 🎯 Next Steps

1. **Create CLAUDE.md** in project root
2. **Implement top 3 commands** this week
3. **Test with real workflows**
4. **Iterate based on usage**
5. **Document patterns** in wiki

---

*"The bottleneck IS the amplifier" - Every tool compounds into the network*

**Generated**: January 2025  
**Purpose**: Optimize Sparkii Command Center for 10x development velocity  
**Result**: Unified configuration merging best practices from multiple analyses