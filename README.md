# WebChat-ReactJs-FastAPI

## Chat application using ReactJS and FastAPI

## run backend
activate virtual environment : \venv\Scripts\activate
uvicorn main:app
## run frontend
npm install
npm run server

### Frontend Code (React)

1. **State Management**:
   - `clientId` represents a unique identifier generated based on the current timestamp.
   - `websckt` stores the WebSocket connection.
   - `message` stores the current user input message.
   - `messages` holds an array of chat messages exchanged between the user and the chatbot.

2. **WebSocket Connection Setup** (`useEffect`):
   - A WebSocket connection is established with the URL "ws://localhost:8000/ws/" + clientId.
   - On open, the client sends a "Connect" message to the server.
   - `onmessage` listens for incoming messages and appends them to the `messages` state.

3. **Sending and Receiving Messages**:
   - When the user sends a message, the `sendMessage` function sends the message to the server using the WebSocket connection.
   - The `onmessage` event handler for the WebSocket listens for responses and appends them to the `messages` state.

4. **Message Display**:
   - Messages are displayed in the `chat` container. If the message's `clientId` matches the current `clientId`, it's displayed as a user message; otherwise, it's displayed as a chatbot message.

### Backend Code (WebSocket Server):-

### Importing Dependencies

- `from typing import List`: Importing the `List` type for type hints.
- `from fastapi import FastAPI, WebSocket, WebSocketDisconnect`: Importing necessary classes from FastAPI for creating the FastAPI app, WebSocket handling, and handling WebSocket disconnections.
- `from fastapi.middleware.cors import CORSMiddleware`: Importing the `CORSMiddleware` for enabling Cross-Origin Resource Sharing (CORS).
- `from datetime import datetime`: Importing `datetime` for managing timestamps.
- `import json`: Importing the `json` module for JSON handling.

### Creating FastAPI App and Enabling CORS

- Creating a FastAPI app instance: `app = FastAPI()`
- Adding `CORSMiddleware` to the app to allow cross-origin requests.

### ConnectionManager Class

A class named `ConnectionManager` is defined to manage WebSocket connections.

- `__init__(self)`: Constructor initializes the `active_connections` list to store active WebSocket connections.

- `connect(self, websocket: WebSocket)`: This method is called when a new WebSocket connection is established. It accepts the WebSocket connection, sends an acceptance response, and adds the connection to the `active_connections` list.

- `disconnect(self, websocket: WebSocket)`: This method is called when a WebSocket disconnects. It removes the connection from the `active_connections` list.

- `send_personal_message(self, message: str, websocket: WebSocket)`: Sends a personal message to a specific WebSocket connection.

- `broadcast(self, message: str)`: Broadcasts a message to all active WebSocket connections.

### Defining Endpoints

- `@app.get("/")`: Defines a route for the root URL ("/"). Returns a simple welcome message.

- `@app.websocket("/ws/{client_id}")`: Defines a WebSocket endpoint with a parameter `client_id`. This is where WebSocket communication takes place.

### WebSocket Handling

Inside the `websocket_endpoint` function:

- `await manager.connect(websocket)`: Establishes the WebSocket connection and adds it to the list of active connections.
- `now = datetime.now()`: Gets the current timestamp.
- `try` block: A loop to receive messages from the WebSocket connection until it's disconnected.
  - `data = await websocket.receive_text()`: Waits for and receives a text message from the WebSocket.
  - `message`: Constructs a dictionary with `time`, `clientId`, and `message`.
  - `await manager.broadcast(json.dumps(message))`: Broadcasts the message to all active WebSocket connections.
- `except WebSocketDisconnect`: Handles WebSocket disconnection by removing the WebSocket from the list and broadcasting an "Offline" message.

This code essentially creates a WebSocket server that can handle connections, disconnections, and message broadcasting for real-time communication between clients and the server. Messages sent by clients are broadcasted to all connected clients, allowing for a chat-like communication pattern.
