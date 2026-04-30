# AI Chatbot Feature - Implementation Guide

## Overview

The AI Chatbot feature has been successfully integrated into the OmniVision React application. It provides an intelligent assistant that helps users filter and summarize incident data based on natural language queries.

## Features

### 1. **Natural Language Query Parsing**
   - Users can ask questions in natural language
   - The bot understands keywords like "person", "object", "behavior", severity levels, status, and locations
   - Intelligent filtering based on query context

### 2. **Incident Filtering**
   - Filter by incident type (person, object, behavior)
   - Filter by severity (critical, high, medium, low)
   - Filter by status (active, resolved, pending)
   - Filter by location (Sector, Entrance, Perimeter, etc.)
   - Combine multiple filters in a single query

### 3. **Automated Summarization**
   - Generates comprehensive incident summaries
   - Shows statistics (total incidents, active/resolved counts, severity breakdown)
   - Lists all matching incidents with details
   - Displays confidence scores and locations
   - Color-coded severity indicators

### 4. **User-Friendly Interface**
   - Floating chat bubble for easy access
   - Clean, modern chat interface with dark theme
   - Message history within the session
   - Minimize/maximize functionality
   - Typing indicators and loading states
   - Responsive design for all screen sizes

## File Structure

```
src/
├── components/
│   ├── ChatBot.tsx              # Main chat UI component
│   └── AppLayout.tsx            # (existing)
├── services/
│   └── chatbotService.ts        # AI logic and incident data
├── pages/
│   ├── AIInsights.tsx           # (existing)
│   └── ...
├── App.tsx                      # Updated to include ChatBot
└── ...
```

## Component Architecture

### `chatbotService.ts`
**Purpose:** Core AI logic and data management

**Key Methods:**
- `parseQuery(query: string)`: Extracts filter criteria from user input
- `filterIncidents(criteria: FilterCriteria)`: Filters incidents based on criteria
- `generateSummary(incidents: IncidentData[])`: Creates formatted incident summaries
- `generateResponse(query: string)`: Main method that processes queries and returns responses
- `getAllIncidents()`: Returns all incidents
- `addIncident(incident: IncidentData)`: Adds new incidents (for future API integration)

**Data Structure:**
```typescript
interface IncidentData {
  id: string
  timestamp: string
  type: 'object' | 'person' | 'behavior'
  description: string
  severity: 'low' | 'medium' | 'high' | 'critical'
  location: string
  confidence: number
  involved: {
    objects?: string[]
    people?: string[]
  }
  status: 'active' | 'resolved' | 'pending'
}
```

### `ChatBot.tsx`
**Purpose:** Interactive UI component for chat interface

**Features:**
- Message sending and receiving
- Auto-scroll to latest messages
- Loading states with animated indicators
- Minimize/maximize chat window
- Keyboard shortcuts (Shift+Enter for multiline, Enter to send)
- Floating action button for accessibility

## Usage Examples

### Example Queries

1. **Find all critical incidents:**
   ```
   "Show me critical incidents"
   ```

2. **Find active person incidents:**
   ```
   "What person incidents are currently active?"
   ```

3. **Filter by location:**
   ```
   "Summarize incidents in Sector 7"
   ```

4. **Filter by object type:**
   ```
   "Show all suspicious objects"
   ```

5. **Combine filters:**
   ```
   "Show me high severity active incidents in Sector 3"
   ```

6. **Get full summary:**
   ```
   "Give me a summary of all incidents"
   ```

## Integration with Existing Features

The ChatBot is now integrated as a global component available on all pages:

1. **Dashboard** - Access incident insights while monitoring live feeds
2. **AI Insights** - Enhanced with AI assistant for data exploration
3. **Alerts** - Quick incident summaries from the alert page
4. **Live Feeds** - Real-time incident analysis while watching cameras
5. **All Pages** - Accessible via floating chat button

## Mock Data

The service includes 5 sample incidents:
- Unauthorized person in restricted zone (HIGH severity)
- Unidentified package (CRITICAL severity)
- Loitering detected (MEDIUM severity)
- Suspicious vehicle (MEDIUM severity)
- Crowd gathering (LOW severity)

## Future Enhancements

### 1. **Real API Integration**
   ```typescript
   // Replace mock data with real API calls
   const fetchIncidents = async () => {
     const response = await fetch('/api/incidents')
     return response.json()
   }
   ```

### 2. **Machine Learning Integration**
   - Train models on historical incident data
   - Predict incident patterns
   - Anomaly detection

### 3. **Advanced Features**
   - Voice input/output
   - Multi-language support
   - Incident recommendations
   - Sentiment analysis
   - Incident predictions

### 4. **User Preferences**
   - Save favorite filters
   - Custom alert thresholds
   - Personalized summaries

### 5. **Export Functionality**
   - Export chat history as PDF
   - Generate incident reports
   - Share incident summaries

## Development Notes

### Adding New Query Patterns

To add new query patterns, modify the `parseQuery` method in `chatbotService.ts`:

```typescript
// Example: Add support for "unauthorized" keyword
if (lowerQuery.includes('unauthorized') || lowerQuery.includes('trespass')) {
  criteria.type = 'person'
  criteria.severity = 'high'
}
```

### Adding New Incidents

To add mock incidents, update the `mockIncidents` array in `chatbotService.ts`:

```typescript
const mockIncidents: IncidentData[] = [
  // ... existing incidents
  {
    id: 'INC006',
    timestamp: '2026-04-27T09:00:00Z',
    type: 'behavior',
    description: 'Your description here',
    severity: 'high',
    location: 'New Location',
    confidence: 95.0,
    involved: { people: ['PersonID'] },
    status: 'active',
  },
]
```

## Performance Considerations

- Incident filtering is O(n) but suitable for current dataset size
- For larger datasets (10k+ incidents), consider:
  - Indexing by type, severity, and location
  - Pagination of results
  - Caching filtered results

## Security Notes

- All incident data is currently client-side (mock)
- For production:
  - Validate and sanitize user inputs
  - Implement proper API authentication
  - Use role-based access control (RBAC)
  - Audit all data access
  - Encrypt sensitive incident information

## Testing

To test the chatbot locally:

1. Run the development server:
   ```bash
   npm run dev
   ```

2. Click the floating chat button (bottom-right corner)

3. Try the example queries above

4. Test edge cases:
   - Empty queries
   - Non-existent locations
   - Multiple filters
   - Invalid keywords

## Troubleshooting

### ChatBot not appearing?
- Ensure `ChatBot` component is imported in `App.tsx`
- Check browser console for errors
- Clear cache and rebuild: `npm run build`

### Queries not working?
- Check spelling and query format
- Verify keywords match the `parseQuery` method
- Check browser console for debugging info

### Build errors?
- Ensure all imports use proper type-only syntax
- Run `npm run lint` to check for issues
- Delete `dist/` folder and rebuild

## Files Modified/Created

### Created:
- `src/services/chatbotService.ts` - Core AI service
- `src/components/ChatBot.tsx` - Chat UI component

### Modified:
- `src/App.tsx` - Added ChatBot global component

## Dependencies

No new dependencies added! Uses existing:
- React 19.2.5
- Lucide React (for icons)
- Tailwind CSS (for styling)

## License

Part of OmniVision AI security system.
