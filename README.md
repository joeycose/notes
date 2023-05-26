import random
import socket

class LotteryTicketGenerator:
    # ... (Same as the previous prototypes) ...

def generate_ticket(ticket_type):
    # ... (Same as the previous prototypes) ...

def serve_forever(port):
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind(("0.0.0.0", port))
    server.listen(5)

    print(f"Server is listening at port {port}...")

    while True:
        conn, addr = server.accept()
        print(f"New client connected: {addr}")

        ticket_type = conn.recv(1024).decode("utf-8").strip()
        ticket = generate_ticket(ticket_type)

        conn.send(ticket.encode("utf-8"))

        conn.close()
        print(f"Client disconnected: {addr}")

def main():
    fixed_port = 9999  # Fixed port number where the server will listen
    serve_forever(fixed_port)

if __name__ == "__main__":
    main()
