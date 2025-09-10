# /shadcn-rules - MCP-First shadcn/ui Development Rules

## Command Usage

### Basic Commands
```bash
/shadcn-rules --list          # Show this help and all available options
/shadcn-rules --workflow      # Display the mandatory MCP workflow
/shadcn-rules --checklist     # Show component completion checklist
/shadcn-rules --patterns      # Display Sparkii component patterns
/shadcn-rules --mistakes      # Common mistakes to avoid
/shadcn-rules --mcp           # List all MCP shadcn commands
/shadcn-rules --quick         # Quick reference for daily development
```

### Advanced Options
```bash
/shadcn-rules --component <name>    # Get specific component guidelines
/shadcn-rules --debug <issue>       # Troubleshooting guide for common issues
/shadcn-rules --validate            # Validate current component against rules
/shadcn-rules --examples            # Show real implementation examples
```

## 🎯 CRITICAL: MCP-First Development

### The Golden Rule
**NEVER implement UI without MCP context**. Every component must be verified through MCP tools first.

## 📋 Sparkii UI Development Workflow

### Phase 1: Planning (NO CODE)
When building any Sparkii UI feature:
1. Use MCP `list_components` to see available components
2. Use MCP `list_blocks` for pre-built solutions
3. Create component mapping in `ui-plans/`
4. Document before coding

### Phase 2: Component Research (MANDATORY)
Before implementing:
1. **ALWAYS** use `get_component_demo` first
2. Study demo implementation patterns
3. Use `get_component` for source reference
4. Map to Sparkii requirements

### Phase 3: Implementation
1. Install: `npx shadcn@latest add [component]`
2. Follow EXACT demo patterns
3. Apply Sparkii theming
4. Connect Supabase data
5. Test all breakpoints

## 🚀 Sparkii-Specific Patterns

### Venture Cards
```typescript
// Always use Card + Badge + Progress
VentureCard = Card + {
  header: CardHeader + Badge(status)
  metrics: CardContent + Progress(health)
  actions: CardFooter + Button(primary)
}
```

### Dashboard Widgets
```typescript
// Standard widget structure
SparkiiWidget = Card + {
  title: CardTitle + Tooltip
  value: CardDescription + AnimatedNumber
  chart: Recharts + Skeleton(loading)
  trend: Badge + ArrowIcon
}
```

### Data Tables
```typescript
// All tables must have
SparkiiTable = DataTable + {
  sorting: true,
  filtering: true,
  pagination: true,
  export: DropdownMenu,
  realtime: Supabase.subscribe()
}
```

## 🎨 Sparkii Brand Integration

### Color Variables
```css
/* Sparkii brand colors */
--sparkii-primary: hsl(217, 91%, 60%)
--sparkii-success: hsl(142, 76%, 36%)
--sparkii-warning: hsl(38, 92%, 50%)
--sparkii-danger: hsl(0, 84%, 60%)
--sparkii-ai: hsl(280, 85%, 60%)
```

### Component Priorities
1. **Blocks First**: Check if a block exists
2. **Compose Second**: Combine existing components
3. **Custom Last**: Only if absolutely necessary

## ✅ Sparkii Component Checklist

Before marking complete:
- [ ] MCP demo verified
- [ ] Supabase connected
- [ ] Real-time updates work
- [ ] Mobile responsive
- [ ] Dark mode tested
- [ ] Loading states
- [ ] Error boundaries
- [ ] TypeScript strict
- [ ] Accessibility (a11y)

## 🔧 Sparkii UI Components Map

### Executive Dashboard
- `dashboard-01` block (modified)
- Card components for ventures
- Chart components for metrics
- Table for transactions
- Sheet for mobile nav

### Venture Management
- Card with collapsible details
- Progress bars for health
- Badge for status
- Tabs for sections
- Dialog for quick actions

### AI Operations
- Alert for service status
- Skeleton for loading
- Toast for notifications
- RadioGroup for model selection
- Slider for parameters

### Financial Tracking
- Table with virtual scroll
- Input with currency format
- DatePicker for ranges
- Select for categories
- Button with loading states

## 🚫 Common Mistakes to Avoid

### ❌ DON'T
- Implement without MCP demo
- Use inline styles
- Forget Supabase RLS
- Skip loading states
- Mix component versions
- Hard-code data

### ✅ DO
- Always check MCP first
- Use Tailwind classes
- Implement proper auth
- Handle all states
- Version lock dependencies
- Use environment variables

## 🔄 Real-time Data Pattern

```typescript
// Standard Sparkii realtime component
export function SparkiiRealtimeComponent({ table, filters }) {
  // 1. Get UI from MCP demo
  // 2. Set up Supabase subscription
  // 3. Handle connection states
  // 4. Update optimistically
  // 5. Rollback on error
}
```

## 📊 Performance Rules

### Critical Metrics
- First Contentful Paint < 1.2s
- Time to Interactive < 3.5s
- Bundle size per route < 300KB
- Lighthouse score > 90

### Optimization
- Lazy load heavy components
- Use React.memo strategically
- Virtualize long lists
- Optimize images with next/image
- Code split by route

## 🔧 MCP shadcn Commands Reference

### Discovery Phase
```bash
# Browse available components
mcp: shadcn.list_components

# Check pre-built UI blocks
mcp: shadcn.list_blocks

# Search for specific component
mcp: shadcn.search_components --query "data table"
```

### Research Phase (MANDATORY)
```bash
# ALWAYS get demo first!
mcp: shadcn.get_component_demo --name "card"

# Get component source
mcp: shadcn.get_component --name "card"

# Get dependencies and metadata
mcp: shadcn.get_component_metadata --name "card"
```

### Block Implementation
```bash
# Get complete block source
mcp: shadcn.get_block --name "dashboard-01"

# List available blocks by category
mcp: shadcn.list_blocks --category "dashboard"
```

## 🛠 Development Commands

### Quick Actions
```bash
# Add component with Sparkii wrapper
pnpm sparkii:add [component]

# Create venture widget
pnpm sparkii:widget [name]

# Test all responsive
pnpm test:responsive

# Check bundle size
pnpm analyze
```

## 📝 Documentation Requirements

Every Sparkii component needs:
```typescript
/**
 * @sparkii
 * @component VentureCard
 * @supabase ventures, metrics
 * @realtime true
 * @responsive mobile, tablet, desktop
 */
```

## 🔐 Security Considerations

- Always use Supabase RLS
- Validate all user inputs
- Sanitize displayed data
- Use HTTPS for all APIs
- Implement rate limiting
- Add error boundaries

## 🎯 Final Reminders

1. **MCP FIRST, CODE SECOND**
2. Blocks > Components > Custom
3. Test everything
4. Document patterns
5. Ship at 80%

This is the Sparkii way.

---

## Command Summary

```bash
/shadcn-rules --list       # This help
/shadcn-rules --workflow   # MCP workflow  
/shadcn-rules --checklist  # Completion checklist
/shadcn-rules --patterns   # Component patterns
/shadcn-rules --mistakes   # What to avoid
/shadcn-rules --mcp        # MCP commands
/shadcn-rules --quick      # Quick reference
/shadcn-rules --examples   # Code examples
/shadcn-rules --debug      # Troubleshooting
/shadcn-rules --validate   # Pre-commit checks
```

Remember: **MCP Demo → Source → Implementation → Test → Commit**

Last Updated: 2025-09-08