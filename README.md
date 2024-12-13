import socket
import base64
import threading

def handle_client(client_socket):
    while True:
        message = client_socket.recv(1024).decode()
        if message.lower() == 'bye':
            print("Client has closed the connection.")
            break
        decoded_message = base64.b64decode(message).decode()
        print(f"Client: {decoded_message}")
        response = input("Server: ")
        encoded_response = base64.b64encode(response.encode())
        client_socket.send(encoded_response)

    client_socket.close()

def start_server():
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind(('localhost', 12345))
    server_socket.listen(1)
    print("Server is listening on port 12345...")
    
    client_socket, client_address = server_socket.accept()
    print(f"Connection from {client_address} has been established!")
    
    handle_client(client_socket)
    server_socket.close()

def start_client():
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client_socket.connect(('localhost', 12345))

    while True:
        message = input("Client: ")
        encoded_message = base64.b64encode(message.encode())
        client_socket.send(encoded_message)

        if message.lower() == 'bye':
            print("Connection closed.")
            break
        
        response = client_socket.recv(1024).decode()
        decoded_response = base64.b64decode(response).decode()
        print(f"Server: {decoded_response}")

    client_socket.close()

if __name__ == "__main__":
    choice = input("Start server or client? (s/c): ").lower()
    if choice == 's':
        start_server()
    elif choice == 'c':
        start_client()
    else:
        print("Invalid choice! Please enter 's' for server or 'c' for client.")
