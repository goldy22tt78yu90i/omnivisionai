/**
 * API Integration Guide for ChatBot Service
 * This file shows how to integrate the chatbot with a real backend API
 */

// ============================================
// Step 1: Replace Mock Data with API Calls
// ============================================

/*
// In chatbotService.ts, replace the mock data with API integration:

import { IncidentData, ChatMessage } from './types'

class ChatBotService {
  private incidents: IncidentData[] = []
  private isLoading = false

  async initialize() {
    try {
      this.isLoading = true
      this.incidents = await this.fetchIncidentsFromAPI()
    } catch (error) {
      console.error('Failed to fetch incidents:', error)
      // Fallback to mock data
      this.incidents = mockIncidents
    } finally {
      this.isLoading = false
    }
  }

  private async fetchIncidentsFromAPI(): Promise<IncidentData[]> {
    const response = await fetch('/api/incidents', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${getAuthToken()}`
      }
    })

    if (!response.ok) {
      throw new Error(`API error: ${response.statusText}`)
    }

    return response.json()
  }

  // Add support for real-time updates
  async subscribeToIncidentUpdates(callback: (incident: IncidentData) => void) {
    // WebSocket or Server-Sent Events implementation
    const eventSource = new EventSource('/api/incidents/stream')

    eventSource.addEventListener('incident_update', (event) => {
      const incident: IncidentData = JSON.parse(event.data)
      this.incidents.push(incident)
      callback(incident)
    })

    return () => eventSource.close()
  }
}
*/

// ============================================
// Step 2: API Endpoints Reference
// ============================================

/*
Backend API Endpoints Expected:

GET /api/incidents
  - Returns: IncidentData[]
  - Query params: ?severity=high&type=person&status=active&location=Sector7
  - Headers: Authorization: Bearer {token}

GET /api/incidents/:id
  - Returns: IncidentData
  - Gets specific incident by ID

POST /api/incidents
  - Body: Partial<IncidentData>
  - Returns: IncidentData (with ID and timestamp)
  - Creates new incident

PUT /api/incidents/:id
  - Body: Partial<IncidentData>
  - Returns: IncidentData
  - Updates incident

DELETE /api/incidents/:id
  - Returns: { success: boolean }
  - Deletes incident

GET /api/incidents/stats
  - Returns: IncidentStats
  - Gets aggregated statistics

WebSocket /ws/incidents
  - Real-time incident updates
  - Messages: { type: 'incident_update', data: IncidentData }
*/

// ============================================
// Step 3: Error Handling
// ============================================

/*
type APIError = {
  code: string
  message: string
  details?: unknown
}

class ChatBotServiceWithErrorHandling {
  async fetchIncidentsFromAPI(): Promise<IncidentData[]> {
    try {
      const response = await fetch('/api/incidents')

      if (response.status === 401) {
        throw new APIError('UNAUTHORIZED', 'Authentication required')
      }

      if (response.status === 403) {
        throw new APIError('FORBIDDEN', 'Insufficient permissions')
      }

      if (response.status === 429) {
        throw new APIError('RATE_LIMITED', 'Too many requests. Please try again later.')
      }

      if (!response.ok) {
        throw new APIError('API_ERROR', `Server error: ${response.statusText}`)
      }

      return response.json()
    } catch (error) {
      if (error instanceof APIError) {
        console.error(`API Error [${error.code}]: ${error.message}`)
      }
      throw error
    }
  }
}
*/

// ============================================
// Step 4: Caching Strategy
// ============================================

/*
class ChatBotServiceWithCaching {
  private cache = new Map<string, { data: IncidentData[], timestamp: number }>()
  private CACHE_TTL = 5 * 60 * 1000 // 5 minutes

  async getIncidentsCached(cacheKey: string): Promise<IncidentData[]> {
    const cached = this.cache.get(cacheKey)

    if (cached && Date.now() - cached.timestamp < this.CACHE_TTL) {
      console.log('Using cached incidents')
      return cached.data
    }

    const incidents = await this.fetchIncidentsFromAPI()
    this.cache.set(cacheKey, { data: incidents, timestamp: Date.now() })

    return incidents
  }

  clearCache(cacheKey?: string) {
    if (cacheKey) {
      this.cache.delete(cacheKey)
    } else {
      this.cache.clear()
    }
  }
}
*/

// ============================================
// Step 5: Advanced Query Optimization
// ============================================

/*
// For large datasets, use query parameters instead of filtering on client:

async getFilteredIncidents(criteria: FilterCriteria): Promise<IncidentData[]> {
  const params = new URLSearchParams()

  if (criteria.type) params.append('type', criteria.type)
  if (criteria.severity) params.append('severity', criteria.severity)
  if (criteria.status) params.append('status', criteria.status)
  if (criteria.location) params.append('location', criteria.location)
  if (criteria.limit) params.append('limit', criteria.limit.toString())
  if (criteria.offset) params.append('offset', criteria.offset.toString())

  const response = await fetch(`/api/incidents?${params.toString()}`)
  return response.json()
}
*/

// ============================================
// Step 6: React Integration Example
// ============================================

/*
import { useEffect, useState } from 'react'
import chatbotService, { IncidentData } from '../services/chatbotService'

export function ChatBotWithRealAPI() {
  const [incidents, setIncidents] = useState<IncidentData[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    // Initialize service and fetch incidents
    (async () => {
      try {
        setLoading(true)
        await chatbotService.initialize()
        setIncidents(chatbotService.getAllIncidents())
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error')
      } finally {
        setLoading(false)
      }
    })()

    // Subscribe to real-time updates
    const unsubscribe = chatbotService.subscribeToIncidentUpdates((newIncident) => {
      setIncidents((prev) => [newIncident, ...prev])
      // Show toast notification
      showNotification(`New incident: ${newIncident.description}`)
    })

    return () => unsubscribe()
  }, [])

  if (loading) return <div>Loading incidents...</div>
  if (error) return <div>Error: {error}</div>

  return (
    <div>
      <h3>Total Incidents: {incidents.length}</h3>
      {/* Render incidents */}
    </div>
  )
}
*/

// ============================================
// Step 7: Authentication Integration
// ============================================

/*
class ChatBotServiceWithAuth {
  private token: string | null = null

  setAuthToken(token: string) {
    this.token = token
    localStorage.setItem('auth_token', token)
  }

  private getAuthHeaders(): HeadersInit {
    return {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${this.token || localStorage.getItem('auth_token')}`
    }
  }

  async fetchIncidentsFromAPI(): Promise<IncidentData[]> {
    const response = await fetch('/api/incidents', {
      method: 'GET',
      headers: this.getAuthHeaders()
    })

    if (response.status === 401) {
      // Token expired, refresh or redirect to login
      this.handleTokenExpired()
    }

    return response.json()
  }

  private handleTokenExpired() {
    // Clear stored token
    localStorage.removeItem('auth_token')
    this.token = null
    // Redirect to login
    window.location.href = '/login'
  }
}
*/

// ============================================
// Step 8: Testing with Mock Server
// ============================================

/*
// For development, use MSW (Mock Service Worker)
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const handlers = [
  http.get('/api/incidents', () => {
    return HttpResponse.json(mockIncidents)
  }),

  http.post('/api/incidents', async ({ request }) => {
    const newIncident = await request.json()
    return HttpResponse.json({ ...newIncident, id: 'NEW_ID' }, { status: 201 })
  }),

  http.get('/api/incidents/:id', ({ params }) => {
    const incident = mockIncidents.find((i) => i.id === params.id)
    return incident ? HttpResponse.json(incident) : HttpResponse.json(null, { status: 404 })
  }),
]

export const server = setupServer(...handlers)
*/

// ============================================
// Step 9: Performance Monitoring
// ============================================

/*
class ChatBotServiceWithMonitoring {
  private metrics = {
    queryCount: 0,
    averageQueryTime: 0,
    errorCount: 0,
  }

  async generateResponse(query: string): Promise<string> {
    const startTime = performance.now()

    try {
      const criteria = this.parseQuery(query)
      const filtered = this.filterIncidents(criteria)
      const response = this.generateSummary(filtered)

      const duration = performance.now() - startTime
      this.recordMetric('query_time', duration)
      this.metrics.queryCount++

      return response
    } catch (error) {
      this.metrics.errorCount++
      throw error
    }
  }

  private recordMetric(name: string, value: number) {
    // Send to analytics service
    console.log(`Metric: ${name} = ${value}ms`)
  }

  getMetrics() {
    return this.metrics
  }
}
*/

// ============================================
// Step 10: Migration Checklist
// ============================================

/*
API Integration Checklist:
☐ Set up backend API endpoints
☐ Implement authentication (JWT, OAuth, etc.)
☐ Replace mock data with API calls
☐ Add error handling and logging
☐ Implement caching strategy
☐ Add real-time updates (WebSocket/SSE)
☐ Set up rate limiting on client
☐ Add loading states and error UI
☐ Test with various network conditions
☐ Monitor API performance
☐ Add analytics tracking
☐ Document API contracts
☐ Set up API key rotation
☐ Implement audit logging
☐ Add data encryption for sensitive fields
*/

export {}
