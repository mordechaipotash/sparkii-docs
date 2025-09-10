---
description: "Lightning-fast natural language queries for your 11,283 enriched conversations - find exactly what you need in seconds"
argument-hint: "[natural language query like: 'business gold', 'epic ai', 'starred today', 'wotc automation']"
allowed-tools: ["mcp__supabase_sparkii__execute_sql", "mcp__supabase_sparkii__list_tables"]
---

# /sparkii-query - Instant Knowledge Discovery Engine

Transform natural language into powerful database queries. Find conversations instantly from your 11,283 enriched chat histories.

## 🎯 Query Parser

Analyze user input: "$ARGUMENTS"

### Step 1: Intent Detection
Determine query type and build appropriate SQL.

## 📚 Natural Language Mappings

### Business & Revenue Keywords
```yaml
"business", "revenue", "profit", "money", "earning", "income", "monetize", "customer", "client", "sales":
  → WHERE business_relevance_score >= 0.75
  
"business gold", "revenue opportunities", "money makers":
  → WHERE business_relevance_score >= 0.9 AND actionability_score >= 0.7
  
"wotc", "tax credit":
  → WHERE (title ILIKE '%WOTC%' OR title ILIKE '%tax%credit%')
```

### Quality & Complexity Keywords
```yaml
"epic", "massive", "huge":
  → WHERE estimated_tokens > 50000
  
"complex", "advanced", "sophisticated":
  → WHERE complexity_level >= 4
  
"simple", "basic", "easy":
  → WHERE complexity_level <= 2
  
"actionable", "practical", "implementable":
  → WHERE actionability_score >= 0.7
  
"starred", "favorite", "best":
  → WHERE is_starred = true
```

### Topic Keywords
```yaml
"ai", "ml", "gpt", "claude", "llm":
  → WHERE primary_topic = 'AI/ML'
  
"automation", "automate", "workflow":
  → WHERE primary_topic = 'Automation'
  
"web", "website", "frontend", "backend":
  → WHERE primary_topic = 'Web Development'
  
"mobile", "app", "ios", "android":
  → WHERE primary_topic = 'Mobile Development'
  
"data", "database", "analytics", "dashboard":
  → WHERE primary_topic = 'Data & Analytics'
  
"api", "integration", "webhook":
  → WHERE primary_topic = 'API & Integration'
```

### Temporal Keywords
```yaml
"today":
  → WHERE DATE(occurred_at) = CURRENT_DATE
  
"yesterday":
  → WHERE DATE(occurred_at) = CURRENT_DATE - 1
  
"this week", "recent":
  → WHERE occurred_at >= CURRENT_DATE - INTERVAL '7 days'
  
"this month":
  → WHERE occurred_at >= CURRENT_DATE - INTERVAL '30 days'
  
"old", "ancient", "historical":
  → WHERE occurred_at < CURRENT_DATE - INTERVAL '180 days'
  
"no date", "undated", "null date":
  → WHERE occurred_at IS NULL
```

### Content Type Keywords
```yaml
"code", "coding", "programming":
  → WHERE has_code_blocks = true
  
"technical", "tech":
  → WHERE has_technical_content = true
  
"error", "bug", "issue", "problem":
  → WHERE has_error_messages = true
  
"troubleshooting", "debugging", "fixing":
  → WHERE conversation_type = 'troubleshooting'
  
"creative", "design", "idea":
  → WHERE conversation_type = 'creative'
  
"analysis", "analyze", "research":
  → WHERE conversation_type = 'analysis'
```

### Special Query Patterns
```yaml
"best work":
  → WHERE is_starred = true AND complexity_level >= 4 AND estimated_tokens > 20000
  
"quick wins":
  → WHERE actionability_score >= 0.8 AND complexity_level <= 3
  
"hidden gems":
  → WHERE estimated_tokens > 30000 AND business_relevance_score < 0.5
  
"needs review":
  → WHERE actionability_score >= 0.6 AND is_starred = false
  
"follow up", "incomplete":
  → WHERE conversation_completeness < 0.7 OR conversation_type = 'task'
  
"top [N]", "best [N]":
  → ORDER BY [relevant_score] DESC LIMIT N
```

## 🔧 SQL Query Builder

### Base Query Template
```sql
WITH query_results AS (
  SELECT 
    c.id,
    c.title,
    c.conversation_id,
    c.source_type,
    c.occurred_at,
    m.primary_topic,
    m.business_relevance_score,
    m.actionability_score,
    m.complexity_level,
    m.estimated_tokens,
    m.total_user_messages + m.total_ai_messages as total_messages,
    m.is_starred,
    m.has_code_blocks,
    m.has_technical_content,
    m.conversation_type,
    -- Composite score for ranking
    (
      COALESCE(m.business_relevance_score, 0) * 0.3 +
      COALESCE(m.actionability_score, 0) * 0.3 +
      COALESCE(m.complexity_level, 1) / 5.0 * 0.2 +
      LEAST(COALESCE(m.estimated_tokens, 0) / 50000.0, 1.0) * 0.2
    ) as relevance_score
  FROM clean_chat_histories c
  JOIN clean_chat_histories_metadata m ON c.id = m.chat_history_id
  WHERE {CONDITIONS}
)
SELECT * FROM query_results
ORDER BY {ORDER_BY}
LIMIT {LIMIT};
```

### Logical Operators
- **AND**: "business AND epic" → Multiple WHERE conditions with AND
- **OR**: "ai OR automation" → WHERE primary_topic IN (...)
- **NOT**: "not starred", "except mobile" → WHERE NOT condition
- **Parentheses**: Support complex grouping

### Smart Defaults
```yaml
Default Limit: 20 (unless specified)
Default Order: relevance_score DESC (unless time-based query)
Time queries order: occurred_at DESC
Complexity queries order: complexity_level DESC
Token queries order: estimated_tokens DESC
```

## 📊 Response Format

### Standard Response Template
```markdown
## 🔍 Query: "{original_query}"

**Interpreted as**: {natural_language_interpretation}
**Results found**: {count}

### 📋 Top Results
| # | Title | Topic | Business | Action | Complexity | Tokens | {extra_columns} |
|---|-------|-------|----------|--------|------------|--------|-----------------|
{results_table}

### 💡 Key Insights
- **Pattern**: {observed_pattern}
- **Opportunity**: {actionable_insight}
- **Distribution**: {statistical_insight}

### 🎯 Suggested Queries
1. `{related_query_1}` - {why_relevant}
2. `{related_query_2}` - {why_relevant}
3. `{drill_down_query}` - Drill deeper into {specific_aspect}
```

### Special Formats

#### For Statistical Queries
```markdown
### 📊 Statistical Breakdown
- **Total Matches**: {count}
- **Average Complexity**: {avg_complexity}
- **Average Tokens**: {avg_tokens}
- **Business Relevance**: {avg_business}
- **Starred Percentage**: {starred_pct}%
```

#### For Empty Results
```markdown
### 🔍 No Results Found

**Suggestions**:
- Try broader terms: `{broader_query}`
- Check different timeframe: `{time_adjusted_query}`
- Explore related topic: `{adjacent_topic_query}`
```

## 🚀 Advanced Features

### Aggregation Queries
```yaml
"breakdown", "distribution", "stats", "summary":
  → GROUP BY with COUNT, AVG, SUM

"breakdown by topic":
  → SELECT primary_topic, COUNT(*), AVG(scores) GROUP BY primary_topic

"daily stats", "weekly pattern":
  → GROUP BY DATE_TRUNC('day'/'week', occurred_at)
```

### Trend Analysis
```yaml
"trend", "over time", "evolution":
  → Add time-series analysis with window functions

"improving", "getting better":
  → ORDER BY occurred_at, compare early vs recent averages

"declining", "getting worse":
  → Inverse trend analysis
```

### Export Formats
```yaml
"export", "csv", "markdown":
  → Format results for export

"full details", "everything":
  → Include all columns in results

"ids only", "list":
  → Return just conversation_ids for further processing
```

## 🛡️ Error Handling

### Query Too Broad
If results > 100:
- Add automatic LIMIT 50
- Suggest refinement: "Too many results (X). Showing top 50. Try adding filters like 'starred' or 'this month'"

### No Results
- Suggest broader search
- Show similar successful queries
- Offer to search without certain filters

### Ambiguous Input
When unclear, ask:
"Did you mean:
1. Business-related conversations (business_relevance_score)
2. Conversations about business (title/topic matching)
3. Both?"

## 💾 Query Memory

Track successful queries for pattern learning:
- Remember user's common searches
- Suggest personalized queries based on history
- Learn user's terminology preferences

## 🎯 Examples

### Example 1: Simple Business Query
**Input**: `business gold`
**Output**: Top 20 conversations with business_relevance >= 0.9

### Example 2: Complex Combination
**Input**: `epic ai conversations this month not starred`
**Output**: AI/ML topics, 50K+ tokens, last 30 days, is_starred = false

### Example 3: Statistical Query
**Input**: `breakdown by topic`
**Output**: Table showing count, avg complexity, avg tokens for each primary_topic

### Example 4: Trend Query
**Input**: `automation trend over time`
**Output**: Monthly automation conversation counts with complexity trends

### Example 5: Discovery Query
**Input**: `hidden gems`
**Output**: High-token, low-business-score conversations that might contain untapped value

## 🔄 Post-Query Actions

Always offer relevant follow-ups:
1. **Drill down**: "View conversation details for #1?"
2. **Expand**: "Include similar conversations?"
3. **Export**: "Export these results as markdown?"
4. **Analyze**: "Run statistical analysis on these results?"
5. **Star**: "Star all high-value results?"

## 🚦 Performance Optimization

- Use indexes: conversation_id, occurred_at, primary_topic
- Limit default results to 20
- Use CTEs for complex queries
- Cache common query patterns
- Pre-calculate relevance scores if needed

Remember: The goal is instant, intuitive access to knowledge. Make it feel like magic! ✨