# /db-edge-functions - Write Supabase Edge Functions

## Expert: Deno/TypeScript Edge Function Developer

Create performant Edge Functions for Supabase using Deno runtime.

## Command Usage

```bash
/db-edge-functions <function_name>    # Create new edge function
/db-edge-functions --webhook          # Create webhook handler
/db-edge-functions --cron             # Create scheduled function
/db-edge-functions --auth             # Create auth hook
/db-edge-functions --storage          # Create storage trigger
/db-edge-functions --openai           # Create AI integration
```

## Edge Function Structure

### Basic Function Template
```typescript
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

const corsHeaders = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Headers': 'authorization, x-client-info, apikey, content-type',
}

serve(async (req) => {
  // Handle CORS
  if (req.method === 'OPTIONS') {
    return new Response('ok', { headers: corsHeaders })
  }

  try {
    // Create Supabase client
    const supabaseClient = createClient(
      Deno.env.get('SUPABASE_URL') ?? '',
      Deno.env.get('SUPABASE_ANON_KEY') ?? '',
      {
        global: {
          headers: { Authorization: req.headers.get('Authorization')! },
        },
      }
    )

    // Get user from JWT
    const {
      data: { user },
    } = await supabaseClient.auth.getUser()

    // Your function logic here
    const { data, error } = await processRequest(req, supabaseClient, user)

    if (error) throw error

    return new Response(
      JSON.stringify(data),
      {
        headers: { ...corsHeaders, 'Content-Type': 'application/json' },
        status: 200,
      }
    )
  } catch (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      {
        headers: { ...corsHeaders, 'Content-Type': 'application/json' },
        status: 400,
      }
    )
  }
})
```

### Webhook Handler
```typescript
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'
import { crypto } from 'https://deno.land/std@0.168.0/crypto/mod.ts'

serve(async (req) => {
  const signature = req.headers.get('x-webhook-signature')
  const body = await req.text()

  // Verify webhook signature
  const secret = Deno.env.get('WEBHOOK_SECRET')!
  const expectedSignature = await crypto.subtle.digest(
    'SHA-256',
    new TextEncoder().encode(secret + body)
  )

  if (!verifySignature(signature, expectedSignature)) {
    return new Response('Unauthorized', { status: 401 })
  }

  const payload = JSON.parse(body)

  // Process webhook
  const supabase = createClient(
    Deno.env.get('SUPABASE_URL')!,
    Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
  )

  // Handle different webhook types
  switch (payload.type) {
    case 'payment.success':
      await handlePaymentSuccess(payload, supabase)
      break
    case 'user.created':
      await handleUserCreated(payload, supabase)
      break
    default:
      console.log('Unknown webhook type:', payload.type)
  }

  return new Response('OK', { status: 200 })
})
```

### Scheduled Cron Function
```typescript
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

serve(async (req) => {
  // Verify cron secret
  const authHeader = req.headers.get('Authorization')
  if (authHeader !== `Bearer ${Deno.env.get('CRON_SECRET')}`) {
    return new Response('Unauthorized', { status: 401 })
  }

  const supabase = createClient(
    Deno.env.get('SUPABASE_URL')!,
    Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
  )

  // Perform scheduled tasks
  try {
    // Clean up old records
    const { error: cleanupError } = await supabase
      .from('logs')
      .delete()
      .lt('created_at', new Date(Date.now() - 30 * 24 * 60 * 60 * 1000).toISOString())

    // Send daily reports
    await sendDailyReports(supabase)

    // Update statistics
    await updateStatistics(supabase)

    return new Response('Cron job completed', { status: 200 })
  } catch (error) {
    console.error('Cron job failed:', error)
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500 }
    )
  }
})
```

### OpenAI Integration
```typescript
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { Configuration, OpenAIApi } from 'https://esm.sh/openai@3.2.1'

const configuration = new Configuration({
  apiKey: Deno.env.get('OPENAI_API_KEY'),
})
const openai = new OpenAIApi(configuration)

serve(async (req) => {
  const { prompt, model = 'gpt-3.5-turbo' } = await req.json()

  try {
    const completion = await openai.createChatCompletion({
      model,
      messages: [
        { role: 'system', content: 'You are a helpful assistant.' },
        { role: 'user', content: prompt }
      ],
      temperature: 0.7,
      max_tokens: 500,
    })

    const response = completion.data.choices[0].message?.content

    // Store in database
    const supabase = createClient(
      Deno.env.get('SUPABASE_URL')!,
      Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
    )

    await supabase.from('ai_responses').insert({
      prompt,
      response,
      model,
      created_at: new Date().toISOString(),
    })

    return new Response(
      JSON.stringify({ response }),
      { headers: { 'Content-Type': 'application/json' } }
    )
  } catch (error) {
    return new Response(
      JSON.stringify({ error: error.message }),
      { status: 500 }
    )
  }
})
```

### Database Triggers
```typescript
// Function called by database trigger
serve(async (req) => {
  const { type, table, record, old_record } = await req.json()

  const supabase = createClient(
    Deno.env.get('SUPABASE_URL')!,
    Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
  )

  switch (type) {
    case 'INSERT':
      if (table === 'users') {
        await sendWelcomeEmail(record)
        await createUserProfile(record, supabase)
      }
      break

    case 'UPDATE':
      if (table === 'orders' && record.status === 'completed') {
        await processCompletedOrder(record, supabase)
      }
      break

    case 'DELETE':
      await logDeletion(table, old_record, supabase)
      break
  }

  return new Response('OK', { status: 200 })
})
```

## Environment Variables

```typescript
// Always available
Deno.env.get('SUPABASE_URL')
Deno.env.get('SUPABASE_ANON_KEY')
Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')

// Custom secrets (set via Supabase dashboard)
Deno.env.get('OPENAI_API_KEY')
Deno.env.get('STRIPE_SECRET_KEY')
Deno.env.get('WEBHOOK_SECRET')
```

## Testing Edge Functions

### Local Development
```bash
# Serve function locally
supabase functions serve function-name --env-file .env.local

# Test with curl
curl -i --location --request POST \
  'http://localhost:54321/functions/v1/function-name' \
  --header 'Authorization: Bearer YOUR_ANON_KEY' \
  --header 'Content-Type: application/json' \
  --data '{"name":"Functions"}'
```

### Deploy to Supabase
```bash
# Deploy function
supabase functions deploy function-name

# Set secrets
supabase secrets set OPENAI_API_KEY=your-key

# View logs
supabase functions logs function-name
```

## MCP Integration

```bash
# Deploy via MCP
mcp: supabase.deploy_edge_function
  params: {
    name: "process-webhook",
    files: [{
      name: "index.ts",
      content: "<function_code>"
    }]
  }

# Get function details
mcp: supabase.get_edge_function
  params: { function_slug: "process-webhook" }

# View logs
mcp: supabase.get_logs
  params: { service: "edge-function" }
```

## Best Practices

1. **Always handle CORS** for browser requests
2. **Validate input** - Never trust client data
3. **Use service role carefully** - Only when needed
4. **Implement rate limiting** - Prevent abuse
5. **Log errors** - But don't expose sensitive data
6. **Set timeouts** - Functions timeout after 150s
7. **Optimize cold starts** - Keep imports minimal

## Common Patterns

### Rate Limiting
```typescript
const rateLimit = new Map()

function checkRateLimit(userId: string): boolean {
  const now = Date.now()
  const userLimits = rateLimit.get(userId) || []
  
  // Remove old entries (older than 1 minute)
  const recentRequests = userLimits.filter(
    (timestamp: number) => now - timestamp < 60000
  )
  
  if (recentRequests.length >= 10) {
    return false // Rate limit exceeded
  }
  
  recentRequests.push(now)
  rateLimit.set(userId, recentRequests)
  return true
}
```

### Error Handling
```typescript
class EdgeFunctionError extends Error {
  constructor(
    message: string,
    public statusCode: number = 400,
    public code?: string
  ) {
    super(message)
  }
}

try {
  // Your code
} catch (error) {
  if (error instanceof EdgeFunctionError) {
    return new Response(
      JSON.stringify({ 
        error: error.message,
        code: error.code 
      }),
      { status: error.statusCode }
    )
  }
  // Generic error
  return new Response(
    JSON.stringify({ error: 'Internal server error' }),
    { status: 500 }
  )
}
```

---

Last Updated: 2025-01-10