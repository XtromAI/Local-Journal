# GitHub Copilot Instructions for Local Journal

## Project Overview

Local Journal is a privacy-first, AI-powered personal journaling application built with Flutter. The app provides intelligent, conversational journaling experiences while maintaining complete user privacy through local-only data processing.

### Key Principles
- **Privacy by Design**: All personal data remains on-device, never transmitted to external servers
- **Offline-First**: Core functionality works without internet connectivity
- **Performance-Oriented**: Optimized for mobile devices with efficient resource usage
- **Security-Focused**: Local data encryption and secure API key management

## Technology Stack

- **Framework**: Flutter 3.x
- **Language**: Dart 3.x
- **Database**: Isar (NoSQL, local-only database)
- **AI Integration**: Google Gemini API
- **State Management**: Provider + ChangeNotifier pattern
- **Vector Processing**: ml_linalg + vector_math libraries
- **Target Platforms**: Android (primary), iOS (future), Web, Desktop

## Architecture & Code Structure

### Clean Architecture Pattern
The project follows Clean Architecture with these layers:
- **Presentation Layer**: Providers, Pages, Widgets
- **Domain Layer**: Entities, Use Cases, Repository Interfaces
- **Data Layer**: Models, Data Sources, Repository Implementations
- **Core Layer**: Services, Utils, Constants, Error Handling

### Key Components
- `ChatProvider`: Manages conversation state and AI interactions
- `JournalProvider`: Handles journal creation and management
- `RAGEngine`: Vector similarity search and context retrieval
- `GeminiApiClient`: API communication with error handling
- `EntryRepository`: Data access for journal entries
- `EmbeddingService`: Vector generation and management

### Project Structure
```
lib/
├── main.dart
├── app/ (App configuration and routing)
├── core/ (Constants, services, utilities, error handling)
├── data/ (Data sources, models, repositories)
├── domain/ (Entities, use cases, repository interfaces)
├── presentation/ (Providers, pages, widgets)
└── features/ (Feature-based modules)
```

## Coding Guidelines & Best Practices

### Dart & Flutter Conventions
- Use `lowerCamelCase` for variables, functions, and parameters
- Use `UpperCamelCase` for classes, enums, and type definitions
- Use `snake_case` for library names and file names
- Follow Flutter widget naming conventions (StatelessWidget, StatefulWidget)
- Prefer `const` constructors where possible for performance
- Use `final` for immutable fields and variables

### State Management Patterns
- Use Provider pattern with ChangeNotifier for state management
- Implement proper dispose methods to prevent memory leaks
- Use Consumer and Selector widgets appropriately for rebuilds
- Keep UI logic separate from business logic

### Data Persistence & Privacy
- All user data must be stored locally using Isar database
- Never transmit personal journal content to external services
- Only send AI prompts (not personal data) to Gemini API
- Implement proper encryption for sensitive data like API keys
- Use proper error handling for database operations

### AI Integration Guidelines
- Structure Gemini API calls with proper error handling
- Implement timeout mechanisms for API requests
- Use RAG (Retrieval-Augmented Generation) for context awareness
- Generate and store vector embeddings locally for similarity search
- Follow CBT (Cognitive Behavioral Therapy) principles in AI responses

### Performance Considerations
- Optimize for mobile devices with limited resources
- Implement lazy loading for historical entries
- Use efficient vector operations for RAG processing
- Minimize memory usage in conversation handling
- Implement proper caching strategies for embeddings

### Error Handling & Resilience
- Implement comprehensive error handling for all API calls
- Handle network connectivity issues gracefully
- Provide meaningful error messages to users
- Implement retry mechanisms with exponential backoff
- Ensure app functionality when offline (except AI features)

### Testing Guidelines
- Write unit tests for business logic and data operations
- Create widget tests for UI components
- Mock external dependencies (Gemini API, database)
- Test error scenarios and edge cases
- Ensure privacy compliance in all test scenarios

### Security Best Practices
- Store API keys securely using flutter_secure_storage
- Implement proper data validation and sanitization
- Use HTTPS for all external API communications
- Ensure proper data encryption at rest
- Implement secure data export/import functionality

### UI/UX Guidelines
- Follow Material Design principles for Android
- Implement dark mode as the default theme
- Ensure mobile-first, touch-friendly interface design
- Maintain consistency in spacing, colors, and typography
- Implement proper accessibility features (semantics, contrast)

## Common Patterns & Anti-Patterns

### Recommended Patterns
- Repository pattern for data access abstraction
- Factory pattern for creating complex objects (embeddings, API clients)
- Observer pattern using Provider for state management
- Strategy pattern for different AI response types
- Command pattern for user actions (finish entry, cancel)

### Anti-Patterns to Avoid
- Storing personal data in SharedPreferences or insecure storage
- Making API calls directly from widgets
- Mixing UI logic with business logic
- Creating memory leaks by not disposing resources
- Hardcoding API keys or sensitive configuration
- Sending user personal data to external services

## Feature-Specific Guidelines

### Chat Interface
- Maintain single active conversation constraint
- Implement proper message threading and display
- Handle AI response streaming if available
- Provide clear loading states and error handling
- Implement message retry mechanisms

### Journal Management
- Support multiple journals with context separation
- Implement proper CRUD operations for journals
- Maintain chronological entry organization
- Support journal-specific AI persona adaptation

### RAG Implementation
- Generate embeddings for journal entry summaries
- Implement efficient vector similarity search
- Retrieve relevant context for AI responses
- Optimize embedding storage and retrieval performance
- Handle embedding generation errors gracefully

### Data Export/Import
- Implement secure data export in standard formats (JSON)
- Ensure complete data portability for users
- Handle large data sets efficiently
- Maintain data integrity during export/import operations

## Privacy & Compliance Considerations

### Data Handling
- Never log or store personal journal content in debug logs
- Ensure all personal data processing occurs locally
- Implement proper data deletion (right to be forgotten)
- Provide clear privacy policy and data handling explanations
- Maintain audit trails for data operations without storing content

### API Integration
- Only send AI prompts and system messages to external APIs
- Never include personal journal content in API requests
- Implement proper API key rotation and management
- Monitor API usage to prevent cost overruns
- Handle API rate limiting and quotas appropriately

## Development Workflow

### Code Quality
- Run `flutter analyze` before committing code
- Use `flutter format` for consistent code formatting
- Implement proper code documentation and comments
- Follow semantic versioning for releases
- Maintain changelog for feature updates

### Testing Strategy
- Aim for 80%+ code coverage on business logic
- Test all privacy-critical components thoroughly
- Implement integration tests for complete user flows
- Mock external dependencies for reliable testing
- Test offline functionality and error scenarios

### Performance Monitoring
- Monitor memory usage, especially for vector operations
- Track AI response times and optimize as needed
- Profile database operations for performance bottlenecks
- Monitor battery usage impact on mobile devices
- Track app startup and page load times

## Common Implementation Examples

### Making API Calls with Error Handling
```dart
try {
  final response = await _geminiClient.generateContent(prompt);
  return Right(response);
} catch (e) {
  if (e is NetworkException) {
    return Left(NetworkFailure());
  } else if (e is ApiException) {
    return Left(ApiFailure(e.message));
  } else {
    return Left(UnknownFailure());
  }
}
```

### Implementing Provider State Management
```dart
class ChatProvider extends ChangeNotifier {
  final List<ChatMessage> _messages = [];
  List<ChatMessage> get messages => List.unmodifiable(_messages);
  
  Future<void> sendMessage(String content) async {
    // Add user message
    _messages.add(ChatMessage(content: content, type: MessageType.user));
    notifyListeners();
    
    // Generate AI response with error handling
    try {
      final response = await _aiService.generateResponse(content);
      _messages.add(ChatMessage(content: response, type: MessageType.ai));
    } catch (e) {
      _messages.add(ChatMessage(
        content: 'Sorry, I encountered an error. Please try again.',
        type: MessageType.system
      ));
    }
    notifyListeners();
  }
}
```

### Database Operations with Isar
```dart
Future<void> saveEntry(JournalEntry entry) async {
  final isar = await db;
  await isar.writeTxn(() async {
    await isar.journalEntrys.put(entry);
  });
}

Future<List<JournalEntry>> getEntriesByJournal(int journalId) async {
  final isar = await db;
  return await isar.journalEntrys
    .filter()
    .journalIdEqualTo(journalId)
    .sortByCreatedAtDesc()
    .findAll();
}
```

When contributing to this project, always prioritize user privacy, maintain the local-only architecture, and ensure the app provides value through intelligent AI guidance while keeping all personal data secure on the user's device.