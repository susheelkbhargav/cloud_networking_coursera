About mulating a datacenter using a toy topology in a network emulator called Mininet. 
So our datacenter topology will consist of two levels of switches. The top level are called core switches and the bottom are called edge switches.  Each core switch is connected to every edge switch and for the first three assignments, our topology will have two core switches and three edge switches organized just like you see here. Connected to each edge switch are hosts.  We'll also have two hosts attached to each edge switch. In our data center, we'll be emulating a software-defined network, which has a controller which connects to all switches. And on this controller, we'll be running an open source software controller called Ryu and we'll be extending a program that can control the network in a centralized manner. Switches and hosts are connected by links and on each switch is a port for each link. 

And these ports are important in a SDN, since it's one way we can specify forwarding behavior.  Our host, h3 has IP address 10.0.0.3 and our controller program installs a rule at switch s101 that matches on h3's IP address. The first part is a match and the second part is the action. So if a packet arrives at switch s101 from host h3, we'll look for a matching rule in that switch. So the switch would find its matching rule and output it or forward output one. 

Our data center will have two tenants, four hosts running iperf and the remaining running a video streaming server and client. Our edge switches will initially send all their traffic through switch s104. And this naive policy is obviously not optimal since all traffic on the network must be sent through s104, while s105 goes completely unused. This also means that traffic from one tenant will have an observable impact on another tenant. And we'll see this on our video client as it experiences poor video quality. 



We can see bandwidth monitor by opening a new Chrome instance from the desktop. Not from the one playing the video stream since this is in a sandboxed environment in our emulator. And bring up our cloud dashboard by going to localhost or 127.0.0.1. 


Few functions to keep in mind :

status reply handler:

This is a function that is called each time the controller receives the statistics reply from a switch. 

The first thing to note is the variable name, which takes the responding switch's ID. Called the data path ID, or dpid, and converts it to the representation we saw on our topology diagram. So the variable name will be something like s101 or s102. 


The next section is extracting the number of dropped packets from the statsReply. So the reply consists of a list of statistics grouped by port, as I described earlier. So this block of code enumerates each item in body with the current item name stat. 


This then sums all the dropped packets from each port and then passes the result to our bandwidth statistics module. 

We can determine what host is associated with the port for that statistic as this is a two-level dictionary that takes the responding switch's name and a port number and returns. Either a host name, such as H1 or H2, or a switch name, such as S101 or S102, depending on which node is associated with that port. 