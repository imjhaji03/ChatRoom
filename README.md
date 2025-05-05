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



## 📂 Project Structure

    ChatRoomCpp/
    ├── include/
    │   ├── chatRoom.hpp
    │   └── message.hpp
    ├── src/
    │   ├── chatRoom.cpp
    │   ├── client.cpp
    │   └── server.cpp
    ├── test/
    └── CMakeLists.txt

## 🔄 Message Flow
    Client Input → write() to socket
    
    Server → async_read() parses message
    
    Room → broadcasts Message to all Sessions
    
    Session → queues Message for async write
    
    Socket → delivers to recipient clients

## 🧵 Thread Model
    IO Thread: Runs io_context.run() for async operations
    
    Input Thread: Handles user typing (on client)
    
    Async Callbacks: All async handlers run on IO thread
    
    Thread Safety: Guaranteed using post() and queueing logic


   
