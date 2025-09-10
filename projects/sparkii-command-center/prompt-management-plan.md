#wotcfy
#chat_histories
#supabase
#openrouter
#jsonb
#youtube
# 🧠 Sparkii Prompt Management System - Implementation Plan

## Executive Summary
Transform your Sparkii database (`ppxclcyrnuactlfhmchm`) into a dynamic AI orchestration hub by implementing a database-driven prompt management system similar to 
WOTCFY's architecture. This will enable centralized prompt control, version management, and multi-app AI integration across your entire ecosystem.

---

## 🎯 Vision & Goals

### Primary Objectives
1. **Centralized AI Configuration**: Single source of truth for all AI prompts across Sparkii apps
2. **Zero-Deployment Updates**: Change prompts without touching code or redeploying
3. **Multi-Model Support**: Seamlessly switch between Gemini, Claude, GPT-4, etc.
4. **Version Control**: Track prompt evolution with full audit trail
5. **Performance Optimization**: A/B test prompts, track success metrics
6. **Cost Management**: Monitor and optimize token usage across models

### Key Benefits
- **Agility**: Instant prompt optimization based on real-world results
- **Scalability**: Support unlimited apps/use cases from single database
- **Governance**: Centralized control over AI behavior across organization
- **Economics**: Optimize costs by routing to appropriate models dynamically

---

## 📊 Database Schema Design

### 1. Core Prompts Table
```sql
CREATE TABLE IF NOT EXISTS public.ai_prompts (
    -- Identity
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    prompt_id TEXT UNIQUE NOT NULL, -- Human-readable identifier (e.g., 'chat_analysis_v3')
    
    -- Versioning
    version INTEGER NOT NULL DEFAULT 1,
    parent_version_id UUID REFERENCES ai_prompts(id),
    is_active BOOLEAN DEFAULT true,
    
    -- Prompt Content
    name TEXT NOT NULL,
    description TEXT,
    system_prompt TEXT,
    user_prompt TEXT,
    assistant_prompt TEXT, -- For few-shot examples
    
    -- Model Configuration
    provider TEXT CHECK (provider IN ('openrouter', 'openai', 'anthropic', 'google', 'local')),
    model TEXT NOT NULL, -- e.g., 'google/gemini-2.5-flash-lite'
    temperature NUMERIC(3,2) DEFAULT 0.7,
    max_tokens INTEGER DEFAULT 4000,
    top_p NUMERIC(3,2) DEFAULT 0.9,
    frequency_penalty NUMERIC(3,2) DEFAULT 0.0,
    presence_penalty NUMERIC(3,2) DEFAULT 0.0,
    
    -- Output Configuration
    output_format TEXT CHECK (output_format IN ('text', 'json', 'markdown', 'code', 'structured')),
    json_schema JSONB, -- For structured output validation
    response_format JSONB, -- OpenAI-style response format
    
    -- Usage Context
    app_context TEXT[], -- ['dashboard', 'chat-analyzer', 'youtube-enricher']
    use_case TEXT NOT NULL, -- 'conversation_analysis', 'content_extraction', etc.
    tags TEXT[],
    
    -- Parameters & Variables
    parameters JSONB DEFAULT '{}', -- Dynamic variables that can be injected
    input_validators JSONB, -- Validation rules for inputs
    output_validators JSONB, -- Validation rules for outputs
    
    -- Performance & Cost
    estimated_input_tokens INTEGER,
    estimated_output_tokens INTEGER,
    estimated_cost_per_call NUMERIC(10,6),
    timeout_seconds INTEGER DEFAULT 30,
    retry_config JSONB DEFAULT '{"max_retries": 3, "backoff_multiplier": 2}',
    
    -- Quality & Testing
    test_cases JSONB DEFAULT '[]',
    success_criteria JSONB,
    quality_threshold NUMERIC(3,2) DEFAULT 0.8,
    
    -- Metadata
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    created_by UUID,
    approved_by UUID,
    approved_at TIMESTAMPTZ,
    
    -- Usage Tracking
    usage_count INTEGER DEFAULT 0,
    last_used_at TIMESTAMPTZ,
    success_rate NUMERIC(5,2),
    avg_latency_ms INTEGER,
    
    -- Feature Flags
    features JSONB DEFAULT '{}', -- {"supports_streaming": true, "supports_tools": false}
    
    -- Search
    search_vector tsvector GENERATED ALWAYS AS (
        to_tsvector('english', 
            COALESCE(name, '') || ' ' || 
            COALESCE(description, '') || ' ' || 
            COALESCE(use_case, '')
        )
    ) STORED
);

-- Indexes for performance
CREATE INDEX idx_ai_prompts_prompt_id ON ai_prompts(prompt_id) WHERE is_active = true;
CREATE INDEX idx_ai_prompts_use_case ON ai_prompts(use_case);
CREATE INDEX idx_ai_prompts_app_context ON ai_prompts USING GIN(app_context);
CREATE INDEX idx_ai_prompts_search ON ai_prompts USING GIN(search_vector);
CREATE INDEX idx_ai_prompts_active_version ON ai_prompts(prompt_id, version) WHERE is_active = true;
```

### 2. Prompt Execution Log
```sql
CREATE TABLE IF NOT EXISTS public.ai_prompt_executions (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    prompt_id UUID REFERENCES ai_prompts(id),
    
    -- Execution Context
    app_name TEXT NOT NULL,
    session_id UUID,
    user_id UUID,
    request_id TEXT UNIQUE,
    
    -- Input/Output
    input_data JSONB,
    input_tokens INTEGER,
    output_data JSONB,
    output_tokens INTEGER,
    
    -- Performance
    latency_ms INTEGER,
    status TEXT CHECK (status IN ('success', 'error', 'timeout', 'rate_limited')),
    error_message TEXT,
    
    -- Cost Tracking
    model_used TEXT,
    input_cost NUMERIC(10,6),
    output_cost NUMERIC(10,6),
    total_cost NUMERIC(10,6),
    
    -- Quality Metrics
    quality_score NUMERIC(3,2),
    user_feedback JSONB,
    auto_evaluation JSONB,
    
    -- Metadata
    executed_at TIMESTAMPTZ DEFAULT NOW(),
    metadata JSONB DEFAULT '{}'
);

-- Indexes
CREATE INDEX idx_executions_prompt_id ON ai_prompt_executions(prompt_id);
CREATE INDEX idx_executions_session ON ai_prompt_executions(session_id);
CREATE INDEX idx_executions_timestamp ON ai_prompt_executions(executed_at DESC);
```

### 3. Model Registry
```sql
CREATE TABLE IF NOT EXISTS public.ai_models (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    provider TEXT NOT NULL,
    model_id TEXT UNIQUE NOT NULL,
    display_name TEXT NOT NULL,
    
    -- Capabilities
    max_context_tokens INTEGER,
    max_output_tokens INTEGER,
    supports_vision BOOLEAN DEFAULT false,
    supports_tools BOOLEAN DEFAULT false,
    supports_streaming BOOLEAN DEFAULT false,
    supports_json_mode BOOLEAN DEFAULT false,
    
    -- Pricing (per million tokens)
    input_price_per_million NUMERIC(10,2),
    output_price_per_million NUMERIC(10,2),
    
    -- Performance Characteristics
    avg_latency_ms INTEGER,
    reliability_score NUMERIC(3,2),
    quality_score NUMERIC(3,2),
    
    -- Routing Rules
    preferred_for TEXT[], -- ['analysis', 'generation', 'extraction']
    routing_priority INTEGER DEFAULT 100,
    fallback_model_id TEXT,
    
    -- Status
    is_active BOOLEAN DEFAULT true,
    deprecated_at TIMESTAMPTZ,
    sunset_at TIMESTAMPTZ,
    
    -- Metadata
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    last_tested_at TIMESTAMPTZ,
    notes TEXT
);
```

### 4. Prompt Templates Library
```sql
CREATE TABLE IF NOT EXISTS public.ai_prompt_templates (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    category TEXT NOT NULL, -- 'analysis', 'extraction', 'generation', 'classification'
    template_name TEXT UNIQUE NOT NULL,
    description TEXT,
    
    -- Template Structure
    base_system_prompt TEXT,
    variable_mappings JSONB, -- {"{{context}}": "description of context variable"}
    example_usage JSONB,
    
    -- Best Practices
    recommended_models TEXT[],
    recommended_temperature NUMERIC(3,2),
    recommended_max_tokens INTEGER,
    
    -- Success Patterns
    proven_use_cases JSONB,
    performance_benchmarks JSONB,
    
    -- Metadata
    is_public BOOLEAN DEFAULT false,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by UUID,
    usage_count INTEGER DEFAULT 0
);
```

### 5. A/B Testing Framework
```sql
CREATE TABLE IF NOT EXISTS public.ai_prompt_experiments (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    experiment_name TEXT UNIQUE NOT NULL,
    
    -- Variants
    control_prompt_id UUID REFERENCES ai_prompts(id),
    variant_prompt_ids UUID[],
    
    -- Configuration
    traffic_allocation JSONB, -- {"control": 50, "variant_a": 25, "variant_b": 25}
    targeting_rules JSONB,
    
    -- Status
    status TEXT CHECK (status IN ('draft', 'running', 'paused', 'completed')),
    started_at TIMESTAMPTZ,
    ended_at TIMESTAMPTZ,
    
    -- Results
    results JSONB,
    winning_variant UUID,
    statistical_significance NUMERIC(3,2),
    
    -- Metadata
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by UUID
);
```

---

## 🚀 Edge Function Architecture

### 1. Universal Prompt Executor Edge Function
```typescript
// Edge Function: prompt-executor
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2';

const OPENROUTER_API_KEY = Deno.env.get('OPENROUTER_API_KEY');
const SUPABASE_URL = Deno.env.get('SUPABASE_URL');
const SUPABASE_SERVICE_ROLE_KEY = Deno.env.get('SUPABASE_SERVICE_ROLE_KEY');

const supabase = createClient(SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY);

interface PromptRequest {
  prompt_id: string;
  input_data: any;
  override_params?: {
    temperature?: number;
    max_tokens?: number;
    model?: string;
  };
  session_id?: string;
  user_id?: string;
}

async function getActivePrompt(promptId: string) {
  const { data, error } = await supabase
    .from('ai_prompts')
    .select('*')
    .eq('prompt_id', promptId)
    .eq('is_active', true)
    .order('version', { ascending: false })
    .limit(1)
    .single();
    
  if (error) throw new Error(`Prompt not found: ${promptId}`);
  return data;
}

async function executePrompt(prompt: any, input: any, overrides?: any) {
  // Build messages array
  const messages = [];
  
  if (prompt.system_prompt) {
    messages.push({
      role: 'system',
      content: interpolateVariables(prompt.system_prompt, input)
    });
  }
  
  if (prompt.user_prompt) {
    messages.push({
      role: 'user',
      content: interpolateVariables(prompt.user_prompt, input)
    });
  }
  
  // Apply overrides
  const config = {
    model: overrides?.model || prompt.model,
    temperature: overrides?.temperature || prompt.temperature,
    max_tokens: overrides?.max_tokens || prompt.max_tokens,
    messages
  };
  
  // Route to appropriate provider
  switch (prompt.provider) {
    case 'openrouter':
      return await callOpenRouter(config);
    case 'anthropic':
      return await callAnthropic(config);
    case 'google':
      return await callGoogle(config);
    default:
      throw new Error(`Unsupported provider: ${prompt.provider}`);
  }
}

async function callOpenRouter(config: any) {
  const response = await fetch('https://openrouter.ai/api/v1/chat/completions', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${OPENROUTER_API_KEY}`,
      'Content-Type': 'application/json',
      'HTTP-Referer': 'https://sparkii.com',
      'X-Title': 'Sparkii AI Platform'
    },
    body: JSON.stringify(config)
  });
  
  if (!response.ok) {
    throw new Error(`OpenRouter API error: ${response.status}`);
  }
  
  return await response.json();
}

function interpolateVariables(template: string, data: any): string {
  return template.replace(/\{\{(\w+)\}\}/g, (match, key) => {
    return data[key] || match;
  });
}

// Main handler
serve(async (req) => {
  try {
    const payload: PromptRequest = await req.json();
    
    // Get active prompt configuration
    const prompt = await getActivePrompt(payload.prompt_id);
    
    // Execute prompt with input data
    const startTime = Date.now();
    const result = await executePrompt(
      prompt, 
      payload.input_data,
      payload.override_params
    );
    const latency = Date.now() - startTime;
    
    // Log execution
    await supabase.from('ai_prompt_executions').insert({
      prompt_id: prompt.id,
      app_name: 'edge-function',
      session_id: payload.session_id,
      user_id: payload.user_id,
      input_data: payload.input_data,
      output_data: result,
      latency_ms: latency,
      status: 'success',
      model_used: prompt.model,
      executed_at: new Date().toISOString()
    });
    
    // Update usage stats
    await supabase.rpc('increment_prompt_usage', {
      prompt_id: prompt.id,
      latency_ms: latency
    });
    
    return new Response(JSON.stringify({
      success: true,
      data: result,
      prompt_version: prompt.version,
      latency_ms: latency
    }), {
      headers: { 'Content-Type': 'application/json' }
    });
    
  } catch (error) {
    console.error('Error:', error);
    return new Response(JSON.stringify({
      success: false,
      error: error.message
    }), {
      status: 500,
      headers: { 'Content-Type': 'application/json' }
    });
  }
});
```

### 2. Specialized Edge Functions

#### Chat Analysis Edge Function
```typescript
// Edge Function: analyze-chat
async function analyzeChat(chatId: string) {
  // Fetch chat data
  const { data: chat } = await supabase
    .from('clean_chat_histories')
    .select('*')
    .eq('id', chatId)
    .single();
    
  // Execute analysis prompt
  const result = await fetch(`${EDGE_FUNCTION_URL}/prompt-executor`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      prompt_id: 'chat_analysis_v3',
      input_data: {
        messages: chat.messages,
        metadata: chat.metadata
      }
    })
  });
  
  // Store analysis results
  await supabase.from('mordelens').insert({
    clean_chat_id: chatId,
    lenses: result.data,
    computed_at: new Date().toISOString()
  });
}
```

#### Content Enrichment Edge Function
```typescript
// Edge Function: enrich-content
async function enrichContent(contentId: string) {
  const { data: content } = await supabase
    .from('raw_content_unified')
    .select('*')
    .eq('id', contentId)
    .single();
    
  // Route to appropriate enrichment prompt based on source type
  const promptMap = {
    'youtube': 'youtube_transcript_analysis',
    'conversation': 'conversation_enrichment',
    'document': 'document_extraction'
  };
  
  const result = await fetch(`${EDGE_FUNCTION_URL}/prompt-executor`, {
    method: 'POST',
    body: JSON.stringify({
      prompt_id: promptMap[content.source_type],
      input_data: content
    })
  });
  
  // Update content with enrichment
  await supabase
    .from('raw_content_unified')
    .update({
      metadata: { ...content.metadata, enrichment: result.data },
      processing_status: 'processed',
      updated_at: new Date().toISOString()
    })
    .eq('id', contentId);
}
```

---

## 🔄 Integration Patterns

### 1. Client SDK (TypeScript/JavaScript)
```typescript
// lib/sparkii-ai-client.ts
export class SparkiiAI {
  private supabase: SupabaseClient;
  private edgeFunctionUrl: string;
  
  constructor(supabaseUrl: string, supabaseKey: string) {
    this.supabase = createClient(supabaseUrl, supabaseKey);
    this.edgeFunctionUrl = `${supabaseUrl}/functions/v1`;
  }
  
  async executePrompt(
    promptId: string, 
    inputData: any,
    options?: {
      sessionId?: string;
      userId?: string;
      overrides?: any;
    }
  ) {
    const response = await fetch(`${this.edgeFunctionUrl}/prompt-executor`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.supabase.auth.session?.access_token}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        prompt_id: promptId,
        input_data: inputData,
        session_id: options?.sessionId,
        user_id: options?.userId,
        override_params: options?.overrides
      })
    });
    
    if (!response.ok) {
      throw new Error(`Prompt execution failed: ${response.statusText}`);
    }
    
    return response.json();
  }
  
  // Convenience methods for common operations
  async analyzeChat(chatId: string) {
    return this.executePrompt('chat_analysis_v3', { chat_id: chatId });
  }
  
  async extractFromDocument(documentUrl: string) {
    return this.executePrompt('document_extraction', { url: documentUrl });
  }
  
  async enrichYouTubeTranscript(transcript: string) {
    return this.executePrompt('youtube_enrichment', { transcript });
  }
}

// Usage in your apps
const ai = new SparkiiAI(SUPABASE_URL, SUPABASE_ANON_KEY);

// In your dashboard
const analysis = await ai.analyzeChat(chatId);

// In your YouTube pipeline
const enriched = await ai.enrichYouTubeTranscript(transcript);
```

### 2. React Hook Pattern
```typescript
// hooks/use-ai-prompt.ts
export function useAIPrompt(promptId: string) {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);
  
  const execute = useCallback(async (inputData: any) => {
    setLoading(true);
    setError(null);
    
    try {
      const result = await ai.executePrompt(promptId, inputData);
      return result;
    } catch (err) {
      setError(err as Error);
      throw err;
    } finally {
      setLoading(false);
    }
  }, [promptId]);
  
  return { execute, loading, error };
}

// Usage in components
function ChatAnalyzer({ chatId }: { chatId: string }) {
  const { execute, loading, error } = useAIPrompt('chat_analysis_v3');
  
  const handleAnalyze = async () => {
    const result = await execute({ chat_id: chatId });
    console.log('Analysis:', result);
  };
  
  return (
    <Button onClick={handleAnalyze} disabled={loading}>
      {loading ? 'Analyzing...' : 'Analyze Chat'}
    </Button>
  );
}
```

### 3. Python Integration
```python
# sparkii_ai.py
import asyncio
from typing import Any, Dict, Optional
import aiohttp
from supabase import create_client, Client

class SparkiiAI:
    def __init__(self, supabase_url: str, supabase_key: str):
        self.supabase: Client = create_client(supabase_url, supabase_key)
        self.edge_function_url = f"{supabase_url}/functions/v1"
        
    async def execute_prompt(
        self, 
        prompt_id: str,
        input_data: Dict[str, Any],
        session_id: Optional[str] = None,
        user_id: Optional[str] = None,
        overrides: Optional[Dict[str, Any]] = None
    ) -> Dict[str, Any]:
        async with aiohttp.ClientSession() as session:
            async with session.post(
                f"{self.edge_function_url}/prompt-executor",
                json={
                    "prompt_id": prompt_id,
                    "input_data": input_data,
                    "session_id": session_id,
                    "user_id": user_id,
                    "override_params": overrides
                },
                headers={
                    "Authorization": f"Bearer {self.supabase.auth.get_session().access_token}",
                    "Content-Type": "application/json"
                }
            ) as response:
                return await response.json()
    
    async def batch_analyze_chats(self, chat_ids: list[str]):
        """Analyze multiple chats in parallel"""
        tasks = [
            self.execute_prompt('chat_analysis_v3', {'chat_id': chat_id})
            for chat_id in chat_ids
        ]
        return await asyncio.gather(*tasks)

# Usage in your Python scripts
ai = SparkiiAI(SUPABASE_URL, SUPABASE_KEY)

# Single analysis
result = await ai.execute_prompt('chat_analysis_v3', {'chat_id': 'abc-123'})

# Batch processing
results = await ai.batch_analyze_chats(['chat1', 'chat2', 'chat3'])
```

---

## 🎮 Admin Dashboard UI

### Prompt Management Interface
```typescript
// app/admin/prompts/page.tsx
export default function PromptsAdmin() {
  return (
    <div className="space-y-6">
      <PromptsList />
      <PromptEditor />
      <PromptTester />
      <PromptAnalytics />
      <ABTestManager />
    </div>
  );
}

// Components needed:
// 1. PromptsList - Browse all prompts with search/filter
// 2. PromptEditor - Edit prompts with syntax highlighting
// 3. PromptTester - Test prompts with real data
// 4. PromptAnalytics - View usage, costs, performance
// 5. ABTestManager - Create and monitor experiments
```

---

## 📈 Implementation Roadmap

### Phase 1: Foundation (Week 1)
1. **Database Setup**
   ```sql
   -- Run migration to create all tables
   mcp: supabase.apply_migration
   params: {
     project_id: "ppxclcyrnuactlfhmchm",
     name: "prompt_management_system",
     query: "<full schema SQL>"
   }
   ```

2. **Seed Initial Prompts**
   ```sql
   -- Import existing prompts from your apps
   INSERT INTO ai_prompts (prompt_id, name, system_prompt, user_prompt, model)
   VALUES 
   ('chat_analysis_v1', 'Chat Analysis', '...', '...', 'google/gemini-2.5-flash-lite'),
   ('youtube_enrichment_v1', 'YouTube Enrichment', '...', '...', 'google/gemini-2.5-flash'),
   ('document_extraction_v1', 'Document Extraction', '...', '...', 'anthropic/claude-3-haiku');
   ```

3. **Deploy Core Edge Function**
   ```typescript
   mcp: supabase.deploy_edge_function
   params: {
     project_id: "ppxclcyrnuactlfhmchm",
     name: "prompt-executor",
     files: [{ name: "index.ts", content: "<edge function code>" }]
   }
   ```

### Phase 2: Integration (Week 2)
1. **Update Existing Apps**
   - Replace hardcoded prompts with database calls
   - Implement SparkiiAI client SDK
   - Add error handling and fallbacks

2. **Create Admin Dashboard**
   - Build prompt management UI
   - Add testing interface
   - Implement analytics views

3. **Set Up Monitoring**
   - Track prompt usage and performance
   - Monitor costs across models
   - Set up alerts for failures

### Phase 3: Optimization (Week 3)
1. **A/B Testing**
   - Create experiments for critical prompts
   - Implement traffic splitting
   - Analyze results and optimize

2. **Performance Tuning**
   - Add caching for frequently used prompts
   - Implement connection pooling
   - Optimize database queries

3. **Advanced Features**
   - Prompt chaining (multi-step workflows)
   - Conditional routing based on input
   - Automatic fallback to cheaper models

### Phase 4: Scale (Week 4)
1. **Multi-App Rollout**
   - Migrate all apps to use central system
   - Standardize prompt naming conventions
   - Document best practices

2. **Team Enablement**
   - Create prompt templates library
   - Build self-service interfaces
   - Train team on prompt engineering

3. **Production Hardening**
   - Add rate limiting
   - Implement security controls
   - Set up backup and recovery

---

## 🔐 Security Considerations

### Access Control
```sql
-- Row Level Security for prompts
ALTER TABLE ai_prompts ENABLE ROW LEVEL SECURITY;

-- Only admins can modify prompts
CREATE POLICY "Admin write access" ON ai_prompts
  FOR ALL TO authenticated
  USING (auth.jwt() ->> 'role' = 'admin');

-- All authenticated users can read active prompts
CREATE POLICY "Read active prompts" ON ai_prompts
  FOR SELECT TO authenticated
  USING (is_active = true);
```

### API Key Management
```sql
-- Secure storage for API keys
CREATE TABLE ai_api_keys (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  provider TEXT NOT NULL,
  key_name TEXT NOT NULL,
  encrypted_key TEXT NOT NULL, -- Use Supabase Vault
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Use Supabase Vault for encryption
INSERT INTO vault.secrets (name, secret)
VALUES ('openrouter_api_key', 'sk-or-...');
```

---

## 💰 Cost Optimization Strategies

### 1. Intelligent Model Routing
```typescript
// Route to cheapest model that meets requirements
function selectOptimalModel(requirements: ModelRequirements) {
  const models = await getActiveModels();
  
  return models
    .filter(m => meetsRequirements(m, requirements))
    .sort((a, b) => a.input_price_per_million - b.input_price_per_million)[0];
}
```

### 2. Prompt Compression
```typescript
// Automatically compress prompts to reduce tokens
function compressPrompt(prompt: string): string {
  return prompt
    .replace(/\s+/g, ' ')  // Remove extra whitespace
    .replace(/\n{3,}/g, '\n\n')  // Limit newlines
    .trim();
}
```

### 3. Response Caching
```sql
-- Cache frequently requested AI responses
CREATE TABLE ai_response_cache (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  cache_key TEXT UNIQUE NOT NULL, -- Hash of prompt + input
  response JSONB NOT NULL,
  expires_at TIMESTAMPTZ NOT NULL,
  hit_count INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 🎯 Success Metrics

### Key Performance Indicators
1. **Prompt Performance**
   - Average latency per prompt
   - Success rate by model
   - Cost per execution

2. **Business Impact**
   - Time saved on prompt updates
   - Reduction in deployment frequency
   - Improvement in AI output quality

3. **System Health**
   - Edge Function uptime
   - Database query performance
   - API rate limit utilization

### Monitoring Dashboard
```sql
-- Real-time metrics view
CREATE VIEW ai_system_metrics AS
SELECT 
  DATE_TRUNC('hour', executed_at) as hour,
  COUNT(*) as total_executions,
  AVG(latency_ms) as avg_latency,
  SUM(total_cost) as total_cost,
  COUNT(DISTINCT prompt_id) as unique_prompts,
  COUNT(DISTINCT user_id) as unique_users
FROM ai_prompt_executions
WHERE executed_at > NOW() - INTERVAL '24 hours'
GROUP BY 1
ORDER BY 1 DESC;
```

---

## 🚦 Quick Start Commands

### Setup Database
```bash
# 1. Create migration file
mcp: supabase.apply_migration
params: {
  project_id: "ppxclcyrnuactlfhmchm",
  name: "create_prompt_management_system",
  query: "<full schema from this document>"
}

# 2. Deploy Edge Function
mcp: supabase.deploy_edge_function
params: {
  project_id: "ppxclcyrnuactlfhmchm",
  name: "prompt-executor",
  files: [{ name: "index.ts", content: "<edge function code>" }]
}

# 3. Test the system
mcp: supabase.execute_sql
params: {
  project_id: "ppxclcyrnuactlfhmchm",
  query: "INSERT INTO ai_prompts (prompt_id, name, ...) VALUES (...);"
}
```

---

## 📚 Best Practices

### Prompt Engineering
1. **Version Everything**: Never modify prompts in place, create new versions
2. **Test Rigorously**: Always test prompts with edge cases before activation
3. **Monitor Continuously**: Track performance and iterate based on data
4. **Document Thoroughly**: Include examples and expected outputs

### System Design
1. **Fail Gracefully**: Always have fallback prompts and models
2. **Cache Aggressively**: Cache both prompts and responses where appropriate
3. **Log Everything**: Comprehensive logging for debugging and optimization
4. **Secure by Default**: Encrypt sensitive data, use RLS, validate inputs

### Operations
1. **Gradual Rollouts**: Use A/B testing for major changes
2. **Regular Reviews**: Weekly prompt performance reviews
3. **Cost Monitoring**: Daily cost tracking and alerts
4. **Team Training**: Regular prompt engineering workshops

---

## 🎉 Expected Outcomes

### Immediate Benefits (Week 1)
- Centralized prompt management
- Zero-deployment updates
- Basic usage tracking

### Short-term Wins (Month 1)
- 50% reduction in prompt update time
- 30% cost savings through model optimization
- Improved prompt quality through A/B testing

### Long-term Value (Quarter 1)
- Complete AI governance across organization
- 70% reduction in AI-related deployments
- Data-driven prompt optimization culture
- Scalable AI infrastructure for future apps

---

## 🤝 Next Steps

1. **Review & Approve**: Review this plan with your team
2. **Prioritize Features**: Decide which features are must-have vs nice-to-have
3. **Set Up Environment**: Configure API keys and database
4. **Begin Implementation**: Start with Phase 1 database setup
5. **Iterate & Improve**: Continuously refine based on usage

---

*This plan provides a production-ready, scalable prompt management system that will transform how Sparkii leverages AI across all applications. The database-driven approach ensures maximum flexibility while maintaining governance and cost control.*