import argparse
import socket


def parse_arguments():
    parser = argparse.ArgumentParser(description="Lottery Ticket Client")
    parser.add_argument("ipv6_address", help="IPv6 address of the server")
    parser.add_argument("port", type=int, help="TCP port where the server is listening")
    parser.add_argument("ticket_type", choices=["type1", "type2", "type3"], help="Type of lottery ticket to request")
    args = parser.parse_args()
    if not (1 <= args.port <= 65535):
        parser.error("Invalid port number")

    return args


def request_ticket(ipv6_address, port, ticket_type):
    client = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
    client.connect((ipv6_address, port))

    client.send(ticket_type.encode("utf-8"))

    ticket = client.recv(1024).decode("utf-8")

    client.close()

    return ticket


def main():
    args = parse_arguments()
    ipv6_address = args.ipv6_address
    port = args.port
    ticket_type = args.ticket_type

    ticket = request_ticket(ipv6_address, port, ticket_type)

    print(ticket)


if __name__ == "__main__":
    main()
