## localhost vs 127.0.0.1

### The difference between localhost and 127.0.0.1 

- definition
  - "localhost" and "127.0.0.1" are two different ways to refer to the same thing: the local loopback network interface on your computer.

- localhost: hostname that is used to refer to the loopback interface
  - When you type "localhost" in a web browser or another application that needs to connect to a server running on your own machine, it resolves to the IP address 127.0.0.1.

- 127.0.0.1: IP address of the loopback interface
  - It is a special IP address that is reserved for the loopback interface, which allows a computer to communicate with itself.

- summary
  - "localhost" is just a hostname that resolves to 127.0.0.1, which is the actual IP address of the loopback interface. 
  - Both "localhost" and "127.0.0.1" are commonly used interchangeably to refer to the loopback interface.

### local loopback network interface on your computer

- The local loopback network interface on your computer is a virtual network interface that allows a computer to communicate with itself. It is commonly referred to as the loopback interface or loopback address.

- The loopback interface is assigned the IP address 127.0.0.1 and is used by many applications that need to communicate with a server running on the same machine. 
  - example: a web browser might use the loopback interface to connect to a web server running on the same machine, or a database server might use the loopback interface to connect to a database running on the same machine.

- When a computer sends a packet to the loopback address (127.0.0.1), the packet is routed back to the same computer without ever leaving the network interface. This allows applications to communicate with each other on the same machine without having to go through the network stack, which can improve performance and security.