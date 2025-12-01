# **Local Journal: Requirements Document**

## **1. Problem Statement**

### **1.1 Problem**
Traditional journaling lacks guidance and insight generation, while cloud-based AI solutions raise data security concerns for personal thoughts. Users need an accessible tool for self-reflection that provides intelligent guidance while keeping their private thoughts completely under their control.

### **1.2 Solution**
A client-side AI-powered journaling application that:
- Provides AI-guided self-reflection based on Cognitive Behavioral Therapy (CBT) principles
- Stores all data locally on the user's device
- Uses Retrieval-Augmented Generation (RAG) to provide personalized insights from past entries
- Works offline after initial setup

## **2. Functional Requirements**

### **2.1 User Onboarding**
- Streamlined initial setup process
- Privacy explanation to build trust
- Progressive feature introduction

### **2.2 Conversation Management**
- Allow multiple draft conversations for flexible journaling
- Guided conversation flow leading to actionable insights
- Clear session completion with summary generation
- Prevention of conversation abandonment through confirmation dialogs

### **2.3 Historical Intelligence**
- Access to previous entries for context and continuity
- AI-powered trend identification and progress tracking
- Personalized insights based on historical data

### **2.4 Data Export & Portability**
- Complete data export functionality covering all journals, entries, and settings
- Standard format output (JSON/CSV) for interoperability and backup
- Local-only export process maintaining privacy principles
- User-initiated export from settings interface
- Clear export completion feedback and file location guidance

### **2.5 Multi-Context Organization**
- Multiple journal creation (work, personal, health, etc.)
- Context separation to maintain focus and organization
- Cross-journal pattern recognition where appropriate

## **3. User Experience Requirements**

### **3.1 Accessibility & Usability**
- Mobile-first design for on-the-go access
- Dark mode for comfortable extended use
- Intuitive single-page interface
- Minimal learning curve
- Rich Text Support: Support for Markdown rendering and Emojis

### **3.2 Trust & Security**
- Local-only data processing and storage
- Clear privacy policy and data handling explanation
- No external data transmission of personal content
- User control over API key management

## **4. Non-Functional Requirements**

### **4.1 Performance**
- Response time under 3 seconds for AI responses
- Instant loading of historical entries
- Efficient local search and RAG processing
- Minimal battery impact on mobile devices

### **4.2 Reliability**
- High app stability (no crashes)
- Reliable local data persistence
- Graceful handling of network issues
- Automatic data backup to device storage

### **4.3 Security**
- No personal data transmission to external servers
- Secure local storage of user data
- API key protection and management

## **5. Constraints & Dependencies**

### **5.1 Technical Constraints**
- Client-side only: All processing must occur locally
- API Dependency: Reliance on Gemini API availability
- Device Storage: Limited by user device storage capacity
- Platform Support: Flutter framework capabilities

### **5.2 Dependencies**
- Gemini API: Stable API access for AI functionality
- Continued improvement in edge AI capabilities
