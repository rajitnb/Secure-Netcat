Netcat (the Unix nc command) is a powerful command line utility that is commonly used by systems administrators and adversaries alike.

While netcat has many command line options and lots of interesting functionality, this program focuses on the functionality described by its name: “net” and “cat.” The Unix cat command takes a ﬁle and prints it to STDOUT (or wherever STDOUT is redirected). For our purposes, think of netcat performing cat across a network. A client instance will receive input from STDIN and send it to a server instance. The server instance will receive network bytes and send it to STDOUT. As you might imagine, netcat provides no protection for the information as it is transmitted across the network.


The goal of this program is to provide conﬁdentiality and integrity to this simple version of netcat i.e. a secure version of 'nc' and hence the name 'snc'.

The standard way to add conﬁdentiality and integrity to messages is through the use of cryptography. To fulfil this, we will be using AES (Advanced Encryption Standard) encryption function which will provide confidentiality and MAC function which provides integrity. So to provide both, we will be using the GCM (Galios Counter Mode), which is a cipher mode that combines encryption and MAC.

The instance running on the client will read from STDIN and send AES-GCM protected (using PyCryptodome) data to the instance running on the server. The instance running on the server will read from the network, decrypt and authenticate the data, and write it to STDOUT. Both instances will terminate  if an EOF character is encountered, or if a keyboard interrupt (i.e., control-c) occurs. If the data fails to authenticate (e.g., the data was manipulated, or the keys were different), the server will terminate, reporting an error to STDERR.

To be equivalent to the nc command, information entered in STDIN on the server should make its way to STDOUT on the client. To do this, I have used the select call to block and wait for input either on standard input or the network socket. As an alternative to using select, you may use multiple threads, although this is far less efficient.

</br><br>
A sample execution with bi-directional ﬂow is as follows:

On the SERVER side:

	$./snc --key IAMAWESOME -l 9999 < ssend.txt > sout.txt

On the CLIENT side:

	$./snc --key IAMAWESOME 127.0.0.1 9999 < csend.txt > cout.txt

</br><br>
The usage is as follows:

usage: snc.py [-h] [--key KEY] [-l] dest_port [dest_port ...]

positional arguments:
  dest_port     Destination IP address and the port number

optional arguments:
  -h, --help    show this help message and exit
  --key KEY     The key used for encryption/decryption
  -l, --listen  Listen
</br><br>

Modules used : argparser, socket, sys, queue, select, pycryptodome
</br><br>

The program was tested on the following environment:(NCSU's VCL)

             OS : Ubuntu 16.04 LTS Base
Python3 version : 3.5.2
