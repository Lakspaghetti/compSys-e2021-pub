# Exercises for Monday Nov 01, 2021

## Socket Programming Preparatory Session

Today you need to read and understand the code snippets for network programming in BOH. Some of the code snippets have been covered previously (rio functions) and some (socket interface) will be covered in the lecture today.

* Do-it-yourself Recap: System-level I/ O
  * What is the difference between the Unix I/O and the book’s robust I/O APIs?
    P. 947 picture
    
    Use the standard I/O functions whenever possible. The standard I/O func-tions are the method of choice for I/O on disk and terminal devices. Most C programmers use standard I/O exclusively throughout their careers, never bothering with the lower-level Unix I/O functions (except possibly stat, which has no counterpart in the standard I/O library). Whenever possible, we recommend that you do likewise.

    G2: Don’t use scanf orrio_readlineb to read binary files.Functions like scanf and rio_readlineb are designed specifically for reading text files. A common error that students make is to use these functions to read binary data, causing their programs to fail in strange and unpredictable ways. For example, binary files might be littered with many 0xa bytes that have nothing to do with terminating text lines.

    G3: Use the Rio functions for I/O on network sockets. Unfortunately, standard I/O poses some nasty problems when we attempt to use it for input and output on networks. As we will see in Section 11.4, the Linux abstrac-tion for a network is a type of file called a socket. Like any Linux file, sockets are referenced by file descriptors, known in this case as socket de-scriptors. Application processes communicate with processes running on other computers by reading and writing socket descriptors.

    
  * When can short counts be returned by I/O functions? Why?
  
    In some situations, read and write transfer fewer bytes than the application requests. Such short counts do not indicate an error. They occur for a number of reasons:
    
    Encountering EOFon reads. Suppose that we are ready to read from a file that contains only 20 more bytes from the current file position and that we are reading the file in 50-byte chunks. Then the next read will return a short count of 20, and the read after that will signal EOF by returning a short count of 0.
    
    Reading text lines from a terminal. If the open file is associated with a terminal (i.e., a keyboard and display), then each read function will transfer one text line at a time, returning a short count equal to the size of the text line.
    
    Reading and writing network sockets. If the open file corresponds to a network socket (Section 11.4), then internal buffering constraints and long net-work delays can cause read and write to return short counts. Short counts can also occur when you call read and write on a Linux pipe, an inter-process communication mechanism that is beyond our scope.
In 

* Refresh your understanding of rio functions which were covered earlier in the course
  * rio_readn, rio_writen (Fig 10.4 BOH page 935)
  * rio_read (Fig 10.7 BOH page 937)
  * rio_readlineb, rio_readnb (Fig 10.8 BOH page 938)
  * Read and understand Section 10.11 (especially directives G1,G2,G3) in BOH page 947-948
  * Using rio functions are not strictly necessary you can use UNIX I/O functions but they simplify programming a lot

* Understand socket interface
  * Read man pages of socket, bind, connect, listen, accept, read and write.
  * hostinfo example code (Fig 11.17 BOH page 977)
  * open_clientfd code (Fig 11.18 BOH page 979)
  * open_listenfd code (Fig 11.19 BOH page 981)
  * Echo client/iterative server example code (Fig 11.20 - 11.22 BOH page 982-983)
  * Using the wrapper functions is optional but they simplify programming a lot

* You can find the source refs for the BOH book [here](http://csapp.cs.cmu.edu/3e/code.html)

## Extend the echo server example
You can do this with any functionality, but we have two suggestions below. You can use any of the server types: iterative, process and threaded. But consider your choice and argue to yourself (and your study group) why you make the choice. What are the design criteria for the application and why is the chosen server type the best for solving this?

### Ping/pong
When the server receives a `ping` message, it replies with a `pong`. In all other cases the server returns `BAD REQUEST`.

### Distributed variable
The server accepts two different kinds of messages.

* `PUT [SOME STRING]` that stores a string in the internal storage of the server. Note that the first space is a separator and thus part of the protocol syntax.
* `GET` that returns the string that is stored.

When the server receives a `PUT` it saves the string to an internal variable and returns `OK`. The following `PUT` messages will not update the variable, but return `VARIABLE FILLED`.

When the server receives a `GET` it returns the stores variable. All `GET`s should be blocking, until the server has received the first `PUT`. Thus, clients sending a `GET` before another client send a `PUT` should block and wait until the server receives the first `PUT` and responds to all waiting clients.
