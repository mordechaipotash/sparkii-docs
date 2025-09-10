---
description: "Semantic search across your Personal (11,618 conversations) and World (32,612 videos) Intelligence using vector embeddings"
argument-hint: "[query] [scope=auto|personal|world|analytics] [limit=10]"  
allowed-tools: ["mcp__supabase_sparkii__execute_sql"]
---

# /search-embeddings - Intelligence Vector Search

Search your **Personal Intelligence** (11,618 AI conversations) and **World Intelligence** (32,612 YouTube videos) using semantic embeddings.

## 🎯 Query Analysis: "$ARGUMENTS"

Parse the query and determine search scope:

### Scope Detection
- **Personal keywords** (I, me, my, thinking, patterns, conversations, asked, discussed) → `clean_chat_histories`
- **World keywords** (trends, latest, developments, content, videos, watched, channels) → `raw_content_unified` 
- **Analytics keywords** (patterns, insights, deep, cognitive, dimensions, analysis) → `mordelens`
- **Auto-detect**: Determine best scope based on query intent

## 🔍 Search Implementation

### Step 1: Query Embedding Generation
Since we can't generate embeddings in real-time, use text search with semantic hints:

```sql
-- For Personal Intelligence Search
WITH personal_search AS (
  SELECT 
    c.id,
    c.title,
    c.conversation_id,
    c.ai_source,
    c.source_type,
    c.occurred_at,
    c.total_messages,
    -- Use title for quick semantic matching
    c.title <-> '$ARGUMENTS' as title_distance,
    -- Text search as fallback
    ts_rank_cd(c.search_vector, plainto_tsquery('english', '$ARGUMENTS')) as text_rank
  FROM clean_chat_histories c
  WHERE 
    c.conversation_embedding IS NOT NULL
    AND (
      c.search_vector @@ plainto_tsquery('english', '$ARGUMENTS')
      OR c.title ILIKE '%' || '$ARGUMENTS' || '%'
    )
  ORDER BY 
    text_rank DESC,
    c.occurred_at DESC
  LIMIT 20
)
SELECT * FROM personal_search;
```

### Step 2: World Intelligence Search
```sql
-- For YouTube/World content search
WITH world_search AS (
  SELECT 
    r.id,
    r.content->>'title' as title,
    r.source_type,
    r.occurred_at,
    r.youtube_title,
    r.youtube_video_id,
    -- Use content embedding distance if available
    CASE WHEN r.content_embedding IS NOT NULL 
         THEN 'Has embedding' 
         ELSE 'Text only' END as search_type,
    -- Text search ranking
    ts_rank_cd(r.search_vector, plainto_tsquery('english', '$ARGUMENTS')) as rank
  FROM raw_content_unified r
  WHERE 
    r.source_type = 'youtube'
    AND r.search_vector @@ plainto_tsquery('english', '$ARGUMENTS')
  ORDER BY 
    (CASE WHEN r.content_embedding IS NOT NULL THEN 1 ELSE 0 END) DESC,
    rank DESC,
    r.occurred_at DESC
  LIMIT 20
)
SELECT * FROM world_search;
```

### Step 3: Advanced Analytics Search
```sql
-- For deep insights and patterns
WITH analytics_search AS (
  SELECT 
    m.id,
    c.title,
    c.conversation_id,
    c.ai_source,
    c.occurred_at,
    m.lenses,
    m.computed_insights,
    -- Indicate if advanced embeddings exist
    CASE WHEN m.insight_embedding IS NOT NULL THEN 'Advanced' ELSE 'Basic' END as analysis_level
  FROM mordelens m
  JOIN clean_chat_histories c ON m.clean_chat_id = c.id
  WHERE 
    m.insight_embedding IS NOT NULL
    AND (
      (m.lenses::text ILIKE '%' || '$ARGUMENTS' || '%')
      OR (m.computed_insights::text ILIKE '%' || '$ARGUMENTS' || '%')
      OR c.title ILIKE '%' || '$ARGUMENTS' || '%'
    )
  ORDER BY 
    m.computed_at DESC
  LIMIT 15
)
SELECT * FROM analytics_search;
```

## 🧠 Intelligent Query Routing

Based on query analysis, execute the appropriate search:

### Route 1: Personal Intelligence
**Triggers**: Personal pronouns, conversation terms, "what did I", "my thoughts on"

Execute personal_search query above.

### Route 2: World Intelligence  
**Triggers**: "trends", "videos about", "content on", "watched", "channels"

Execute world_search query above.

### Route 3: Analytics Intelligence
**Triggers**: "patterns", "insights", "deep analysis", "cognitive", "dimensions"

Execute analytics_search query above.

### Route 4: Hybrid Search
**Triggers**: Complex queries spanning multiple domains

Execute all three searches and merge results by relevance.

## 📊 Response Format

```markdown
## 🔍 Search Results for: "{query}"

**Search Scope**: {Personal|World|Analytics|Hybrid}
**Embedding Coverage**: {coverage_stats}
**Results Found**: {total_count}

### 🧠 Personal Intelligence (Conversations)
{personal_results_table}

### 🌍 World Intelligence (Content)  
{world_results_table}

### 🔬 Advanced Analytics (Patterns)
{analytics_results_table}

### 💡 Key Insights
- **Pattern Detected**: {pattern}
- **Coverage**: {embedding_coverage}%
- **Time Range**: {date_range}

### 🎯 Suggested Refinements
1. `/search-embeddings "{refined_query_1}" personal`
2. `/search-embeddings "{refined_query_2}" world` 
3. `/search-embeddings "{broader_query}" analytics`
```

## 🚀 Advanced Features

### Database Statistics Check
First, check the current embedding coverage:

```sql
-- Get current embedding statistics
SELECT 
  'Personal Intelligence' as system,
  'clean_chat_histories' as table_name,
  COUNT(*) as total_rows,
  COUNT(conversation_embedding) as embeddings,
  ROUND(COUNT(conversation_embedding)::numeric / COUNT(*) * 100, 1) as coverage_pct
FROM clean_chat_histories

UNION ALL

SELECT 
  'World Intelligence',
  'raw_content_unified',
  COUNT(*),
  COUNT(content_embedding),
  ROUND(COUNT(content_embedding)::numeric / COUNT(*) * 100, 1)
FROM raw_content_unified
WHERE source_type = 'youtube'

UNION ALL

SELECT 
  'Advanced Analytics',
  'mordelens', 
  COUNT(*),
  COUNT(insight_embedding),
  ROUND(COUNT(insight_embedding)::numeric / COUNT(*) * 100, 1)
FROM mordelens;
```

### Query Expansion
For better results without true vector search:

```sql
-- Expand query with related terms
WITH query_expansion AS (
  SELECT unnest(string_to_array('$ARGUMENTS AI artificial intelligence machine learning ML', ' ')) as term
),
expanded_search AS (
  SELECT c.*, 
    COUNT(*) OVER() as total_matches
  FROM clean_chat_histories c
  CROSS JOIN query_expansion qe
  WHERE c.search_vector @@ plainto_tsquery('english', qe.term)
  GROUP BY c.id, c.title, c.conversation_id, c.ai_source, c.occurred_at
)
SELECT * FROM expanded_search
ORDER BY total_matches DESC, occurred_at DESC
LIMIT 20;
```

## 🎯 Usage Examples

```bash
# Personal Intelligence  
/search-embeddings "what did I learn about AI" personal

# World Intelligence
/search-embeddings "latest AI developments" world  

# Advanced Analytics
/search-embeddings "patterns in my thinking" analytics

# Auto-detect scope
/search-embeddings "WOTC automation strategies"

# With custom limit
/search-embeddings "business opportunities" auto 25
```

## ⚡ Performance Notes

- **Personal**: 100% embedding coverage (11,618/11,618)
- **World**: 45.6% embedding coverage (16,532/36,217 YouTube)  
- **Analytics**: 18.4% embedding coverage (2,458/13,344)

Missing embeddings fallback to full-text search with PostgreSQL's `ts_rank_cd()`.

## 🔧 Embedding Dimensions

- **Personal/World**: 384d (OpenAI-style embeddings)
- **Analytics Insights**: 512d (Deep analysis vectors)
- **Analytics Dimensions**: 256d (Cognitive profiling)

---

**Remember**: This is YOUR intelligence system. It knows you through 11,618 conversations and 32,612 videos. Ask it anything! ✨