import argparse
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


def parse_arguments():
    parser = argparse.ArgumentParser(description="Lottery Ticket Server")
    parser.add_argument("port", type=int, help="TCP port where the server will listen")
    args = parser.parse_args()

    if not (1 <= args.port <= 65535):
        parser.error("Invalid port number")

    return args


def serve_forever(port):
    server = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind(("::", port))
    server.listen(5)

    print(f"Server is listening at port {port}...")

    while True:
        conn, addr = server.accept()
        print(f"New client connected: {addr}")
        ticket_type = conn.recv(1024).decode("utf-8")

        ticket = generate_ticket(ticket_type)

        conn.send(ticket.encode("utf-8"))

        conn.close()
        print(f"Client disconnected: {addr}")


def main():
    args = parse_arguments()
    serve_forever(args.port)


if __name__ == "__main__":
    main()
