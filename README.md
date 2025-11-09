# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program 

client
```


import socket
import time

HOST = '127.0.0.1'
PORT = 8081

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect((HOST, PORT))
print("Connected to the server.\n")

filename = input("Enter the HTML file name to upload (e.g., index.html): ")
frame_size = int(input("Enter frame size (in bytes): "))


try:
    with open(filename, "r") as f:
        content = f.read()
except FileNotFoundError:
    print("File not found. Make sure the HTML file exists in the same folder.")
    client_socket.close()
    exit()


frames = [content[i:i + frame_size] for i in range(0, len(content), frame_size)]


for i, frame in enumerate(frames):
    print(f"\nSending Frame {i+1}:")
    client_socket.send(frame.encode())
    ack = client_socket.recv(1024).decode()
    print(f"Server Response: {ack}")
    time.sleep(0.5)

# Send end signal
client_socket.send("END".encode())
print("\nAll frames sent successfully!")

client_socket.close()

```

server
```


import socket

HOST = '127.0.0.1'   
PORT = 8081          


server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind((HOST, PORT))
server_socket.listen(1)

print(f"Server started and listening on {HOST}:{PORT}")

conn, addr = server_socket.accept()
print(f"Connected by {addr}")

try:
    file_data = ""
    while True:
        frame = conn.recv(1024).decode()
        if not frame or frame == "END":
            break

        print(f"Received Frame: {frame}")
        
        if frame:
            conn.send("ACK".encode())
            file_data += frame
        else:
            conn.send("NACK".encode())

   
    with open("received_page.html", "w") as f:
        f.write(file_data)

    print("\nWebpage received and saved as 'received_page.html'")
    conn.close()
except Exception as e:
    print(f"Error: {e}")

server_socket.close()

```
## OUTPUT

client

<img width="687" height="350" alt="Screenshot 2025-11-09 203945" src="https://github.com/user-attachments/assets/ed236b62-8ebe-4bd6-b01e-b6734e67063e" />

server

<img width="701" height="290" alt="Screenshot 2025-11-09 203959" src="https://github.com/user-attachments/assets/c0d02e0e-25be-4959-8f56-2da30a885f38" />


## Result
Thus the socket for HTTP for web page upload and download created and Executed
