# CN

Server Program (MyServer.java)
import java.io.*;
import java.net.*;
public class MyServer {
public static void main(String[] args) {
try {
ServerSocket ss = new ServerSocket(6666);
System.out.println("Server is on..... Listening...... ");
Socket s = ss.accept();// establishes connection
DataInputStream dis = new DataInputStream(s.getInputStream());
String str = (String) dis.readUTF();
System.out.println("message= " + str);
ss.close();
} catch (Exception e) {
System.out.println(e);
}
}
}
Client Program (MyClient.java)
import java.io.*;
import java.net.*;
public class MyClient {
public static void main(String[] args) {
try {
Socket s = new Socket("localhost", 6666);
DataOutputStream dout = new DataOutputStream(s.getOutputStream());
dout.writeUTF("Hello Server");
dout.flush();
dout.close();
s.close();
} catch (Exception e) {
System.out.println(e);
}
}
}


Echo SErver

Server Program(ECHO):
import java.net.*;
import java.io.*;
public class EServer {
public static void main(String args[]) {
try {
ServerSocket s = new ServerSocket(9000);
String line;
Socket c = s.accept();
DataInputStream is = new DataInputStream(c.getInputStream());
PrintStream ps = new PrintStream(c.getOutputStream());
while (true) {
line = is.readLine();
ps.println(line);
}
} catch (IOException e) {
System.out.println(e);
}
}
}
Client Program (ECHO):
import java.net.*;
import java.io.*;
public class EClient {
public static void main(String arg[]) {
try {
InetAddress ia = InetAddress.getLocalHost();
Socket c = new Socket(ia, 9000);
String line;
PrintStream os = new PrintStream(c.getOutputStream());
DataInputStream is = new DataInputStream(System.in);
DataInputStream is1 = new DataInputStream(c.getInputStream());
while (true) {
System.out.println("Client:");
line = is.readLine();
os.println(line);
System.out.println("Server:" + is1.readLine());
}
} catch (IOException e) {
System.out.println("Socket Closed!");
}
}
}


tcp/ip

Server_DT:
import java.net.*;
import java.io.*;
import java.util.Date;
public class Server_DT {
public static void main(String[] args) throws IOException {
// TODO code application logic here
// Step 1. Reserve a port number on the Server to offer this service
ServerSocket ss = new ServerSocket(5000);
// (Optional)To confirm Server Reserved specified port or not
System.out.println("The Server has reserved port No.: " + ss.getLocalPort() + " for this
Service");
// Step 2. Now create a Client Socket on Server for Bidirectonal Communication.
// Socket is created only when client communicates with the server
Socket cs = ss.accept();
// To confirm Server communicated through the socket or not
System.out.println("Client with IP Address " + cs.getInetAddress() + " has
communicated via port No.: " + cs.getPort());
Date d = new Date();
String s = "Current Date & Time on Server is:" + d;
// Send String s to client via client socket
PrintWriter toclient = new PrintWriter(cs.getOutputStream(), true);
toclient.print(s);
toclient.close();
cs.close();
ss.close();
}
}Client_DT
import java.net.*;
import java.io.*;
public class Client_DT {
public static void main(String[] args) throws UnknownHostException, IOException {
// TODO code application logic here
// Step 1. Create a client socket to connect to Server
Socket cs = new Socket("LocalHost", 5000);
// To confirm Client is communicating through the port
System.out.println("Client " + cs.getInetAddress() + " is communicating from port No.:"
+ cs.getPort());
// Receive Date Sent by Server
BufferedReader fromserver = new BufferedReader(new
InputStreamReader(cs.getInputStream()));
System.out.println(fromserver.readLine());
fromserver.close();
cs.close();
}
}

Half duplex

Server
import java.io.*;
import java.net.*;
class Server_HalfDup {public static void main(String args[]) throws Exception {
// Create server Socket
ServerSocket ss = new ServerSocket(888);
// connect it to client socket
Socket s = ss.accept();
System.out.println("Connection established");
// to send data to the client
PrintStream ps = new PrintStream(s.getOutputStream());
// to read data coming from the client
BufferedReader br = new BufferedReader(new
InputStreamReader(s.getInputStream()));
// to read data from the keyboard
BufferedReader kb = new BufferedReader(new InputStreamReader(System.in));
// server executes continuously
while (true) {
String str, str1;
// repeat as long as the client
// does not send a null string
// read from client
while ((str = br.readLine()) != null) {
System.out.println("From Client:" + str);
str1 = kb.readLine();
// send to client
ps.println(str1);
}
// close connection
ps.close();
br.close();
kb.close();
ss.close();
s.close();
// terminate application
System.exit(0);
} // end of while
}
}Client
import java.io.*;
import java.net.*;
class Client_HalfDup {
public static void main(String args[])throws Exception {
// Create client socket
Socket s = new Socket("localhost", 888);
// to send data to the server
DataOutputStream dos = new DataOutputStream(s.getOutputStream());
// to read data coming from the server
BufferedReader br = new BufferedReader(new
InputStreamReader(s.getInputStream()));
// to read data from the keyboard
BufferedReader kb = new BufferedReader(new InputStreamReader(System.in));
String str, str1;
// repeat as long as exit
// is not typed at client
while (!(str = kb.readLine()).equals("exit")) {
// send to the server
dos.writeBytes(str + "\n");
// receive from the server
str1 = br.readLine();
System.out.println("From Server: " + str1);
}
// close connection.
dos.close();
br.close();
kb.close();
s.close();
}
}


Full duplex

Server
import java.io.IOException;
import java.io.*;
import java.net.*;
import java.util.Scanner;
public class Server {
static ServerSocket serverSocket;
public static void main(String[] args) {
try {
serverSocket = new ServerSocket(1515);while (true) {
Socket accept_client = serverSocket.accept();
new Thread(new ServerIn(accept_client)).start();
new Thread(new ServerOut(accept_client)).start();
}
} catch (IOException e) {
e.printStackTrace();
try {
serverSocket.close();
} catch (IOException e1) {
// TODO Auto-generated catch block
e1.printStackTrace();
}
}
}
}
// Accepted threads
class ServerIn implements Runnable {
Socket socket;
ServerIn(Socket socket) {
this.socket = socket;
}
@Override
public void run() {
try {
InputStream in = socket.getInputStream();
while (true) {
byte infile[] = new byte[1024];
int size = in.read(infile);
String string = new String(infile, 0, size);
if (!string.equals("") && !string.equals("\n"))
System.out.println("message from client: " + string);
}
} catch (IOException e) {
e.printStackTrace();
try {
socket.close();
} catch (IOException e1) {
e1.printStackTrace();
}
}
}
}
// Thread to send
class ServerOut implements Runnable {Socket socket;
Scanner reader = new Scanner(System.in);
ServerOut(Socket socket) {
this.socket = socket;
}
public void run() {
try {
OutputStreamWriter out = new OutputStreamWriter(socket.getOutputStream());
while (true) {
String string = reader.nextLine();
out.write(string);
out.flush();
}
} catch (IOException e) {
e.printStackTrace();
try {
socket.close();
reader.close();
} catch (IOException e1) {
e1.printStackTrace();
}
}
}
}
Client
import java.io.*;
import java.util.*;
import java.net.*;
public class Client {
public static void main(String[] args) {
try {
Socket client = new Socket("127.0.0.1", 1515);
new Thread(new ClientIn(client)).start();
new Thread(new ClientOut(client)).start();
} catch (IOException e) {
e.printStackTrace();
}
}
}
// Accepted threads
class ClientIn implements Runnable {Socket socket;
ClientIn(Socket socket) {
this.socket = socket;
}
@Override
public void run() {
try {
InputStream in = socket.getInputStream();
while (true) {
byte infile[] = new byte[1024];
int size = in.read(infile);
String string = new String(infile, 0, size);
if (!string.equals("") && !string.equals("\n"))
System.out.println("message from server: " + string);
}
} catch (IOException e) {
e.printStackTrace();
try {
socket.close();
} catch (IOException e1) {
e1.printStackTrace();
}
}
}
}
// Thread to send
class ClientOut implements Runnable {
Socket socket;
Scanner reader = new Scanner(System.in);
ClientOut(Socket socket) {
this.socket = socket;
}
public void run() {
try {
OutputStreamWriter out = new OutputStreamWriter(socket.getOutputStream());
while (true) {
String string = reader.nextLine();
out.write(string);
out.flush();}
} catch (IOException e) {
e.printStackTrace();
try {
socket.close();
reader.close();
} catch (IOException e1) {
e1.printStackTrace();
}
}
}
}


Ftp

Server:-
import java.io.*;
import java.net.*;
import java.util.*;
class Serverfile {
public static void main(String args[]) {
try {
ServerSocket obj = new ServerSocket(139);
while (true) {
Socket obj1 = obj.accept();
DataInputStream din = new DataInputStream(obj1.getInputStream());
DataOutputStream dout = new DataOutputStream(obj1.getOutputStream());
String str = din.readLine();
FileReader f = new FileReader(str);
BufferedReader b = new BufferedReader(f);
String s;
while ((s = b.readLine()) != null) {
System.out.println(s);
dout.writeBytes(s + '\n');
}
f.close();
dout.writeBytes("-1\n");
}
} catch (Exception e) {
System.out.println(e);
}
}
}Client:-
import java.io.*;
import java.net.*;
import java.util.*;
class Clientfile {
public static void main(String args[]) {
try {
BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
Socket clsct = new Socket("127.0.0.1", 139);
DataInputStream din = new DataInputStream(clsct.getInputStream());
DataOutputStream dout = new DataOutputStream(clsct.getOutputStream());
System.out.println("Enter the file name:");
String str = in.readLine();
dout.writeBytes(str + '\n');
System.out.println("Enter the new file name:");
String str2 = in.readLine();
String str1, ss;
FileWriter f = new FileWriter(str2);
char buffer[];
while (true) {
str1 = din.readLine();
if (str1.equals("-1"))
break;
System.out.println(str1);
buffer = new char[str1.length()];
str1.getChars(0, str1.length(), buffer, 0);
f.write(buffer);
}
f.close();
clsct.close();
} catch (Exception e) {
System.out.println(e);
}
}
}


Remote code
Client
import java.io.*;
import java.net.*;
class RemoteClient {
public static void main(String args[]) {
try {
int Port;
BufferedReader Buf = new BufferedReader(new InputStreamReader(System.in));
System.out.print(" Enter the Port Address : ");
Port = Integer.parseInt(Buf.readLine());
Socket s = new Socket("localhost", Port);if (s.isConnected() == true)
System.out.println(" Server Socket is Connected Successfully. ");
InputStream in = s.getInputStream();
OutputStream ou = s.getOutputStream();
BufferedReader buf = new BufferedReader(new InputStreamReader(System.in));
BufferedReader buf1 = new BufferedReader(new InputStreamReader(in));
PrintWriter pr = new PrintWriter(ou);
System.out.print(" Enter the Command to be Executed : ");
pr.println(buf.readLine());
pr.flush();
String str = buf1.readLine();
System.out.println(" " + str + " Opened Successfully. ");
System.out.println(" The " + str + " Command is Executed Successfully. ");
pr.close();
ou.close();
in.close();
} catch (Exception e) {
System.out.println(" Error : " + e.getMessage());
}
}
}
Server
import java.io.*;
import java.net.*;
class RemoteServer {
public static void main(String args[]) {
try {
int Port;
BufferedReader Buf = new BufferedReader(new InputStreamReader(System.in));System.out.print(" Enter the Port Address : ");
Port = Integer.parseInt(Buf.readLine());
ServerSocket ss = new ServerSocket(Port);
System.out.println(" Server is Ready To Receive a Command. ");
System.out.println(" Waiting ..... ");
Socket s = ss.accept();
if (s.isConnected() == true)
System.out.println(" Client Socket is Connected Successfully. ");
InputStream in = s.getInputStream();
OutputStream ou = s.getOutputStream();
BufferedReader buf = new BufferedReader(new InputStreamReader(in));
String command = buf.readLine();
PrintWriter pr = new PrintWriter(ou);
pr.println(command);
Runtime H = Runtime.getRuntime();
Process P = H.exec(command);
System.out.println(" The " + command + " Command is Executed Successfully. ");
pr.flush();
pr.close();
ou.close();
in.close();
} catch (Exception e) {
System.out.println(" Error : " + e.getMessage());
}
}
}

Arp using udp


Server Program
import java.io.*;
import java.net.*;
import java.util.*;
class Serverarp12 {
public static void main(String args[]) {
try {
DatagramSocket server = new DatagramSocket(1309);
while (true) {byte[] sendbyte = new byte[1024];
byte[] receivebyte = new byte[1024];
DatagramPacket receiver = new DatagramPacket(receivebyte, receivebyte.length);
server.receive(receiver);
String str = new String(receiver.getData());
String s = str.trim();
// System.out.println(s);
InetAddress addr = receiver.getAddress();
int port = receiver.getPort();
String ip[] = { "165.165.80.80", "165.165.79.1" };
String mac[] = { "6A:08:AA:C2", "8A:BC:E3:FA" };
for (int i = 0; i < ip.length; i++) {
if (s.equals(ip[i])) {
sendbyte = mac[i].getBytes();
DatagramPacket sender = new DatagramPacket(sendbyte, sendbyte.length,
addr, port);
server.send(sender);
break;
}
}
break;
}
} catch (Exception e) {
System.out.println(e);
}
}
}Client Program
import java.io.*;
import java.net.*;
import java.util.*;
class Clientarp12 {
public static void main(String args[]) {
try {
DatagramSocket client = new DatagramSocket();
InetAddress addr = InetAddress.getByName("127.0.0.1");
byte[] sendbyte = new byte[1024];
byte[] receivebyte = new byte[1024];
BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
System.out.println("Enter the logical address (IP):");
String str = in.readLine();
sendbyte = str.getBytes();
DatagramPacket sender = new DatagramPacket(sendbyte, sendbyte.length, addr,
1309);
client.send(sender);
DatagramPacket receiver = new DatagramPacket(receivebyte, receivebyte.length);
client.receive(receiver);
String s = new String(receiver.getData());
System.out.println("The Physical Address is: " + s.trim());
client.close();
} catch (Exception e) {
System.out.println(e);
}
}
}
