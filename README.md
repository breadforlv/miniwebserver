# miniwebserver
尝试实现一个迷你的web服务器，用来学习C++语法、多线程编程、网络编程、Linux环境下编程
学习路线图
1. 基础准备
在开始之前，确保你熟悉一些基础知识：

Linux 系统基本操作：如如何使用命令行、文件系统操作、安装软件等。
C++ 基础：包括类、指针、引用、STL 容器、智能指针等。
网络编程基础：了解 TCP/IP、Socket 编程、HTTP 协议等概念。
2. 环境搭建
首先，你需要在 Linux 系统中搭建编译和运行环境：
sudo apt update
sudo apt install build-essential cmake git
克隆 miniwebserver 项目并进入目录：
git clone https://github.com/xiagai/miniwebserver.git
cd miniwebserver
3. 编译与运行
项目通常是使用 cmake 构建的，可以按以下步骤进行编译：
mkdir build
cd build
cmake ..
make
编译完成后，运行生成的可执行文件，通常是 ./miniwebserver，然后可以在浏览器中访问 http://localhost:8080 来测试服务器的响应。
4. 理解代码结构
学习一个新项目时，首先需要从整体上理解其结构。你可以按如下顺序去阅读代码：

main.cpp：这是程序的入口，启动服务器。首先看服务器的启动流程，理解如何初始化服务器、启动监听和事件循环。
Server.cpp / Server.h：服务器核心逻辑，包括如何处理客户端连接和分发请求。
HttpRequest.h / HttpResponse.h：负责处理 HTTP 请求和响应的代码，理解如何解析 HTTP 请求报文，生成响应报文。
Epoll.h：学习如何使用 epoll 实现 I/O 复用，管理多个文件描述符的事件。
ThreadPool.h：线程池的实现逻辑，理解如何使用多线程提高服务器的并发处理能力。
5. 关键功能深入理解
1. Socket 编程
理解 socket()、bind()、listen() 和 accept() 等系统调用，它们是服务器与客户端建立连接的基础。可以从 Server.cpp 中看到这些系统调用的使用。

socket()：创建一个套接字。
bind()：将套接字与特定的IP地址和端口绑定。
listen()：开始监听来自客户端的连接。
accept()：接受客户端的连接并生成一个新的套接字。
2. I/O 复用 (epoll)
miniwebserver 使用 epoll 来高效处理大量客户端的请求。你需要理解以下内容：

epoll_create()：创建一个 epoll 实例。
epoll_ctl()：将文件描述符（如套接字）注册到 epoll 实例上。
epoll_wait()：等待文件描述符上的事件（如可读、可写）。
epoll 的好处在于它可以同时监听多个文件描述符，并在有事件发生时一次性处理多个事件，避免了传统的 select() 或 poll() 的效率瓶颈。

3. HTTP 请求与响应
学习如何解析 HTTP 请求和构建 HTTP 响应。通常，服务器接收到的 HTTP 请求包括请求行、请求头和请求体，响应则包括状态行、响应头和响应体。

HttpRequest 类负责从客户端读取请求，解析出 HTTP 方法（如 GET、POST）、路径、HTTP 版本、头部等信息。
HttpResponse 类负责构建 HTTP 响应，如设置响应状态码（200、404 等）、设置响应头、写入响应体等。
例如：
std::string response = "HTTP/1.1 200 OK\r\n";
response += "Content-Length: 13\r\n\r\n";
response += "Hello, World!";
write(client_fd, response.c_str(), response.size());
4. 线程池的使用
服务器采用了线程池来处理并发请求，避免主线程阻塞。你需要理解如何实现线程池以及如何向线程池中提交任务。

ThreadPool 类管理了多个工作线程，并通过任务队列将客户端请求分发给这些线程进行处理。
理解如何创建一个线程池，以及如何通过任务队列向线程池中提交任务。
5. 多线程同步
在多线程环境中，资源访问的同步至关重要。例如，线程池中的任务队列需要使用互斥锁（mutex）和条件变量（condition_variable）来保证多个线程同时访问时不会出现竞争条件。

6. 调试和改进
在理解代码的基础上，可以开始尝试自己修改和扩展项目的功能。例如：

添加更多的 HTTP 请求处理功能：比如支持 POST 方法或处理动态路由。
优化并发性能：调节线程池大小、epoll 的触发模式（ET 或 LT 模式）等。
添加定时器功能：管理长连接超时，定期清理无效连接。
安全性增强：添加防止恶意请求的安全措施，避免资源被耗尽。
7. 参考文档与资源
在学习过程中，可以参考一些网络编程和 HTTP 协议的文档和书籍：

《UNIX 网络编程》：讲解了如何在 UNIX/Linux 环境下进行网络编程。
《Linux 高性能服务器编程》：重点讲解了 epoll、多线程和高并发编程技巧。
官方 epoll 和 pthread 文档：详细了解系统调用和线程库的使用。
8. 后续深入学习
学习完 miniwebserver 后，可以继续深入学习更复杂的服务器设计，尝试学习：

高并发编程：深入理解 I/O 多路复用和异步 I/O（如 aio 和 io_uring）。
性能优化：如减少锁竞争、优化线程调度、减少上下文切换等。
分布式系统：可以进一步学习如何构建分布式 Web 服务器，或研究负载均衡、缓存等系统设计。
总结
通过 miniwebserver，你将能够系统地学习以下几个方面：

Linux 网络编程的基础知识（如 socket、bind、accept 等）。
如何使用 epoll 实现高效的 I/O 复用。
HTTP 协议的基本工作原理以及如何处理请求和生成响应。
多线程编程、线程池的使用及同步机制。
通过掌握这些知识，你不仅能搭建一个简单的 HTTP 服务器，还能为以后学习更复杂的 Web 服务器打下坚实的基础。
