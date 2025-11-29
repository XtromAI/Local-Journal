# **Local Journal: Initial Concept Document**

> **Note:** This document represents the initial project concept and early requirements gathering. For the complete and current specifications, please refer to the [Business Requirements](business-requirements.md), [Product Requirements](product-requirements.md), and [Technical Requirements](technical-requirements.md) documents.

---

## **Original Project Requirements**

## **1\. Project Overview**

The goal is to create a client-side, journaling application with a chat-based interface. Users will interact with an AI to explore their thoughts and feelings.

## **2\. Core Features**

### **AI Chat Interface**

* **Conversation-Based Journaling**: The central view is a chat window. The AI acts as a conversational guide, helping users delve into their thoughts.  
* **Conversation Flow**: Users can have **multiple active, unfinished entries (drafts) at any given time.** New conversations can be started and saved as drafts. A conversation is considered finalized when the AI generates a summary. Once a chat is finalized, it cannot be resumed. **Only finalized entries are searchable and part of the RAG system.**  
* **AI Persona**: The AI will adopt a supportive persona based on Cognitive Behavioral Therapy (CBT) principles. It should guide the user toward deeper introspection and, when relevant, provide insights into trends and patterns based on past entries. At the end of each session, it will generate a concise summary. The summaries will be written from the AI's perspective and address the user directly as "you" instead of "user."  
* **Gemini API Integration**: The application will use the Gemini API for all AI responses and summary generation.  
* **User Interaction**: The chat interface includes a text input and three buttons:  
  * **"Go Deeper"**: Submits the user's message to continue the conversation.  
  * **"Finish Entry"**: Triggers the AI to generate a summary. This summary will be the final message of the conversation, completing the entry and saving it. This action requires a **modal confirmation dialog**.  
  * **"Cancel"**: Resets the current chat entry without saving. This action requires a **modal confirmation dialog**.

### **Journal Entry Management**

* **Automatic Entry Creation**: A new journal entry is automatically created for each finished conversation, with the AI-generated summary as its core content. This summary, along with the full conversation, will be stored for historical review and RAG purposes.  
* **Multiple Journals**: Users can create multiple journals for the separation of concerns, for example, a "Work" journal and a "Personal" journal.  
* **Historical View**: Users can view a chronological list of their past journal entries in a collapsible panel.  
* **Resuming Conversations**: The app must allow users to load and continue any unfinished draft conversation. However, finalized conversations cannot be resumed.  
* **Entry Accessibility**: Previous entries must be accessible from a collapsible panel on the left side of the UI, similar to a chat assistant interface.

### **User Interface (UI)**

* **Single-Page Application**: The entire app, including the chat and historical entries, will be contained within a single page.  
* **Chat Layout**: The chat interface should be responsive and clean, with distinct styling for user and AI messages.  
* **Mobile-First Design**: The UI and interactive elements are optimized for mobile, ensuring a seamless experience on touch devices.  
* **Dark Mode**: The application will use a dark mode theme.  
* **Confirmation Dialogs**: All modal confirmation dialogs must be designed and implemented within the application.

### **Retrieval-Augmented Generation (RAG)**

* The AI will be able to reference and incorporate information from previous **finalized** journal entries to provide more relevant responses and insights into trends, growth, and patterns. **Draft entries are not included in RAG until they are finalized.**  
* **High-Level RAG Plan**:  
  * **1\. Embedding Generation:** When a user finalizes a conversation, the application will use the Gemini API's embedContent endpoint to generate a vector embedding of the entry's summary.  
  * **2\. Vector Search:** When the user sends a new message, the application will generate an embedding for that message and perform an in-memory vector similarity search (using cosine similarity) against the stored summary embeddings from **finalized entries only** to find the most relevant past entries.  
  * **3\. Context Injection:** The summaries of the most relevant past **finalized** entries will be included in the prompt sent to the Gemini API, providing the AI with additional context to formulate its response.  
* All retrieval and processing will be handled on the client-side.

## **3\. Technology Stack**

* **Flutter**: The application will be built using the Flutter framework.  
* **Dart**: The programming language for all client-side logic, including UI rendering, event handling, and API calls.  
* **Local Database**: **Isar** will be used as the local, on-device database for persistent data storage.

## **4\. Technical Constraints**

* **No Server-Side Components**: The application is entirely client-side.  
* **Authentication**: The app will rely solely on device security for data protection, with no built-in PIN or password required.  
* **API Key Management**: The application will not contain the API key in its source code. Instead, the user will be prompted to enter the key upon first use, and it will be stored in local storage to avoid repeated prompts.  
* **Error Handling**: All errors, such as network failures or invalid API issues, will be displayed as a message within the chat interface.