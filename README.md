

**prototype_c_server.py:**

```python
import random
import socket

class LotteryTicketGenerator:
    def __init__(self, ticket_type):
        self.ticket_type = ticket_type
        self.number_pool = self._initialize_number_pool()

    def _initialize_number_pool(self):
        if self.ticket_type == "type1":
            return list(range(1, 50))
        elif self.ticket_type == "type2":
            return list(range(1, 41))
        elif self.ticket_type == "type3":
            return list(range(1, 60))

    def generate_ticket(self):
        ticket = []
        pool = self.number_pool.copy()
        for _ in range(6):
            index = random.randint(0, len(pool) - 1)
            number = pool.pop(index)
            ticket.append(number)
        return sorted(ticket)

def generate_ticket(ticket_type):
    generator = LotteryTicketGenerator(ticket_type)
    ticket = generator.generate_ticket()
    return ",".join(str(num) for num in ticket) + "\n"

def serve_forever(port):
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind(("0.0.0.0", port))
    server.listen(5)

    print(f"Server is listening at port {port}...")

    while True:
        conn, addr = server.accept()
        print(f"New client connected: {addr}")
        
        data = conn.recv(1024).decode("utf-8")
        ticket_type, num_tickets = data.split()
        num_tickets = int(num_tickets)

        response = ""
        for _ in range(num_tickets):
            response += generate_ticket(ticket_type)

        conn.send(response.encode("utf-8"))

        conn.close()
        print(f"Client disconnected: {addr}")

def main():
    fixed_port = 9999  # The fixed port number where the server will listen
    serve_forever(fixed_port)

if __name__ == "__main__":
    main()
```

**prototype_c_client.py:**

```python
import socket

def request_ticket(ipv4_address, port, ticket_type, num_tickets):
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client.connect((ipv4_address, port))

    client.send(f"{ticket_type} {num_tickets}".encode("utf-8"))

    tickets = []
    for _ in range(num_tickets):
        ticket = client.recv(1024).decode("utf-8")
        tickets.append(ticket)

    client.close()

    return tickets

def main():
    # Hard-coded arguments
    ipv4_address = "127.0.0.1"
    port = 9999
    ticket_type = "type1"
    num_tickets = 5

    tickets = request_ticket(ipv4_address, port, ticket_type, num_tickets)

    for ticket in tickets:
        print(ticket)

if __name__ == "__main__":
    main()
```

Now, you can test Prototype C by running the `prototype_c_server.py` and `prototype_c_client.py` files using your preferred method. Please let me know if everything works as expected, and we can proceed with developing Prototype D.
