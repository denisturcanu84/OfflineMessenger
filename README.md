# Offline Messenger in C++

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Language](https://img.shields.io/badge/language-C%2B%2B-brightgreen.svg)

## Overview

Offline Messenger is a local network messaging application that functions similar to WhatsApp or Facebook Messenger, but operates within your local network. This application allows real-time chat between users with features specifically designed for offline message delivery.

## Features

- **Real-time Chat:** Communicate instantly with online users
- **Offline Messaging:** Send messages to users who are offline (delivered upon login)
- **Message Replies:** Reply to specific messages with reference
- **Chat History:** View your conversation history with any user
- **Online Status:** Check which users are currently active
- **Thread-Safe Design:** Handles multiple concurrent users efficiently

## How It Works

### Server Component

The server works like a post office by:
- Managing user online status
- Delivering messages immediately to online users
- Storing messages for offline users
- Maintaining conversation histories for all users

### Client Component

The client provides the user interface, allowing you to:
- Connect to the messaging server
- View incoming messages in real-time
- Send messages and execute commands
- Track messages for reply functionality

## Technical Implementation

### Network Communication

The application uses TCP (Transmission Control Protocol) for all communication because it provides:
- **Reliability:** Guarantees message delivery in correct order without data loss
- **Connection-based:** Maintains stable client-server connections
- **Error Checking:** Handles corrupted packets and retransmission
- **Flow Control:** Prevents overwhelming receivers with too much data

### Thread Safety

Multiple users can interact with the server simultaneously thanks to:
- **Regular Mutex:** For exclusively accessing critical resources
- **Shared Mutex:** Allows multiple readers but exclusive writers
- **Protected Resources:** Online users, message histories, message IDs, etc.

### Message Structure

```cpp
struct Message {
    string sender;       // Who sent the message
    string receiver;     // Who should receive it
    string content;      // The actual message
    int message_id;      // Unique ID for each message
    int reply_to_id;     // ID of message being replied to (-1 if not a reply)
};
```

### Data Storage

```cpp
// Server-side storage
unordered_map<string, queue<Message>> offline_messages;
unordered_map<string, vector<Message>> message_history;
unordered_map<string, int> logged_users;
unordered_map<int, string> socket_to_username;
```

## Command Reference

| Command | Description | Example |
|---------|-------------|---------|
| login \<username\> | Logs you in | `login denis` |
| logout | Logs you out | `logout` |
| message \<user\> \<text\> | Sends a message | `message denis Hello!` |
| reply \<id\> \<user\> \<text\> | Replies to a message | `reply 1 alex Hi back!` |
| gethistory \<user\> | Shows chat history | `gethistory denis` |
| whoisonline | Shows online users | `whoisonline` |
| whoami | Shows current user | `whoami` |
| exit | Disconnects the client | `exit` |
| help | Prints available commands | `help` |

## Examples

### Replying to Messages

```
[1][ALEX]: Hey, how's the weather there?

// to reply to this message:
> reply 1 alex It's sunny here!
Referenced message:
[1][ALEX]: Hey, how's the weather there?

Reply sent successfully
>
```

### Offline Messages

```
// Alex is offline when Denis sends a message
> message alex Hey, are you there?
User is offline. Message will be delivered when they log in.
>


// Later when Alex logs in:
> login alex
[OFFLINE MESSAGE][2][DENIS]: Hey, are you there?
>
```

### Chat History

```
> gethistory alex
Chat history with ALEX:
[1][ALEX]: Hey, how's the weather there?
[2][DENIS]: It's sunny here!
[3][ALEX]: Nice! Want to go for a walk?
>
```

## Future Improvements

### Security & Privacy
- End-to-End Encryption
- Message Expiration (self-destructing messages)

### Enhanced Messaging Features
- Group Chats
- File Sharing
- Message Edit/Delete
- Message Status (delivered/read)
- Message Search

### User Experience
- GUI Client
- Message Formatting (bold, italic, etc.)
- Emojis
- Desktop Notifications

### Technical Improvements
- Persistent Storage (database implementation)

## Tips and Tricks
- Usernames are case-insensitive
- Type 'help' anytime to see available commands

## License
This project is available under the MIT License.
