# **Local Journal: Product Requirements Document (PRD)**

## **1. Product Overview**

### **1.1 Product Vision**
Local Journal is an AI-powered journaling application that transforms traditional journaling into an intelligent, conversational experience while maintaining complete user privacy through local-only processing.

### **1.2 Core Principles**
- **Privacy-First**: All data remains on the user's device
- **Intelligent Guidance**: AI companion based on CBT principles
- **Personalized Experience**: Context-aware responses through RAG technology

## **2. User Stories & Use Cases**

### **2.1 Primary User Workflows**

#### **Epic 1: First-Time User Onboarding**
**As a new user, I want to quickly understand and start using the app so that I can begin my journaling journey immediately.**

**User Stories:**
- As a new user, I want to see a clear explanation of privacy benefits so that I feel secure about sharing personal thoughts
- As a new user, I don't want to enter my API key right away so that I can start using AI features without technical complexity
- As a new user, I want a guided first conversation so that I understand how AI journaling works
- As a new user, I want to see my first summary generated so that I understand the value of the completion process

**Acceptance Criteria:**
- Privacy explanation appears before any data entry
- API key setup is not required for first journal entry, and when needed there should be clear instructions
- First session demonstrates AI guidance capabilities within 5 minutes
- User completes their first journal entry with summary within 10 minutes

#### **Epic 2: Daily Journaling Experience**
**As a regular user, I want to have meaningful conversations with my AI companion so that I can gain insights into my thoughts and emotions.**

**User Stories:**
- As a user, I want to start a new journal conversation so that I can process my current thoughts
- As a user, I want the AI to ask thoughtful follow-up questions so that I can explore my thoughts more deeply
- As a user, I want to see my conversation history in the current session so that I maintain context
- As a user, I want to finish my entry with an AI-generated summary so that I can capture key insights
- As a user, I want confirmation before ending or canceling so that I don't lose my progress accidentally

**Acceptance Criteria:**
- Multiple draft conversations supported for flexible journaling
- AI responses appear within 3 seconds
- Conversation history displays chronologically within session
- Summary generation triggered by explicit user action with confirmation
- Cancel action requires confirmation and clears unsaved data

#### **Epic 3: Historical Insights & Growth Tracking**
**As a user with multiple entries, I want to see patterns and insights from my past journaling so that I can track my personal growth.**

**User Stories:**
- As a user, I want to view my previous journal entries so that I can reflect on my journey
- As a user, I want the AI to reference relevant past entries so that I receive personalized guidance
- As a user, I want to see how my thoughts and patterns evolve over time so that I can measure growth
- As a user, I want to search through my past entries so that I can find specific topics or insights

**Acceptance Criteria:**
- Historical entries accessible from collapsible side panel
- AI incorporates relevant past context into current conversations
- Entries display chronologically with dates and summaries
- Search functionality works across all journal entries

#### **Epic 4: Multi-Context Organization**
**As a user with different life areas, I want to organize my journaling by context so that I can maintain focus and separation.**

**User Stories:**
- As a user, I want to create multiple journals (work, personal, health) so that I can separate different aspects of my life
- As a user, I want to switch between journals easily so that I can journal about the relevant context
- As a user, I want each journal to maintain its own conversation history so that context remains relevant
- As a user, I want the AI to understand the journal context so that it provides appropriate guidance

**Acceptance Criteria:**
- Users can create and name multiple journals
- Journal switching is accessible from main interface
- Each journal maintains separate entry history
- AI responses adapt to journal context and theme

### **2.2 Secondary User Workflows**

#### **Epic 5: Privacy & Security Management**
**As a privacy-conscious user, I want complete control over my data so that I feel secure using the app.**

**User Stories:**
- As a user, I want all my data stored locally so that my thoughts never leave my device
- As a user, I want to manage my API key so that I control access to AI services
- As a user, I want to export my data so that I can backup or migrate my journals
- As a user, I want to delete specific entries so that I can remove content I no longer want
- As a user, I want to delete all my data if I decide I no longer want it

**Acceptance Criteria:**
- Zero personal data transmitted to external servers (except API calls)
- API key stored securely and user-manageable
- Export functionality covers all user data (journals, entries, settings, conversation history)
- Export generates standard format files (JSON primary, CSV option for entries)
- Export process is fully local with no external data transmission
- Entry deletion with confirmation and permanent removal
- All data deletion with double confirmation and permanent removal

#### **Epic 6: Accessibility & Usability**
**As a mobile user, I want a seamless experience across devices so that I can journal whenever inspiration strikes.**

**User Stories:**
- As a mobile user, I want the interface optimized for touch so that I can use it comfortably
- As a user in low-light conditions, I want dark mode so that I can journal comfortably
- As a user with different device sizes, I want responsive design so that the app works on my device
- As a user who journals regularly, I want quick access to start new entries so that I can capture thoughts immediately

**Acceptance Criteria:**
- Touch-optimized interface with appropriate button sizes
- Light, Dark, or System made options available with consistent theming 
- Responsive design works on phones, tablets, and desktop
- Access to draft and finalized entries from collapsible side panel

## **3. Feature Specifications**

### **3.1 Core Features**

#### **3.1.1 AI Chat Interface**
**Feature Description:** Conversational journaling interface with intelligent AI guidance

**User Interface Elements:**
- Chat window with message history
- Multi line text input field for user messages
- Three action buttons: "Go Deeper", "Finish Entry", "Cancel"
- Message styling distinguishing user vs. AI messages
- **Rich Text Support**: Rendering of Markdown formatting (bold, lists, code blocks) and Emojis
- Typing indicators for AI processing
- Streaming response content builds message as it arrives

**Functional Requirements:**
- Multiple draft conversation support with easy switching
- Real-time message display and history
- AI response generation using Gemini API
- CBT-based guidance and questioning
- Summary generation on session completion

**User Experience Requirements:**
- Intuitive chat flow similar to messaging apps
- Clear visual distinction between user and AI messages
- Responsive design for mobile and desktop
- Loading states for AI processing

#### **3.1.2 Journal Entry Management**
**Feature Description:** Automatic creation and organization of completed journal entries

**User Interface Elements:**
- Collapsible historical entries panel
- Entry list with dates, titles, and preview summaries
- Entry detail view with full conversation and summary
- Journal selection dropdown
- Search functionality within journal across entries

**Functional Requirements:**
- Automatic entry creation upon conversation completion
- Entry storage with full conversation + summary
- Chronological organization with metadata
- Multiple journal support with context separation
- Local search across all entries

**User Experience Requirements:**
- Quick access to historical insights
- Clean, scannable entry list design
- Smooth transitions between entry views
- Contextual organization by journal type

#### **3.1.3 Retrieval-Augmented Generation (RAG)**
**Feature Description:** AI context awareness using past journal entries for personalized responses

**User Interface Elements:**
- Invisible to user (background processing)
- Optional context indicators showing AI is referencing past insights
- Subtle visual cues when AI incorporates historical context

**Functional Requirements:**
- Vector embedding generation for entry summaries
- Similarity search against historical entries
- Context injection into AI prompts
- Relevance scoring and selection of past entries
- Local-only processing and storage

**User Experience Requirements:**
- Seamless integration without user complexity
- Improved AI response quality over time
- No additional user actions required
- Transparent operation with optional visibility
- Ability to turn off RAG feature at individual journal level

#### **3.1.4 Privacy & Security Architecture**
**Feature Description:** Complete local data processing with no external personal data transmission

**User Interface Elements:**
- Privacy explanation during onboarding
- API key management interface
- Data export functionality
- Clear privacy policy and data handling information

**Functional Requirements:**
- Local-only data storage using Isar database
- API key secure storage and management
- No personal data in API calls (only prompts)
- Data export in standard formats
- Secure deletion capabilities

**User Experience Requirements:**
- Clear privacy benefits communication
- Simple API key setup process
- Transparent data handling practices
- User control over all personal information

#### **3.1.5 Data Export Feature**
**Feature Description:** Complete data export functionality for user data portability and backup

**User Interface Elements:**
- Export button accessible from settings/menu
- Export options dialog (JSON format, CSV format for entries)
- Progress indicator during export generation
- Success notification with file location
- Export confirmation dialog with privacy assurance

**Functional Requirements:**
- Export all user data: journals, entries, conversation history, settings
- Generate structured JSON file with complete data model
- Optional CSV export for journal entries for spreadsheet compatibility  
- Local file system write with user-controlled location
- Export process validation and integrity checking
- No external data transmission during export

**User Experience Requirements:**
- One-click export initiation from settings
- Clear progress feedback during export generation
- Success confirmation with file access guidance
- Privacy assurance messaging throughout process
- Error handling with helpful troubleshooting guidance

### **3.2 User Experience Features**

#### **3.2.1 Onboarding & First Use**
**Feature Description:** Guided introduction optimizing first-time user success

**Components:**
- Welcome screen with privacy explanation
- API key setup with clear instructions
- Guided first conversation with example interactions
- Feature introduction without overwhelming complexity

#### **3.2.2 Mobile-First Design**
**Feature Description:** Touch-optimized interface prioritizing mobile experience

**Components:**
- Large, touchable buttons and interface elements
- Swipe gestures for navigation
- Keyboard-aware interface layouts
- Thumb-friendly navigation patterns

#### **3.2.3 Dark Mode Theme**
**Feature Description:** Comfortable viewing experience for extended use

**Components:**
- Consistent dark color scheme across all interfaces
- High contrast text for readability
- Subtle UI elements that don't distract from content
- Eye-strain reduction for extended journaling sessions

### **3.3 Advanced Features**

#### **3.3.1 Multiple Journal Support**
**Feature Description:** Separate journals for different life contexts and goals

**Components:**
- Journal creation and naming interface
- Context switching between journals
- Separate entry histories per journal
- Journal-specific AI personality adaptation

#### **3.3.2 Pattern Recognition & Insights**
**Feature Description:** AI-powered identification of growth patterns and trends

**Components:**
- Trend identification across entries
- Growth pattern visualization
- Personalized insights based on historical data
- Progress tracking over time

## **4. Technical Product Specifications**

### **4.1 Platform Requirements**

#### **4.1.1 Flutter Application**
- **Target Platforms:** iOS, Android, Web, Desktop
- **Minimum OS Support:** iOS 12+, Android API 21+, Modern web browsers
- **Framework:** Flutter 3.x with Dart 3.x
- **Local Database:** Isar for high-performance local storage

#### **4.1.2 AI Integration**
- **API Provider:** Google Gemini API
- **Processing:** Client-side only, no server components
- **Embedding Generation:** Gemini embedContent endpoint
- **Response Generation:** Gemini generateContent endpoint

#### **4.1.3 Data Architecture**
- **Storage:** Local device storage only
- **Database:** Isar for structured data and vector storage
- **Backup:** User-initiated export functionality
- **Sync:** None - single device per installation

### **4.2 Performance Requirements**

#### **4.2.1 Response Times**
- **AI Response Generation:** Under 3 seconds
- **App Launch:** Under 2 seconds to main interface
- **Historical Entry Loading:** Under 1 second
- **Search Results:** Under 0.5 seconds

#### **4.2.2 Reliability**
- **App Stability:** 99%+ crash-free sessions
- **Data Persistence:** 100% local data retention
- **Offline Capability:** Full functionality except AI responses
- **Error Recovery:** Graceful handling of network/API issues

#### **4.2.3 Resource Usage**
- **Battery Impact:** Minimal background processing
- **Storage Efficiency:** Optimized local data storage
- **Memory Usage:** Efficient management of conversation history
- **Network Usage:** Only for AI API calls

### **4.3 Security & Privacy Specifications**

#### **4.3.1 Data Protection**
- **Local Storage:** All personal data remains on device
- **API Communication:** Only prompts sent, no personal data
- **Key Management:** Secure local storage of API keys
- **Data Encryption:** Local database encryption for sensitive data

#### **4.3.2 Compliance**
- **GDPR Compliance:** By design through local-only processing
- **Privacy Regulations:** Adherence to global data protection laws
- **User Rights:** Complete data control and deletion capabilities
- **Transparency:** Clear privacy policy and data handling explanation

## **5. User Acceptance Criteria**

### **5.1 Core Functionality Acceptance**

#### **5.1.1 Conversation Flow**
- [ ] User can start new journal conversations and manage multiple drafts
- [ ] Multiple draft conversations supported for flexible journaling
- [ ] AI responds with contextually appropriate CBT-based guidance
- [ ] User can continue conversation with "Go Deeper" button
- [ ] Conversation completion requires explicit "Finish Entry" action
- [ ] Summary generation occurs within 5 seconds of completion request
- [ ] Confirmation dialogs prevent accidental data loss

#### **5.1.2 Historical Access**
- [ ] All completed entries accessible from side panel
- [ ] Draft conversations clearly separated from finalized entries
- [ ] Entries display chronologically with clear date/time stamps
- [ ] Full conversation and summary viewable for each entry
- [ ] Search functionality finds relevant past finalized entries
- [ ] AI incorporates relevant historical context only from finalized entries in new conversations

#### **5.1.3 Multi-Journal Organization**
- [ ] Users can create and name multiple journals
- [ ] Journal switching maintains separate conversation histories
- [ ] AI responses adapt to journal context and purpose
- [ ] Each journal maintains its own entry chronology

#### **5.1.4 Data Export Functionality**
- [ ] Export button accessible from settings interface
- [ ] Export includes all user data: journals, entries, conversation history, settings
- [ ] JSON export format maintains complete data structure and relationships
- [ ] CSV export option available for journal entries
- [ ] Export process completes locally with no external data transmission
- [ ] Export generates valid, importable file formats
- [ ] Success notification provides clear file location and access guidance
- [ ] Export process includes data validation and integrity verification

### **5.2 User Experience Acceptance**

#### **5.2.1 Mobile Experience**
- [ ] All interface elements easily tappable on mobile devices
- [ ] Text input works smoothly with mobile keyboards
- [ ] Interface adapts to different screen sizes and orientations
- [ ] Gestures work intuitively for navigation and interaction

#### **5.2.2 Privacy & Trust**
- [ ] Privacy explanation clearly communicates local-only processing
- [ ] API key setup is straightforward with clear instructions
- [ ] No personal data transmitted beyond necessary API prompts
- [ ] Users can export their data in usable formats
- [ ] Data deletion removes information completely and permanently

#### **5.2.3 Performance & Reliability**
- [ ] App launches and loads historical data quickly
- [ ] AI responses appear within acceptable time limits
- [ ] App handles network issues gracefully without data loss
- [ ] Battery usage remains minimal during typical usage patterns

## **6. Implementation Phases**

### **6.1 Phase 1: Core Functionality**
**Features:**
- Multiple journal support with draft and finalized entry management
- Basic AI responses with CBT principles
- Local storage and entry management
- Mobile-optimized UI with dark mode
- Privacy-first architecture foundation

### **6.2 Phase 2: Intelligence Integration**
**Features:**
- Vector embedding and similarity search
- Historical context integration
- Pattern recognition and insights
- Enhanced AI persona with deeper CBT integration
- Advanced entry management and search

### **6.3 Phase 3: Advanced Features**
**Features:**
- Multiple journal creation and management
- Advanced analytics and trend visualization
- Performance optimization and polish
- Enhanced user experience features
- Platform expansion (desktop, web)
