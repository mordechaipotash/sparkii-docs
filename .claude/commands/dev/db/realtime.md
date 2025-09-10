# /db-realtime - Implement Supabase Realtime

## Expert: Realtime Systems Architect

Implement real-time subscriptions and broadcasts using Supabase Realtime.

## Command Usage

```bash
/db-realtime <table_name>         # Set up realtime for table
/db-realtime --presence           # Implement presence tracking
/db-realtime --broadcast          # Create broadcast channel
/db-realtime --postgres-changes   # Subscribe to DB changes
/db-realtime --optimistic         # Optimistic UI updates
```

## Realtime Patterns

### 1. Table Changes Subscription
```typescript
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(url, key)

// Subscribe to all changes
const channel = supabase
  .channel('table-changes')
  .on(
    'postgres_changes',
    {
      event: '*', // 'INSERT' | 'UPDATE' | 'DELETE'
      schema: 'public',
      table: 'messages'
    },
    (payload) => {
      console.log('Change received!', payload)
      handleTableChange(payload)
    }
  )
  .subscribe()

// Filtered subscription
const filteredChannel = supabase
  .channel('user-messages')
  .on(
    'postgres_changes',
    {
      event: 'INSERT',
      schema: 'public',
      table: 'messages',
      filter: `user_id=eq.${userId}`
    },
    (payload) => handleNewMessage(payload.new)
  )
  .subscribe()

// Cleanup
channel.unsubscribe()
```

### 2. Presence (Who's Online)
```typescript
// Track online users
const presenceChannel = supabase.channel('online-users')

// Track yourself
presenceChannel
  .on('presence', { event: 'sync' }, () => {
    const state = presenceChannel.presenceState()
    console.log('Online users:', state)
    updateOnlineUsers(state)
  })
  .on('presence', { event: 'join' }, ({ key, newPresences }) => {
    console.log('User joined:', key, newPresences)
  })
  .on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
    console.log('User left:', key, leftPresences)
  })
  .subscribe(async (status) => {
    if (status === 'SUBSCRIBED') {
      await presenceChannel.track({
        user_id: userId,
        username: username,
        online_at: new Date().toISOString()
      })
    }
  })

// Update your status
await presenceChannel.track({ status: 'active' })

// Leave
await presenceChannel.untrack()
```

### 3. Broadcast (Pub/Sub)
```typescript
// Create broadcast channel
const broadcastChannel = supabase.channel('room-1')

// Listen for messages
broadcastChannel
  .on(
    'broadcast',
    { event: 'message' },
    (payload) => {
      console.log('Broadcast received:', payload)
      displayMessage(payload.payload)
    }
  )
  .subscribe()

// Send broadcast
broadcastChannel.send({
  type: 'broadcast',
  event: 'message',
  payload: {
    user: username,
    message: 'Hello everyone!',
    timestamp: Date.now()
  }
})

// Typing indicators
broadcastChannel
  .on('broadcast', { event: 'typing' }, ({ payload }) => {
    showTypingIndicator(payload.user)
  })
  .subscribe()

// Send typing event
broadcastChannel.send({
  type: 'broadcast',
  event: 'typing',
  payload: { user: username }
})
```

### 4. React Hook Implementation
```typescript
import { useEffect, useState } from 'react'
import { RealtimeChannel } from '@supabase/supabase-js'

export function useRealtimeTable<T>(
  table: string,
  filter?: string
) {
  const [data, setData] = useState<T[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    let channel: RealtimeChannel

    async function setupRealtime() {
      try {
        // Initial data fetch
        let query = supabase.from(table).select('*')
        if (filter) {
          query = query.filter(filter)
        }
        
        const { data: initialData, error } = await query
        if (error) throw error
        
        setData(initialData || [])
        setLoading(false)

        // Setup realtime subscription
        channel = supabase
          .channel(`${table}-changes`)
          .on(
            'postgres_changes',
            {
              event: '*',
              schema: 'public',
              table,
              filter
            },
            (payload) => {
              switch (payload.eventType) {
                case 'INSERT':
                  setData(prev => [...prev, payload.new as T])
                  break
                case 'UPDATE':
                  setData(prev => 
                    prev.map(item => 
                      item.id === payload.new.id 
                        ? payload.new as T 
                        : item
                    )
                  )
                  break
                case 'DELETE':
                  setData(prev => 
                    prev.filter(item => item.id !== payload.old.id)
                  )
                  break
              }
            }
          )
          .subscribe()
      } catch (err) {
        setError(err as Error)
        setLoading(false)
      }
    }

    setupRealtime()

    return () => {
      channel?.unsubscribe()
    }
  }, [table, filter])

  return { data, loading, error }
}
```

### 5. Optimistic Updates
```typescript
interface OptimisticUpdate<T> {
  execute: () => Promise<void>
  rollback: () => void
}

export function useOptimisticUpdate<T>(
  table: string,
  onSuccess?: (data: T) => void,
  onError?: (error: Error) => void
) {
  const [optimisticData, setOptimisticData] = useState<T | null>(null)
  const [isUpdating, setIsUpdating] = useState(false)

  const update = async (
    id: string,
    updates: Partial<T>,
    optimisticValue: T
  ): Promise<void> => {
    setIsUpdating(true)
    setOptimisticData(optimisticValue)

    try {
      const { data, error } = await supabase
        .from(table)
        .update(updates)
        .eq('id', id)
        .select()
        .single()

      if (error) throw error

      onSuccess?.(data)
      setOptimisticData(null)
    } catch (error) {
      // Rollback optimistic update
      setOptimisticData(null)
      onError?.(error as Error)
    } finally {
      setIsUpdating(false)
    }
  }

  return {
    update,
    optimisticData,
    isUpdating
  }
}
```

### 6. Cursor-based Pagination with Realtime
```typescript
export function useRealtimePagination<T>(
  table: string,
  pageSize: number = 20
) {
  const [items, setItems] = useState<T[]>([])
  const [cursor, setCursor] = useState<string | null>(null)
  const [hasMore, setHasMore] = useState(true)
  const [loading, setLoading] = useState(false)

  // Load more items
  const loadMore = async () => {
    if (loading || !hasMore) return

    setLoading(true)
    let query = supabase
      .from(table)
      .select('*')
      .order('created_at', { ascending: false })
      .limit(pageSize)

    if (cursor) {
      query = query.lt('created_at', cursor)
    }

    const { data, error } = await query

    if (data && data.length > 0) {
      setItems(prev => [...prev, ...data])
      setCursor(data[data.length - 1].created_at)
      setHasMore(data.length === pageSize)
    } else {
      setHasMore(false)
    }

    setLoading(false)
  }

  // Subscribe to new items
  useEffect(() => {
    const channel = supabase
      .channel(`${table}-new-items`)
      .on(
        'postgres_changes',
        {
          event: 'INSERT',
          schema: 'public',
          table
        },
        (payload) => {
          // Add new item to the beginning
          setItems(prev => [payload.new as T, ...prev])
        }
      )
      .subscribe()

    return () => {
      channel.unsubscribe()
    }
  }, [table])

  return {
    items,
    loadMore,
    hasMore,
    loading
  }
}
```

## Database Setup for Realtime

### Enable Realtime on Tables
```sql
-- Enable realtime for specific table
alter publication supabase_realtime add table public.messages;

-- Enable for multiple tables
alter publication supabase_realtime 
  add table public.messages,
  add table public.notifications,
  add table public.presence;

-- Check enabled tables
select * from pg_publication_tables 
where pubname = 'supabase_realtime';
```

### Optimized Indexes for Realtime
```sql
-- Index for filtered subscriptions
create index idx_messages_user_id 
  on public.messages(user_id);

create index idx_messages_created_at 
  on public.messages(created_at desc);

-- Composite index for complex filters
create index idx_messages_user_status 
  on public.messages(user_id, status)
  where deleted_at is null;
```

## Performance Best Practices

### 1. Channel Management
```typescript
// Reuse channels when possible
const channelCache = new Map<string, RealtimeChannel>()

function getOrCreateChannel(name: string): RealtimeChannel {
  if (!channelCache.has(name)) {
    const channel = supabase.channel(name)
    channelCache.set(name, channel)
  }
  return channelCache.get(name)!
}

// Clean up unused channels
function cleanupChannel(name: string) {
  const channel = channelCache.get(name)
  if (channel) {
    channel.unsubscribe()
    channelCache.delete(name)
  }
}
```

### 2. Batching Updates
```typescript
function useBatchedUpdates<T>(
  delay: number = 100
) {
  const [updates, setUpdates] = useState<T[]>([])
  const timeoutRef = useRef<NodeJS.Timeout>()

  const addUpdate = (update: T) => {
    setUpdates(prev => [...prev, update])

    clearTimeout(timeoutRef.current)
    timeoutRef.current = setTimeout(() => {
      processBatch(updates)
      setUpdates([])
    }, delay)
  }

  return { addUpdate, updates }
}
```

### 3. Connection State Management
```typescript
export function useRealtimeConnection() {
  const [status, setStatus] = useState<'connecting' | 'connected' | 'disconnected'>('connecting')
  const [retryCount, setRetryCount] = useState(0)

  useEffect(() => {
    const channel = supabase.channel('connection-check')
      .subscribe((status) => {
        if (status === 'SUBSCRIBED') {
          setStatus('connected')
          setRetryCount(0)
        } else if (status === 'CHANNEL_ERROR') {
          setStatus('disconnected')
          setRetryCount(prev => prev + 1)
        } else if (status === 'TIMED_OUT') {
          setStatus('disconnected')
        }
      })

    return () => {
      channel.unsubscribe()
    }
  }, [retryCount])

  return { status, retryCount }
}
```

## MCP Integration

```bash
# Enable realtime on table
mcp: supabase.apply_migration
  params: {
    name: "enable_realtime",
    query: "alter publication supabase_realtime add table public.your_table;"
  }

# Check realtime status
mcp: supabase.execute_sql
  params: {
    query: "select * from pg_publication_tables where pubname = 'supabase_realtime';"
  }
```

## Testing Realtime

```typescript
// Test helper
export async function testRealtimeSubscription(
  table: string,
  expectedEvents: number = 1
) {
  return new Promise((resolve, reject) => {
    const events: any[] = []
    const timeout = setTimeout(() => {
      channel.unsubscribe()
      reject(new Error('Realtime test timeout'))
    }, 5000)

    const channel = supabase
      .channel('test-channel')
      .on(
        'postgres_changes',
        { event: '*', schema: 'public', table },
        (payload) => {
          events.push(payload)
          if (events.length === expectedEvents) {
            clearTimeout(timeout)
            channel.unsubscribe()
            resolve(events)
          }
        }
      )
      .subscribe()
  })
}
```

---

Last Updated: 2025-01-10