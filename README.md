# 💬 ChatRoomCpp - Modern C++ Chat Server

![Boost.Asio](https://img.shields.io/badge/Boost.Asio-1.82.0-blue)
![C++17](https://img.shields.io/badge/C++-17-blue)
![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Windows%20%7C%20macOS-lightgrey)

A high-performance, asynchronous chat server built with modern C++ leveraging Boost.Asio for network operations and thread-safe design patterns.

## 🌟 Key Features

- **Real-time group messaging** with low latency
- **Non-blocking I/O** using Boost.Asio's event loop
- **Thread-safe architecture** for concurrent client handling
- **Binary message protocol** with length-prefixed framing
- **Scalable room management** supporting 100+ concurrent users

## 🏗️ System Architecture

### Core Components

| Component       | Responsibilities                              | Key Methods                      |
|-----------------|-----------------------------------------------|----------------------------------|
| **`Session`**   | Client connection lifecycle, message I/O      | `async_read()`,`async_write()`   |
| **`Room`**      | Participant management, message broadcasting  | `join()`,`leave()`,`deliver()`   |
| **`Message`**   | Protocol formatting, header/body handling     | `encodeHeader()`,`decodeHeader()`|

### Class Relationships
@startuml

interface Participant {
    +deliver(Message& message)
    +write(Message& message)
}

class Session {
    -tcp::socket clientSocket
    -boost::asio::streambuf buffer
    -Room& room
    -std::deque<Message> messageQueue
    +Session(tcp::socket s, Room& room)
    +start()
    +deliver(Message& message)
    +write(Message& message)
    +async_read()
    +async_write(std::string messageBody, size_t messageLength)
}

class Room {
    -std::deque<Message> messageQueue
    -std::set<ParticipantPointer> participants
    +join(ParticipantPointer participant)
    +leave(ParticipantPointer participant)
    +deliver(ParticipantPointer participant, Message& message)
}

class Message {
    -char data[header + maxBytes]
    -size_t bodyLength_
    +Message()
    +Message(std::string message)
    +getData(): std::string
    +getBody(): std::string
    +getNewBodyLength(size_t newLength): size_t
    +encodeHeader()
    +decodeHeader(): bool
    +getBodyLength(): size_t
    +printMessage()
}

class Server {
    -boost::asio::io_context io_context
    -tcp::acceptor acceptor
    -Room room
    +main(int argc, char* argv[])
    +accept_connection(io_context, port, acceptor, room, endpoint)
}

class Client {
    -boost::asio::io_context io_context
    -tcp::socket socket
    -std::thread input_thread
    +main(int argc, char* argv[])
    +async_read(tcp::socket& socket)
}

class boost_asio_io_context {
    +run()
    +post(function)
}

class Thread_Model {
    <<concept>>
    +IO Thread: Runs io_context.run()
    +Input Thread: Handles user input
    +Async Callbacks: Run on IO Thread
}

class MessageFlow {
    <<concept>>
    +Client Input → Socket Write
    +Socket Read → Message Parse → Room Broadcast
    +Room Broadcast → Session Queue → Socket Write
}

class AsyncPattern {
    <<concept>>
    +async_read_until(delimiter)
    +async_write(buffer)
    +post(function)
    +Completion Handlers
}

Participant <|-- Session
Session *-- Room : has-reference
Session o-- Message : manages-in-queue
Room o-- Message : manages-in-queue
Room o-- Participant : contains
Server ..> Session : creates
Server ..> Room : creates
Client ..> Message : sends/receives
Server ..> boost_asio_io_context : uses
Client ..> boost_asio_io_context : uses
Client -- Thread_Model : implements
Session -- AsyncPattern : implements
Message -- MessageFlow : participates-in

note right of MessageFlow
Message path:
1. Client input → Client socket
2. Server reads from socket
3. Server creates Message
4. Room broadcasts to participants
5. Each Session queues message
6. Session writes to client socket
end note

note right of Thread_Model
Thread architecture:
1. Main thread runs io_context
2. Client has dedicated input thread
3. All async operations handled by io_context
4. Socket operations are non-blocking
end note

note right of AsyncPattern
Asynchronous operations:
1. async_read_until() with completion handler
2. async_write() with completion handler
3. Handlers capture shared_from_this()
4. post() for thread-safe socket writes
end note

@enduml




   
