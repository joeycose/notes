import socket
import sys
from lottery_ticket_generator import LotteryTicketGenerator

def generate_tickets(ticket_type, num_tickets):
    generator = LotteryTicketGenerator(ticket_type)
    tickets = []

    for _ in range(num_tickets):
        ticket = generator.generate_ticket()
        tickets.append(ticket)

    return tickets

def main():
    host = "::1"
    port = int(input("Enter the socket number to start the server: "))
    
    sock = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
    server_address = (host, port)

    sock.bind(server_address)
    sock.listen(1)

    print(f'Server is running on {host}, port {port}')

    try:
        while True:
            print('Waiting for an incoming connection...')
            connection, client_address = sock.accept()
            print(f'Connection from {client_address}')
            data = connection.recv(1024).decode().strip().split(',')
            ticket_type, num_tickets, unique_id = data

            tickets = generate_tickets(ticket_type, int(num_tickets))
            response = f"{unique_id}-{tickets}"
            connection.sendall(response.encode())

            connection.close()
            print('Connection closed.')

    except KeyboardInterrupt:
        print("Shutting down the server.")
        sock.close()
        sys.exit()

if __name__ == "__main__":
    main()
    
    
    
    
    
   #
   
   import socket
import sys

def main():
    host = "::1"
    port = int(input("Enter the server's socket number: "))
    ticket_type = input("Enter the desired ticket type (type1, type2, or type3): ")
    num_tickets = int(input("Enter the number of tickets to generate: "))
    unique_id = input("Enter a unique identifier or leave it empty: ")
    if not unique_id:
        unique_id = "default"

    sock = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)

    server_address = (host, port)
    sock.connect(server_address)

    request = f"{ticket_type},{num_tickets},{unique_id}"
    sock.sendall(request.encode())

    received_data = sock.recv(1024).decode()
    unique_id, tickets = received_data.split("-")

    print(f"Tickets received for unique_id '{unique_id}':")
    print(tickets)

    with open("tickets.txt", "a") as f:
        f.write(tickets)
        f.write("\n")

    sock.close()

if __name__ == "__main__":
    main()
