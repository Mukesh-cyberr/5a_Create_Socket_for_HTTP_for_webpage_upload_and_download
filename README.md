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

HOST = "127.0.0.1"
PORT = 8000

def upload_file(filename):
    with open(filename, "r") as f:
        content = f.read()

    request = f"UPLOAD {filename}\n{content}"

    with socket.socket() as s:
        s.connect((HOST, PORT))
        s.sendall(request.encode())
        print("Server:", s.recv(4096).decode())


def download_file(filename):
    request = f"DOWNLOAD {filename}"

    with socket.socket() as s:
        s.connect((HOST, PORT))
        s.sendall(request.encode())

        data = s.recv(4096).decode()

        print("Downloaded Content:\n", data)

        with open("downloaded_" + filename, "w") as f:
            f.write(data)


if __name__ == "__main__":
    upload_file("example.html")
    download_file("example.html")

```
server
```
import socket
import threading

HOST = "127.0.0.1"
PORT = 8000

def handle_client(conn, addr):
    print("Connected:", addr)

    data = conn.recv(4096).decode()

    if data.startswith("UPLOAD"):
        filename, content = data.split("\n", 1)

        filename = filename.split(" ")[1]  # UPLOAD <filename>

        with open(filename, "w") as f:
            f.write(content)

        conn.sendall(b"UPLOAD OK")

    elif data.startswith("DOWNLOAD"):
        filename = data.split(" ")[1].strip()

        try:
            with open(filename, "r") as f:
                content = f.read()

            conn.sendall(content.encode())
        except:
            conn.sendall(b"FILE NOT FOUND")

    conn.close()


with socket.socket() as s:
    s.bind((HOST, PORT))
    s.listen()
    print("Server running on", HOST, PORT)

    while True:
        conn, addr = s.accept()
        threading.Thread(target=handle_client, args=(conn, addr)).start()

```
example.html
```
<?xml version="1.0" encoding="iso-8859-1"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
         "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
	<head>
		<title>404 - Not Found</title>
	</head>
	<body>
		<h1>404 - Not Found</h1>
	</body>
</html>
```
## OUTPUT
client

<img width="811" height="372" alt="image" src="https://github.com/user-attachments/assets/9ca2b482-398a-417d-8e2c-5c83c14e3795" />

server

<img width="778" height="190" alt="image" src="https://github.com/user-attachments/assets/4bb5cfa8-c015-453b-96a7-b54efbc1030a" />

## Result
Thus the socket for HTTP for web page upload and download created and Executed
