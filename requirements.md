# Personal Growth Journal App Requirements

## 1. Project Overview

The goal is to create a client-side, single-page journaling application. The core functionality will be a chat-based interface where a user can interact with an AI to explore their thoughts and feelings. All user data, including journal entries, must be stored exclusively on the user's device and never leave the client. The application will leverage the Gemini API to power the AI conversational aspect.

## 2. Core Features

### AI Chat Interface

* **Conversation-Based Journaling**: The main view of the app is a chat window. The user sends messages, and the AI responds, acting as a conversational guide.
* **Conversation Flow**: The user can start a new conversation at any time. They should also be able to select and resume any previous, unfinished conversation. Once a conversation is finished and a summary is generated, it cannot be resumed.
* **AI Persona**: The AI should act as a supportive guide with a tone rooted in Cognitive Behavioral Therapy (CBT). Its role is to help the user dive deeper into their thoughts and feelings. At the end of a conversation, the AI should generate a brief summary of the entry.
* **Gemini API Integration**: The app must make API calls to the Gemini model to generate AI responses and summaries.
* **User Input**: The chat interface must include a text input field for the user to type their message. Below the input, there will be two buttons: "Go Deeper" and "Finish Entry."
  * **"Go Deeper"**: This button submits the user's message to the AI, continuing the conversation.
  * **"Finish Entry"**: This button prompts the AI to generate a summary of the current conversation. Once the summary is received, the conversation is considered finished and will be saved as a journal entry.

### Journal Entry Management

* **Automatic Entry Creation**: Each complete conversation or session with the AI should be saved as a single journal entry.
* **Metadata**: Each entry must include basic metadata: a timestamp of creation and the AI-generated summary.
* **Historical View**: The user must be able to view a chronological list of their past journal entries.
* **Entry Viewing**: Tapping on a historical entry should load the full conversation for review.
* **Resuming Conversations**: The user should be able to resume a conversation that has not been finished.

### User Interface (UI)

* **Single-Page Application**: The entire experience (chat, historical entries) should exist within a single HTML file with CSS and JavaScript.
* **Chat Layout**: The chat interface should be clean and responsive, resembling a modern messaging app with user and AI messages clearly distinguished.
* **Mobile-First Design**: The layout and interactive elements must be optimized for mobile devices, including touch-friendly buttons and a responsive viewport.
* **Dark Mode**: The entire user interface will have a dark mode theme.

## 3. Technology Stack

* **HTML**: For the application structure.
* **JavaScript**: For all client-side logic, including UI rendering, event handling, data storage, and API calls.
* **CSS**: For styling, with a focus on clean, accessible, and responsive design.

## 4. Data Storage

* **Client-Side Only**: All journal entries and related data must be stored locally on the user's device.
* **Mechanism**: localStorage should be used as the primary data persistence mechanism. Each journal entry object should be serialized to a JSON string before being saved.

## 5. Technical Constraints

* **Single File**: The entire application must be contained within a single index.html file. All CSS should be in a `<style>` tag and all JavaScript in a `<script>` tag.
* **No Server-Side Components**: The application will not have a backend server. All logic runs in the user's browser.
* **API Key Management**: The API key will be managed locally. Given the single-file constraint, the simplest method is to have the user copy and paste their API key into a dedicated variable in the JavaScript section of the index.html file.
* **No Authentication**: The app will rely solely on device security for data protection, with no built-in PIN or password required.
* **Error Handling**: All errors, such as network failures or invalid API keys, must be displayed as a message directly within the chat interface, similar to a regular chat message.
                              