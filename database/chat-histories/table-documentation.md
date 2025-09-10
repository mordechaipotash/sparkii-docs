#chat_histories
#supabase
#embeddings
#jsonb
# 📊 Clean Chat Histories Table - Complete Documentation

## Table Overview
**Table Name**: `public.clean_chat_histories`  
**Purpose**: Centralized storage for all AI conversation histories across multiple platforms  
**Total Records**: 11,618 conversations  
**Date Range**: May 11, 2023 - September 7, 2025  

---

## 📋 Field Documentation

### 1. **id** (uuid, NOT NULL)
- **Description**: Unique identifier for each record
- **Default**: `gen_random_uuid()` - Auto-generated UUID
- **Sample**: `3adf47cb-c720-4761-8542-78283f62d992`
- **Purpose**: Primary key for table operations

### 2. **conversation_id** (text, NOT NULL)
- **Description**: Original conversation identifier from source platform
- **Sample**: `68066cc6-8140-8005-8845-a1a3e5dab471`
- **Unique Count**: 11,618 (all unique)
- **Purpose**: Track original conversation reference

### 3. **title** (text, NULLABLE)
- **Description**: Human-readable conversation title/subject
- **Sample Values**:
  - "Breastfeeding Audiobook Recommendations"
  - "Healthy Pregnancy Book Report"
  - "Ryanair Contact Information"
  - "Python Miso Web Framework"
- **NULL Count**: 0 (all populated)
- **Purpose**: Quick identification and search capability

### 4. **source_type** (text, NULLABLE)
- **Description**: Platform where conversation originated
- **Values & Distribution**:
  | Source Type | Count | Percentage |
  |------------|-------|------------|
  | chatgpt-desktop | 5,083 | 43.75% |
  | claude-code | 3,050 | 26.25% |
  | claude-desktop | 2,618 | 22.53% |
  | gemini-ai | 867 | 7.46% |
- **Purpose**: Filter by conversation platform

### 5. **ai_source** (text, NULLABLE)
- **Description**: AI model/service used
- **Values & Distribution**:
  | AI Source | Count | Percentage |
  |-----------|-------|------------|
  | chatgpt | 5,083 | 43.75% |
  | claude | 5,668 | 48.78% |
  | gemini | 867 | 7.46% |
- **Purpose**: Analyze performance by AI model

### 6. **content** (jsonb, NULLABLE)
- **Description**: Complete conversation data in JSON format
- **Structure**: Object containing full conversation context
- **Sample Keys**: title, messages, metadata, timestamps
- **Purpose**: Store complete conversation data

### 7. **messages** (jsonb, NULLABLE)
- **Description**: Array of message objects (human/assistant exchanges)
- **Structure**:
```json
[
  {
    "role": "human",
    "content": "User's message text"
  },
  {
    "role": "assistant", 
    "content": "AI's response text"
  }
]
```
- **Purpose**: Structured message access for analysis

### 8. **total_messages** (integer, NULLABLE)
- **Description**: Count of messages in conversation
- **Statistics**:
  - **Min**: 0
  - **Max**: 964
  - **Mean**: 18.59
  - **Median**: 7
  - **Mode**: 2 (most common)
  - **Std Dev**: 33.34
- **Distribution by Platform**:
  | Platform | Avg Messages | Min | Max |
  |----------|-------------|-----|-----|
  | chatgpt-desktop | 24.77 | 1 | 964 |
  | claude-code | 20.68 | 0 | 171 |
  | claude-desktop | 9.65 | 0 | 120 |
  | gemini-ai | 2.00 | 2 | 2 |
- **Purpose**: Measure conversation depth/complexity

### 9. **occurred_at** (timestamp with timezone, NULLABLE)
- **Description**: When the conversation took place
- **Range**: 
  - **Earliest**: May 11, 2023 13:02:37 UTC
  - **Latest**: September 7, 2025 11:38:55 UTC
- **NULL Count**: 2,674 (23% missing dates)
- **Sample**: `2025-04-21 19:05:27.076935+00`
- **Purpose**: Temporal analysis and trending

### 10. **created_at** (timestamp with timezone, NULLABLE)
- **Description**: When record was inserted into database
- **Default**: `now()` - Current timestamp
- **Sample**: `2025-09-07 13:49:18.572223+00`
- **Purpose**: Database audit trail

### 11. **updated_at** (timestamp with timezone, NULLABLE)
- **Description**: Last modification timestamp
- **Default**: `now()` - Updated on changes
- **Sample**: `2025-09-07 13:49:18.572223+00`
- **Purpose**: Track data modifications

### 12. **metadata** (jsonb, NULLABLE)
- **Description**: Additional conversation metadata
- **Structure Example**:
```json
{
  "title": "Conversation Title",
  "source": "chatgpt-desktop",
  "has_code": false,
  "is_starred": null,
  "create_time": 1745251527.076935,
  "is_archived": false,
  "occurred_at": "2025-04-21T19:05:27.076935+00:00",
  "total_nodes": 15,
  "update_time": 1745251810.873201,
  "has_messages": true,
  "message_count": 11,
  "conversation_id": "68066cc6-8140-8005-8845-a1a3e5dab471",
  "import_timestamp": "2025-09-07T16:49:21.944912",
  "extracted_messages": 11,
  "first_message_preview": "Can u recommend a breast feeding book on audio",
  "average_message_length": 746
}
```
- **Purpose**: Store platform-specific metadata

### 13. **search_vector** (tsvector, NULLABLE)
- **Description**: Full-text search index
- **Type**: PostgreSQL text search vector
- **Purpose**: Enable efficient full-text search across conversations

### 14. **is_empty** (boolean, NULLABLE)
- **Description**: Flag for empty conversations
- **Distribution**:
  - **Empty**: 732 conversations (6.3%)
  - **Non-empty**: 10,886 conversations (93.7%)
- **Purpose**: Filter out empty/invalid conversations

### 15. **title_embedding** (vector, NULLABLE)
- **Description**: Vector embedding of conversation title
- **Type**: Custom vector type (likely for semantic search)
- **Dimensions**: Typically 384 or 1536 dimensions
- **Purpose**: Semantic similarity search on titles

### 16. **conversation_embedding** (vector, NULLABLE)
- **Description**: Vector embedding of full conversation
- **Type**: Custom vector type
- **Purpose**: Semantic similarity search on content

### 17. **embedding_updated_at** (timestamp with timezone, NULLABLE)
- **Description**: When embeddings were last generated
- **Sample**: `2025-09-08 17:32:23.102035+00`
- **Purpose**: Track embedding freshness

---

## 📈 Key Statistics Summary

### Conversation Volume
- **Total Conversations**: 11,618
- **Empty Conversations**: 732 (6.3%)
- **Active Conversations**: 10,886 (93.7%)

### Message Statistics
- **Average Messages per Conversation**: 18.59
- **Median Messages**: 7
- **Most Common Message Count**: 2
- **Longest Conversation**: 964 messages
- **Standard Deviation**: 33.34 (high variability)

### Platform Distribution
1. **ChatGPT Desktop**: 43.75% (most messages per conversation)
2. **Claude Combined**: 48.78% (Claude Code + Desktop)
3. **Gemini AI**: 7.46% (consistently 2 messages)

### Temporal Coverage
- **Span**: ~2.3 years of conversation history
- **Missing Dates**: 23% of records lack occurred_at timestamp

### Data Quality
- **All titles populated**: 100% coverage
- **Conversation IDs**: All unique
- **Message arrays**: Properly structured JSON
- **Embeddings**: Partially populated (ongoing process)

---

## 🔍 Common Query Patterns

### Find High-Engagement Conversations
```sql
SELECT * FROM clean_chat_histories 
WHERE total_messages > 50 
ORDER BY total_messages DESC;
```

### Search by Topic
```sql
SELECT * FROM clean_chat_histories 
WHERE title ILIKE '%API%' 
OR content::text ILIKE '%API%';
```

### Platform Comparison
```sql
SELECT ai_source, 
       AVG(total_messages) as avg_messages,
       COUNT(*) as total_convs
FROM clean_chat_histories
GROUP BY ai_source;
```

### Recent Activity
```sql
SELECT * FROM clean_chat_histories 
WHERE occurred_at > CURRENT_DATE - INTERVAL '30 days'
ORDER BY occurred_at DESC;
```

---

## 💡 Usage Notes

1. **Search Optimization**: Use `search_vector` for full-text search rather than ILIKE on content
2. **Embedding Queries**: Use vector similarity functions for semantic search
3. **NULL Handling**: ~23% of records lack `occurred_at` - use `created_at` as fallback
4. **Empty Conversations**: Filter `is_empty = false` for meaningful analysis
5. **Message Access**: Parse `messages` JSONB array for detailed conversation analysis

---

*Last Updated: September 2025*  
*Total Records: 11,618 conversations across 3 AI platforms*