# Simple File Server Packet Handling Explanation

## Overview

The Simple File Server in this code facilitates communication between clients and the server using packets. Packets are units of data that contain information exchanged between the server and clients. This document provides an overview of how packets are handled in the Simple File Server. (Example code is provided [here](ExampleInfo.md))

## Packet Structure

Packets in this Simple File Server are in the form of JSON-encoded messages. Each message contains a `mode` property that indicates the purpose or type of the message. The server processes different modes, such as "grab," "send," "dir," "delete," and "rename," to handle various client requests.

## Packet Handling Process

### Connection Establishment

When a client connects to the Simple File Server (`wss.on("connection", ...)`), the server logs the connection and sets up event listeners for incoming messages (`ws.on("message", ...)`).

### Message Reception and Processing

1. **Parsing the Message**: The server parses incoming JSON messages from clients to extract relevant data.

2. **Handling Different Modes**:

   - **Mode "grab" Request**:

     ```json
     {
       "mode": "grab",
       "user": "username",
       "name": "file.txt"
     }
     ```

     - Retrieves the content of the specified file and sends it back to the client as a base64-encoded URL.

   - **Mode "send" Request**:

     ```json
     {
       "mode": "send",
       "user": "username",
       "name": "file.txt",
       "url": "data:application/octet-stream;base64,SGVsbG8gd29ybGQhCg=="
     }
     ```

     - Saves a file on the server based on the client's request, either from a base64-encoded data URL or a remote URL.

   - **Mode "dir" Request**:

     ```json
     {
       "mode": "dir",
       "user": "username"
     }
     ```

     - Retrieves the list of files in the specified user directory on the server and sends it back to the client.

   - **Mode "delete" Request**:

     ```json
     {
       "mode": "delete",
       "user": "username",
       "name": "file.txt"
     }
     ```

     - Deletes the specified file and removes the associated directory if empty.

   - **Mode "rename" Request**:

     ```json
     {
       "mode": "rename",
       "user": "username",
       "name1": "old_file.txt",
       "name2": "new_file.txt"
     }
     ```

     - Renames the specified file.

3. **Sending Responses**:

   - **Successful file operation response**:

     ```json
     {
       "message": "File sent successfully"
     }
     ```

   - **Failure response**:

     ```json
     {
       "message": "File sending failed"
     }
     ```

   - **Mode "grab" Response with file data**:

     ```json
     {
       "url": "data:application/octet-stream;base64,UEsDBBQAAAAIAIqDp0hMpwcAAAAQAAAALAAAAbWltZXR5cGUuZGF0Vk1RvgP4VWdbE
       ... (base64-encoded file content) ..."
     }
     ```

   - **Mode "dir" Response with file list**:

     ```json
     {
       "files": ["file1.txt", "file2.jpg", "file3.pdf"]
     }
     ```

   - **Mode "delete" Response**:

     ```json
     {
       "message": "File deleted successfully"
     }
     ```

   - **Mode "rename" Response**:

     ```json
     {
       "message": "File renamed successfully"
     }
     ```

### Connection Closure

When a client disconnects (`ws.on("close", ...)`), the server logs the disconnection.

## Error Handling

Any errors that occur during message processing, file operations, or other tasks are caught, and the Simple File Server sends a failure message back to the client.
