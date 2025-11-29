# **Local Journal: Technical Requirements Document (TRD)**

## **1. Executive Technical Summary**

### **1.1 System Architecture**
The Local Journal application follows a **client-only architecture** with no server-side components. All data processing, storage, and AI interactions occur locally on the user's device, ensuring complete privacy and offline capability.

### **1.2 Technical Philosophy**
- **Privacy by Design**: All personal data remains on-device
- **Offline-First**: Core functionality works without internet connectivity
- **Performance-Oriented**: Optimized for mobile devices with efficient resource usage
- **Security-Focused**: Local data encryption and secure API key management

### **1.3 Technology Stack**
| Component | Technology | Rationale |
|-----------|------------|-----------|
| Framework | Flutter 3.x | Cross-platform, single codebase for Android/iOS |
| Language | Dart 3.x | Type-safe, optimized for UI development |
| Database | Isar | High-performance local NoSQL with encryption support |
| AI Integration | Google Gemini API | Advanced language model with embedding capabilities |
| State Management | Provider + ChangeNotifier | Simple, scalable reactive state management |
| Vector Processing | ml_linalg + vector_math | Efficient local vector operations for RAG |
| Target Platform | Android (Google Play Store) | Primary launch platform |
| Future Platform | iOS | Same codebase, planned expansion |

## **2. Application Architecture**

### **2.1 High-Level System Design**

```
┌─────────────────────────────────────────────────────────────┐
│                    User Interface Layer                     │
│                (Provider + ChangeNotifier)                  │
├─────────────────────────────────────────────────────────────┤
│                   Business Logic Layer                      │
│              (RAG Engine + Chat Controller)                 │
├─────────────────────────────────────────────────────────────┤
│                    Data Access Layer                        │
│              (Repository Pattern + Services)                │
├─────────────────────────────────────────────────────────────┤
│                     Local Storage                           │
│                 (Isar Database + Encryption)                │
└─────────────────────────────────────────────────────────────┘
                            │
              External API Integration (Gemini API)
```

### **2.2 Core Components**

| Component | Responsibility |
|-----------|----------------|
| ChatProvider | Manages conversation state and AI interactions |
| JournalProvider | Handles journal creation, selection, and management |
| SettingsProvider | Manages API keys and user preferences |
| RAGEngine | Vector similarity search and context retrieval |
| GeminiApiClient | API communication with error handling and retry logic |
| EntryRepository | Data access layer for journal entries |
| EmbeddingService | Vector generation and management |
| ExportService | Data export functionality (JSON/CSV) |

### **2.3 Data Flow**

**Conversation Flow:**
```
User Input → ChatProvider → RAGEngine (context lookup) → GeminiApiClient → AI Response → UI Update
```

**Entry Completion Flow:**
```
Finish Entry → Generate Summary → Generate Embedding → Save to Database → Update UI
```

### **2.4 Project Structure**

The application follows **Clean Architecture** principles with clear separation of concerns:

```
lib/
├── main.dart                 # Application entry point
├── app/                      # App configuration and routing
├── core/                     # Shared utilities, constants, services
│   ├── constants/            # App-wide and API constants
│   ├── errors/               # Exception handling
│   ├── services/             # Storage, encryption, export services
│   └── utils/                # Vector operations, text processing
├── data/                     # Data layer
│   ├── datasources/          # Local (Isar) and remote (API) sources
│   ├── models/               # Data transfer objects
│   └── repositories/         # Repository implementations
├── domain/                   # Business logic layer
│   ├── entities/             # Business objects
│   ├── repositories/         # Repository interfaces
│   └── usecases/             # Application use cases
├── presentation/             # UI layer
│   ├── providers/            # State management
│   ├── pages/                # Screen widgets
│   └── widgets/              # Reusable UI components
└── features/                 # Feature modules (chat, journal, RAG, etc.)
```

## **3. Data Architecture**

### **3.1 Database Schema**

#### **3.1.1 Journal Entity**
| Field | Type | Description |
|-------|------|-------------|
| id | Integer (auto) | Primary key |
| name | String (indexed) | Journal name |
| description | String? | Optional description |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last update timestamp |
| entries | Relation | Link to JournalEntry entities |

#### **3.1.2 Journal Entry Entity**
| Field | Type | Description |
|-------|------|-------------|
| id | Integer (auto) | Primary key |
| journalId | Integer (indexed) | Foreign key to Journal |
| summary | String | AI-generated summary |
| conversation | List | Full conversation history |
| summaryEmbedding | List | 768-dimensional vector for RAG |
| createdAt | DateTime | Creation timestamp |
| completedAt | DateTime | Completion timestamp |
| isCompleted | Boolean (indexed) | Draft vs finalized status |

#### **3.1.3 Chat Message Entity**
| Field | Type | Description |
|-------|------|-------------|
| content | String | Message text |
| type | Enum | user, ai, or system |
| timestamp | DateTime | Message timestamp |
| metadata | String? | Optional extensibility field |

#### **3.1.4 User Settings Entity**
| Field | Type | Description |
|-------|------|-------------|
| id | Integer (auto) | Primary key |
| geminiApiKey | String (encrypted) | User's API key |
| theme | String | Theme preference (default: dark) |
| preferences | Map | Additional user preferences |
| updatedAt | DateTime | Last update timestamp |

### **3.2 Database Operations**

- **Initialization**: Database opens with encryption key from secure storage
- **CRUD Operations**: Repository pattern abstracts all database interactions
- **Queries**: Indexed fields enable efficient filtering and sorting
- **Transactions**: Write operations wrapped in transactions for data integrity

## **4. State Management**

### **4.1 Provider Architecture**

The application uses a **hierarchical provider structure**:

1. **Root Providers** (independent):
   - SettingsProvider - API key, theme, preferences
   - JournalProvider - Journal list, selection, entries

2. **Dependent Providers**:
   - ChatProvider - Depends on SettingsProvider (API key) and JournalProvider (selected journal)
   - ExportProvider - Depends on JournalProvider and EntryRepository

### **4.2 State Flow**

| Provider | State | Actions |
|----------|-------|---------|
| ChatProvider | messages, isLoading, conversationState | sendMessage, finishEntry, cancelEntry |
| JournalProvider | journals, selectedJournal, entries, isLoading | createJournal, selectJournal, deleteJournal |
| SettingsProvider | apiKey, theme, preferences | updateApiKey, updateTheme |
| ExportProvider | isExporting, lastResult, progress | exportToJson, exportToCsv |

### **4.3 Conversation States**

| State | Description |
|-------|-------------|
| idle | No active conversation |
| active | Conversation in progress |
| finishing | Generating summary and saving |

## **5. AI Integration**

### **5.1 Gemini API Integration**

**Endpoints Used:**
- `generateContent` - Chat responses and summary generation
- `embedContent` - Vector embedding generation for RAG

**API Configuration:**
| Parameter | Value | Purpose |
|-----------|-------|---------|
| Model | gemini-pro | Text generation |
| Embedding Model | models/embedding-001 | 768-dimensional vectors |
| Temperature | 0.7 | Balanced creativity/consistency |
| Max Output Tokens | 1024 | Response length limit |

### **5.2 AI Persona**

The AI acts as a supportive journaling companion based on **Cognitive Behavioral Therapy (CBT)** principles:

- Asks open-ended questions encouraging deeper reflection
- Helps identify patterns in thinking and behavior
- Provides supportive, non-judgmental responses
- Uses thought-challenging techniques when appropriate
- Keeps responses concise but meaningful
- Addresses user directly as "you"

### **5.3 Error Handling**

| Error Type | Handling Strategy |
|------------|-------------------|
| Network Failure | Exponential backoff retry (3 attempts) |
| Invalid API Key | User notification with setup guidance |
| Rate Limiting | Queued retry with delay |
| API Error | Graceful degradation with user message |

## **6. RAG Implementation**

### **6.1 RAG Pipeline**

**Step 1: Embedding Generation**
- When entry is finalized, summary is sent to Gemini embedContent endpoint
- Returns 768-dimensional vector stored with entry

**Step 2: Context Retrieval**
- User message is embedded using same endpoint
- Cosine similarity calculated against stored embeddings (finalized entries only)
- Top N entries above threshold returned as context

**Step 3: Context Injection**
- Relevant summaries included in AI prompt
- Provides historical context for personalized responses

### **6.2 RAG Configuration**

| Parameter | Value | Purpose |
|-----------|-------|---------|
| Similarity Threshold | 0.7 | Minimum relevance score |
| Max Context Entries | 3 | Limit context size |
| Embedding Dimension | 768 | Gemini embedding size |

### **6.3 Performance Optimization**

- **Batch Processing**: Multiple similarity calculations in single operation
- **Caching**: Recent embeddings cached in memory
- **Lazy Loading**: Embeddings loaded on-demand
- **Index Filtering**: Only finalized entries considered for RAG

## **7. Data Export**

### **7.1 Export Formats**

**JSON Export (Complete):**
- All journals with metadata
- All entries with full conversation history
- User settings (excluding API key for security)
- Export metadata (timestamp, version, counts)

**CSV Export (Entries):**
- Spreadsheet-compatible format
- Journal name, entry date, summary
- Conversation length, first/last messages
- Timestamps

### **7.2 Export Process**

1. User initiates export from settings
2. All data gathered from local database
3. Data formatted in chosen format
4. File saved to device storage with timestamp
5. Success notification with file location

### **7.3 Privacy Considerations**

- API keys excluded from all exports
- Vector embeddings excluded (can be regenerated)
- Export process is fully local (no network transmission)
- User controls export timing and location

## **8. Security & Privacy**

### **8.1 Data Protection**

| Data Type | Protection Method |
|-----------|-------------------|
| API Key | Flutter Secure Storage (platform keychain) |
| Database | Isar encryption with secure random key |
| Encryption Key | Stored in platform secure storage |

### **8.2 API Communication**

- Only prompts sent to Gemini API (no raw journal content)
- HTTPS encryption for all API calls
- No personal identifiers in API requests
- API key included in request headers only

### **8.3 Local Security**

- Database encrypted at rest
- Encryption key derived and stored securely
- No plain-text sensitive data in storage
- Secure deletion capabilities for all user data

## **9. User Interface**

### **9.1 Screen Layout**

**Main Page:**
- App bar with journal selector and menu
- Chat interface (scrollable message list)
- **Message Rendering**: Markdown support (bold, italic, lists) and Emoji rendering
- Chat controls (input field + action buttons)
- Collapsible side panel for historical entries

**Chat Controls:**
- Multi-line text input
- "Go Deeper" button - Submit message
- "Finish Entry" button - Complete with summary
- Cancel button - Discard with confirmation

### **9.2 Dialogs**

| Dialog | Purpose | Actions |
|--------|---------|---------|
| Finish Entry | Confirm entry completion | Confirm / Cancel |
| Cancel Entry | Confirm discard | Confirm (destructive) / Cancel |
| Export | Select export format | JSON / CSV / Cancel |
| Export Success | Show result | Done |

### **9.3 Theme**

- **Primary**: Dark mode optimized for extended use
- **Support**: Light mode and system preference options
- **Colors**: High contrast for readability
- **Typography**: Clear, legible fonts

## **10. Performance Requirements**

### **10.1 Response Times**

| Operation | Target | Maximum |
|-----------|--------|---------|
| AI Response | < 2s | 3s |
| App Launch | < 1s | 2s |
| Entry Loading | < 0.5s | 1s |
| Search Results | < 0.3s | 0.5s |
| Vector Similarity | < 100ms | 200ms |

### **10.2 Resource Usage**

| Resource | Target |
|----------|--------|
| Memory | Efficient conversation history management |
| Storage | Optimized database with periodic compaction |
| Battery | Minimal background processing |
| Network | Only for AI API calls |

### **10.3 Reliability**

| Metric | Target |
|--------|--------|
| Crash-free sessions | 99%+ |
| Data persistence | 100% |
| Offline capability | Full (except AI responses) |

## **11. Testing Strategy**

### **11.1 Test Categories**

| Category | Scope |
|----------|-------|
| Unit Tests | Individual functions, utilities, services |
| Widget Tests | UI components in isolation |
| Integration Tests | Feature flows and data persistence |
| Performance Tests | Vector operations, memory usage |

### **11.2 Key Test Areas**

- RAG engine accuracy and performance
- State management correctness
- Database operations and encryption
- API client error handling
- Export data integrity
- UI responsiveness and accessibility

## **12. Build & Deployment**

### **12.1 Dependencies**

**Production Dependencies:**
| Package | Purpose |
|---------|---------|
| isar, isar_flutter_libs | Local database |
| path_provider | File system access |
| flutter_secure_storage | Secure credential storage |
| provider | State management |
| http | API communication |
| ml_linalg, vector_math | Vector processing |
| csv | CSV export generation |

**Development Dependencies:**
| Package | Purpose |
|---------|---------|
| flutter_lints | Code quality |
| build_runner, isar_generator | Code generation |
| mockito | Test mocking |
| integration_test | E2E testing |

### **12.2 Build Configuration**

**Android:**
- Minimum SDK: 21 (Android 5.0)
- Target SDK: 34 (Android 14)
- Release builds: Obfuscation and minification enabled
- App Bundle format for Play Store

### **12.3 CI/CD Pipeline**

| Stage | Actions |
|-------|---------|
| Test | Lint, analyze, unit tests, integration tests |
| Build | Android APK and App Bundle |
| Deploy | Artifact upload, Play Store deployment |

## **13. Future Considerations**

### **13.1 Planned Enhancements**
- iOS platform release
- Web and desktop versions
- Advanced analytics and visualizations
- Enhanced pattern recognition

### **13.2 Scalability**
- Architecture supports feature modules
- Database schema supports future entities
- Provider pattern scales with complexity

---

This document provides the technical foundation for Local Journal development. Implementation details and code will be developed during the build phase following these specifications.
