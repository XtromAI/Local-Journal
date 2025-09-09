# **Local Journal: Technical Requirements Document (TRD)**

## **1. Technical Overview**

### **1.1 System Architecture**
The Local Journal application follows a **client-only architecture** with no server-side components. All data processing, storage, and AI interactions occur locally on the user's device, ensuring complete privacy and offline capability.

### **1.2 Technical Philosophy**
- **Privacy by Design**: All personal data remains on-device
- **Offline-First**: Core functionality works without internet connectivity
- **Performance-Oriented**: Optimized for mobile devices with efficient resource usage
- **Security-Focused**: Local data encryption and secure API key management

### **1.3 Technology Stack Summary**
- **Framework**: Flutter 3.x
- **Language**: Dart 3.x
- **Database**: Isar (NoSQL, local-only)
- **AI Integration**: Google Gemini API
- **State Management**: [TO BE DECIDED - see Section 8.1]
- **Vector Processing**: Custom implementation with cosine similarity
- **Build System**: Flutter build tools with platform-specific configurations

## **2. Application Architecture**

### **2.1 High-Level Architecture**

```
┌─────────────────────────────────────────────────────────────┐
│                    User Interface Layer                     │
├─────────────────────────────────────────────────────────────┤
│                   Business Logic Layer                      │
├─────────────────────────────────────────────────────────────┤
│                    Data Access Layer                        │
├─────────────────────────────────────────────────────────────┤
│                     Local Storage                           │
└─────────────────────────────────────────────────────────────┘
│
├──── External API Integration (Gemini API)
```

### **2.2 Component Architecture**

#### **2.2.1 Core Components**
- **ChatController**: Manages conversation state and AI interactions
- **JournalService**: Handles journal creation, selection, and management
- **EntryRepository**: Data access layer for journal entries
- **RAGEngine**: Vector similarity search and context retrieval
- **GeminiApiClient**: API communication with error handling
- **StorageService**: Local data persistence and encryption
- **EmbeddingService**: Vector generation and management

#### **2.2.2 Data Flow Architecture**
```
User Input → ChatController → GeminiApiClient → AI Response
     ↓              ↓              ↓
RAGEngine ← EntryRepository ← StorageService
     ↓
Context Injection → Enhanced AI Response
```

### **2.3 Directory Structure**
```
lib/
├── main.dart
├── app/
│   ├── app.dart
│   └── routes.dart
├── core/
│   ├── constants/
│   ├── errors/
│   ├── services/
│   └── utils/
├── data/
│   ├── datasources/
│   ├── models/
│   └── repositories/
├── domain/
│   ├── entities/
│   ├── repositories/
│   └── usecases/
├── presentation/
│   ├── controllers/
│   ├── pages/
│   └── widgets/
└── features/
    ├── chat/
    ├── journal/
    ├── entries/
    └── settings/
```

## **3. Data Architecture**

### **3.1 Database Schema (Isar)**

#### **3.1.1 Journal Entity**
```dart
@Collection()
class Journal {
  Id id = Isar.autoIncrement;
  
  @Index()
  String name;
  
  String? description;
  DateTime createdAt;
  DateTime updatedAt;
  
  // Relationships
  final entries = IsarLinks<JournalEntry>();
}
```

#### **3.1.2 Journal Entry Entity**
```dart
@Collection()
class JournalEntry {
  Id id = Isar.autoIncrement;
  
  @Index()
  int journalId;
  
  String summary;
  List<ChatMessage> conversation;
  List<double> summaryEmbedding;
  
  DateTime createdAt;
  DateTime completedAt;
  
  @Index()
  bool isCompleted;
}
```

#### **3.1.3 Chat Message Entity**
```dart
@Embedded()
class ChatMessage {
  String content;
  MessageType type; // user, ai, system
  DateTime timestamp;
  String? metadata; // For future extensibility
}
```

#### **3.1.4 API Key Storage**
```dart
@Collection()
class UserSettings {
  Id id = Isar.autoIncrement;
  
  @Encrypted()
  String? geminiApiKey;
  
  String theme;
  Map<String, dynamic> preferences;
  DateTime updatedAt;
}
```

### **3.2 Data Relationships**
- **One-to-Many**: Journal → JournalEntry
- **Embedded**: JournalEntry → List<ChatMessage>
- **Singleton**: UserSettings (single instance per installation)

### **3.3 Vector Storage Strategy**
- **Embedding Storage**: Float64List stored as List<double> in JournalEntry
- **Similarity Search**: In-memory cosine similarity calculation
- **Indexing**: Custom indexing for efficient vector retrieval
- **Performance**: Batch processing for multiple similarity calculations

## **4. API Integration Architecture**

### **4.1 Gemini API Client**

#### **4.1.1 API Endpoints Used**
- **generateContent**: Main conversation responses
- **embedContent**: Vector embedding generation

#### **4.1.2 Request/Response Handling**
```dart
class GeminiApiClient {
  static const String baseUrl = 'https://generativelanguage.googleapis.com/v1';
  
  Future<GenerateResponse> generateContent({
    required String prompt,
    List<String>? context,
  });
  
  Future<List<double>> embedContent(String text);
  
  // Error handling and retry logic
  Future<T> _executeWithRetry<T>(Future<T> Function() operation);
}
```

#### **4.1.3 Error Handling Strategy**
- **Network Errors**: Retry with exponential backoff
- **API Rate Limits**: Queue management and throttling
- **Invalid API Key**: User notification and re-authentication
- **Timeout Handling**: Configurable timeout with user feedback

### **4.2 Privacy-Preserving API Usage**
- **No Personal Data**: Only crafted prompts sent to API
- **Context Sanitization**: Historical context anonymized when possible
- **Request Logging**: No logging of personal content
- **Secure Transmission**: HTTPS with certificate pinning

## **5. Retrieval-Augmented Generation (RAG) Implementation**

### **5.1 RAG Pipeline Architecture**

#### **5.1.1 Embedding Generation Process**
```dart
class EmbeddingService {
  Future<List<double>> generateEmbedding(String text) async {
    // 1. Text preprocessing and sanitization
    // 2. API call to Gemini embedContent
    // 3. Vector normalization
    // 4. Return embedding vector
  }
  
  Future<void> storeEntryEmbedding(JournalEntry entry) async {
    // Generate and store embedding for completed entry
  }
}
```

#### **5.1.2 Vector Similarity Search**
```dart
class RAGEngine {
  Future<List<RelevantEntry>> findRelevantEntries({
    required String query,
    required int journalId,
    int limit = 3,
    double threshold = 0.7,
  }) async {
    // 1. Generate query embedding
    // 2. Calculate cosine similarity with stored embeddings
    // 3. Filter by threshold and journal context
    // 4. Return ranked results
  }
  
  double calculateCosineSimilarity(List<double> a, List<double> b);
}
```

#### **5.1.3 Context Injection Strategy**
```dart
class ContextBuilder {
  String buildPromptWithContext({
    required String userMessage,
    required List<RelevantEntry> context,
    required ConversationHistory currentSession,
  }) {
    // 1. Format historical context
    // 2. Include current conversation
    // 3. Add CBT-based guidance prompts
    // 4. Return complete prompt
  }
}
```

### **5.2 Performance Optimization**
- **Embedding Caching**: Store embeddings with entries
- **Batch Processing**: Efficient similarity calculations
- **Lazy Loading**: Load embeddings only when needed
- **Memory Management**: Efficient vector operations

## **6. User Interface Architecture**

### **6.1 Widget Architecture**

#### **6.1.1 Main Application Structure**
```dart
MaterialApp(
  home: MainScaffold(
    drawer: HistoricalEntriesPanel(),
    body: ChatInterface(),
    bottomNavigationBar: ChatControls(),
  ),
)
```

#### **6.1.2 Key UI Components**
- **ChatInterface**: Main conversation view with message list
- **MessageBubble**: Individual message display widget
- **ChatControls**: Input field and action buttons
- **HistoricalEntriesPanel**: Collapsible sidebar with past entries
- **JournalSelector**: Dropdown for journal switching
- **ConfirmationDialog**: Modal dialogs for critical actions

#### **6.1.3 Responsive Design Implementation**
```dart
class ResponsiveLayout extends StatelessWidget {
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth < 600) {
          return MobileLayout();
        } else if (constraints.maxWidth < 1200) {
          return TabletLayout();
        } else {
          return DesktopLayout();
        }
      },
    );
  }
}
```

### **6.2 State Management Architecture**

#### **6.2.1 State Structure** [TO BE DECIDED - see Section 8.1]
```dart
// Example structure - specific implementation TBD
class AppState {
  ChatState chatState;
  JournalState journalState;
  SettingsState settingsState;
  UIState uiState;
}
```

#### **6.2.2 State Persistence**
- **Chat State**: Persist active conversation to survive app restarts
- **UI State**: Remember panel states, selected journal, etc.
- **Settings State**: API key, preferences, theme selection

## **7. Security & Privacy Implementation**

### **7.1 Local Data Security**

#### **7.1.1 Database Encryption**
```dart
// Isar encryption configuration
await Isar.open(
  [JournalSchema, JournalEntrySchema, UserSettingsSchema],
  directory: dir.path,
  encryptionKey: await _getEncryptionKey(),
);
```

#### **7.1.2 API Key Management**
```dart
class SecureStorage {
  static const _storage = FlutterSecureStorage();
  
  Future<void> storeApiKey(String apiKey) async {
    await _storage.write(key: 'gemini_api_key', value: apiKey);
  }
  
  Future<String?> getApiKey() async {
    return await _storage.read(key: 'gemini_api_key');
  }
}
```

### **7.2 Privacy Controls**
- **Data Export**: JSON/CSV export functionality
- **Data Deletion**: Secure deletion with overwrite
- **Local Processing**: No cloud analytics or tracking
- **Minimal Permissions**: Only necessary device permissions

### **7.3 Network Security**
- **Certificate Pinning**: Prevent MITM attacks
- **Request Validation**: Sanitize all API requests
- **Rate Limiting**: Prevent API abuse
- **Timeout Management**: Avoid hanging requests

## **8. Technical Decisions Required**

### **8.1 State Management Solution**
**Decision Needed**: Choose state management approach

**Options:**
1. **Provider + ChangeNotifier**: Simple, Flutter-recommended
2. **Riverpod**: Type-safe, testable, good performance
3. **Bloc**: Event-driven, complex state management
4. **GetX**: All-in-one solution with dependency injection

**Recommendation Request**: Which approach do you prefer for this application?

### **8.2 Vector Processing Strategy**
**Decision Needed**: Vector similarity calculation approach

**Options:**
1. **Pure Dart Implementation**: Full control, no dependencies
2. **ML Kit Integration**: Google's on-device ML
3. **ONNX Runtime**: Cross-platform ML inference
4. **Custom Native Implementation**: Platform-specific optimization

**Recommendation Request**: What's your preference for vector processing performance vs. simplicity?

### **8.3 Build Configuration**
**Decision Needed**: Platform-specific build optimizations

**Questions:**
- Target minimum OS versions?
- App store distribution requirements?
- Code obfuscation level for release builds?
- Platform-specific performance optimizations?

### **8.4 Testing Strategy**
**Decision Needed**: Testing framework and coverage approach

**Options:**
1. **Unit Tests**: Core business logic testing
2. **Widget Tests**: UI component testing
3. **Integration Tests**: End-to-end workflow testing
4. **Performance Tests**: Memory and speed benchmarks

**Recommendation Request**: What testing coverage level do you want to target?

## **9. Performance Requirements**

### **9.1 Response Time Targets**
- **App Launch**: < 2 seconds cold start
- **AI Response**: < 3 seconds including network
- **Vector Search**: < 100ms for similarity calculation
- **UI Transitions**: < 16ms for 60fps animations
- **Database Operations**: < 50ms for typical queries

### **9.2 Memory Management**
```dart
class MemoryManager {
  // Conversation history limits
  static const maxMessagesInMemory = 100;
  static const maxEmbeddingsInCache = 1000;
  
  // Garbage collection triggers
  void cleanupOldConversations();
  void clearEmbeddingCache();
  void optimizeDatabase();
}
```

### **9.3 Storage Optimization**
- **Compression**: Compress conversation data
- **Cleanup**: Automatic cleanup of temporary data
- **Efficient Indexing**: Optimize Isar indexes for common queries
- **Vector Storage**: Efficient float array storage

## **10. Development & Deployment**

### **10.1 Development Environment**
```yaml
# pubspec.yaml key dependencies
dependencies:
  flutter: ^3.0.0
  isar: ^3.1.0
  isar_flutter_libs: ^3.1.0
  http: ^1.1.0
  flutter_secure_storage: ^9.0.0
  path_provider: ^2.1.0

dev_dependencies:
  build_runner: ^2.4.0
  isar_generator: ^3.1.0
  flutter_test:
    sdk: flutter
```

### **10.2 Build Configuration**
```yaml
# Platform-specific build settings
android:
  minSdkVersion: 21
  targetSdkVersion: 34
  
ios:
  deployment_target: '12.0'
  
web:
  renderer: canvaskit # For better performance
```

### **10.3 CI/CD Pipeline**
- **Automated Testing**: Unit, widget, and integration tests
- **Code Quality**: Static analysis and linting
- **Security Scanning**: Dependency vulnerability checks
- **Platform Builds**: Automated builds for all target platforms
- **Release Management**: Version tagging and changelog generation

## **11. Monitoring & Analytics**

### **11.1 Privacy-Compliant Monitoring**
- **Performance Metrics**: App startup time, response times
- **Error Tracking**: Crash reports without personal data
- **Usage Statistics**: Feature adoption without user identification
- **API Metrics**: Request success rates and response times

### **11.2 Logging Strategy**
```dart
class PrivacyLogger {
  // Log levels: ERROR, WARNING, INFO, DEBUG
  static void logError(String message, {Exception? exception});
  static void logPerformance(String operation, Duration duration);
  static void logApiCall(String endpoint, int statusCode);
  
  // Never log personal data
  static void sanitizeLogData(Map<String, dynamic> data);
}
```

## **12. Maintenance & Updates**

### **12.1 Version Management**
- **Semantic Versioning**: Major.Minor.Patch versioning
- **Database Migration**: Isar schema migration strategies
- **API Versioning**: Handle Gemini API version changes
- **Feature Flags**: Gradual feature rollout capability

### **12.2 Update Strategy**
- **Hot Updates**: Non-breaking feature updates
- **Migration Scripts**: Database schema updates
- **Backward Compatibility**: Support for older data formats
- **Rollback Capability**: Safe update rollback mechanisms

## **13. Conclusion**

This Technical Requirements Document provides the engineering foundation for building the Local Journal application. The architecture prioritizes privacy, performance, and user experience while maintaining technical simplicity and maintainability.

Key technical decisions still need to be made regarding state management, vector processing, and testing strategies. Once these decisions are finalized, development can proceed with clear technical specifications and implementation guidelines.

The modular architecture allows for iterative development and easy maintenance while ensuring the application can scale to meet user needs and future feature requirements.
