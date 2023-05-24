import socket
from lottery_ticket_generator import LotteryTicketGenerator


def process_client_request(data):
    ticket_type, number_of_tickets = data.split()
    generator = LotteryTicketGenerator(ticket_type)
    tickets = [generator.generate_ticket() for _ in range(int(number_of_tickets))]
    return tickets


def main():
    HOST = '127.0.0.1'
    PORT = 5000

    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind((HOST, PORT))
    server_socket.listen(1)

    print(f"Listening on {HOST}:{PORT}...")

    while True:
        client_socket, client_address = server_socket.accept()
        print(f"Accepted connection from {client_address}")

        data = client_socket.recv(1024).decode()
        tickets = process_client_request(data)

        for ticket in tickets:
            response = ','.join(str(num) for num in ticket) + '\n'
            client_socket.sendall(response.encode())

        client_socket.close()
        print(f"Closed connection from {client_address}")


if __name__ == "__main__":
    main()
    
    
    
    
   
 //`/   /// /   //  //  //  //  //  /// //  //  //  //  //  //
 
 import argparse
import socket


def parse_arguments():
    parser = argparse.ArgumentParser(description="Lottery Ticket Client")
    parser.add_argument("ticket_type", choices=[
                        "type1", "type2", "type3"], help="Type of lottery ticket to request")
    parser.add_argument("-n", "--number_of_tickets", type=int,
                        default=1, help="Number of tickets to request")
    parser.add_argument("--host", default="127.0.0.1",
                        help="Host address of the server")
    parser.add_argument("--port", type=int, default=5000,
                        help="Port number to use for connection")
    args = parser.parse_args()
    
    if args.number_of_tickets < 1:
        print("Error: The number of tickets must be at least 1.")
        sys.exit(1)
    
    return args


def main():
    args = parse_arguments()
    host = args.host
    port = args.port
    ticket_type = args.ticket_type
    number_of_tickets = args.number_of_tickets

    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client_socket.connect((host, port))

    request = f"{ticket_type} {number_of_tickets}"
    client_socket.sendall(request.encode())

    received_data = []
    while True:
        data = client_socket.recv(1024)
        if not data:
            break
        received_data.append(data.decode())

    client_socket.close()

    print("Received tickets:")
    for ticket_data in ''.join(received_data).split('\n')[:-1]:
        print(ticket_data)

if __name__ == "__main__":
    main()
```

To test the client and server scripts, open two terminal windows.

In the first window, run the server script:

```
python3 server.py
```

In the second window, run the client script:

```
python3 client.py type1 -n 5
