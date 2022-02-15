# Intro to Node.js: Assignment

## Brief

In your group, research and give a detailed response to this question: `Why is Node.js not suitable for CPU-intensive work?`

Discuss your answer with classmates in the last 45 minutes of the assignment session. Your instructor may challenge your answers, so be sure to give a strong defence on your point.

```


Why is Node.js not suitable for CPU-intensive work?


Liew: Node.js does not utilise multiple cores and threads by default and only I/O operations can benefit from the asynchronous feature.


Charles:  Node is single threaded and makes node a bad choice for CPU intensive programmes. 
However, Node introduced a workaround for this in version 10.5. Even so, while the application involves heavy computing and number crunching,  it blocks the single thread running in the application and halts the progress of the event loop until the computation is finished. It also delays serving the requests still in queue. 

Using microservices architecture and separating heavy computational tasks from node  implementation can serve to be a solution. 



Leslie: 

Most programs are either CPU-bound (require heavy computations or a large number of operations, such as image processing, compression algorithms, matrix multiplication … ) or I/O-bound (reading a file from the disk, making a network request, or querying the database …), which means their execution speed is limited by either the CPU or the I/O subsystems.
The reasons are rooted in the way Node.js is designed in its architecture and execution model: Node.js is single-threaded in nature, based on an event-driven architecture and provides an API to access asynchronous non-blocking I/O. 

Node.js uses the event loop and non-blocking I/O for concurrency. In other words, whereas web servers of other languages might allocate a separate thread for each incoming request (or each client), 


Node.js will handle all of the incoming requests in a single thread and use the event loop to coordinate the work. In a single instance of Node.js, the work isn’t performed in parallel since there’s only a single thread executing JavaScript.



A single thread is used by Node.js to handle many clients, and while I/O operations can run asynchronously, the CPU-intensive code would not, so running potentially long CPU-bound tasks without proper techniques could result in a blocked event loop. This means all further operations are blocked until the end of the blocking operation. While the CPU-bound part of the code is executed synchronously in a single thread, the I/O operations are executed asynchronously, without blocking the execution in the main thread (hence the term non-blocking). It is the event loop which is responsible for coordinating the work, and deciding what should be executed at any given time. Essentially, this is how Node.js achieves concurrency.

Possible approaches for dealing with CPU-bound tasks in Node.js:
Splitting up tasks with setImmediate() (still using single-thread)
‘split’ these long-running tasks into smaller, less time-consuming pieces to allow other codes to run in-between 
Spawning a child process
run tasks as separate process, no need to defer steps and run additional tasks ‘in-between’ (adhere to Node’s rules and avoid blocking the event-loop)
Worker threads (latest and greatest approach)
enables the use of threads to execute JavaScript in parallel
run intensive tasks outside of main application’s event loop, therefore not blocking it (similar to child process)


Chen Pang
Node.js is optimised for I/O intensive tasks, which is what most web servers are doing and as a result, it compromises when dealing with CPU intensive tasks.
Node.js uses an Event Loop and non-blocking I/O to handle concurrency, while most other programming languages uses threads. 
A single node instance runs to execute tasks that are queued sequentially in the Event Loop, which allowed node.js to outperform other languages in I/O intensive tasks, while sacrificing its performance when running CPU intensive code.
When a client task takes too long to complete, e.g. a CPU intensive calculation or a slow API fetch response, the whole event loop thread will be blocked, resulting in poor response from subsequent tasks queued in the event loop.
The key to keeping node.js high performance is not to block the Event Loop, which means to keep the work of clients at any given time to a minimum.

Several solutions that can be applied to when dealing with CPU-intensive tasks in node.js:
Splitting up the tasks
Spawning a child process
Using worker threads

Splitting up Tasks with setImmediate()
Use setImmediate() to let the event loop take control of each step of execution
Costs overheads when calling setImmediate()
Only useful on single-thread CPU-intensive tasks

Spawning a child process using child_process API module
Create a new child process to handle the CPU-intensive task
Child process has its own memory, event-loop and V8 instance
Uses messages to communicate between child and parent processes
Allow the event loop to carry on while the child process continues
JS code can use multiple CPU cores

Using Worker Threads using the worker_threads API module
Enables the use of threads to execute code in parallel
While child processes execute in a different process, worker threads uses threads to execute the work within the same process of the main application
This key difference to child process means that:
Worker threads are more lightweight than child process - consumes less memory and start up faster
Worker threads can share memory - worker threads can transfer ArrayBuffer instances and share SharedArrayBuffer instances that hold raw binary data.
Each worker thread has its own node.js runtime (V8 instance, event loop) with its own isolated context.
No thread synchronisation needed as each worker thread operates in isolation.
Worker threads a lot safer than classic threads that share an execution context, e.g. no deadlocks, race-conditions etc.
Use a worker pool to reuse and manage workers/processes, e.g. workerpool library
Worker Thread Methods
new Worker(filename, [options]) - The constructor used to create new worker threads. The created worker threads will execute the contents of the module referred to by filename. Optionally, additional options can be provided. One common option is workerData.

worker.workerData - Can be any JavaScript value. This is a clone of the data passed to this worker thread constructor using the workerData option. Essentially, this is a way to provide a new worker thread with some data upon creation.

worker.isMainThread - Is true if the code is not running inside a worker thread, but rather in the main thread.

worker.parentPort - The worker’s end of the two-way communication channel between the worker thread and the parent thread. Messages sent from the worker thread using parentPort.postMessage() are then available in the parent thread using worker.on('message'). Conversely, messages sent from the parent thread using worker.postMessage() are then available in the worker thread using parentPort.on('message').



Maniraja

Node is, despite its asynchronous event model, by nature single threaded. When you launch a Node process, you are running a single process with a single thread on a single core. So your code will not be executed in parallel, only I/O operations are parallel because they are executed asynchronous. As such, long running CPU tasks will block the whole server and are usually a bad idea.

Node is flawless if you are having asynchronous tasks because java script will run these things by worker pool. But if you run CPU intense tasks (where you heavily use CPU ) Ex you have a billion users and you want to sort those people on name. Its quit a Intense tasks, and this is synchronous which will block other code from running.

when Node.js receives a CPU bound task: Whenever a heavy request comes to the event loop, Node.js would set all the CPU available to process it first, and then answer other requests queued. That results in slow processing and overall delay in the event loop, which is why Node.js is not recommended for heavy computation.

Heavy Computational Applications
If your application is likely to run tasks that involve heavy computing and number crunching, like running the Fibonacci algorithm, Node.js is not the best language to turn to. As discussed above, the heavy computation blocks the single-thread running in the application and halts the progress of the event loop until the computation is finished. And it delays serving the requests still in the queue, which may not take as much time. 

Backends with Relational Databases
Still, Node’s relational database support tools are not up to the expected level when compared to other languages. This makes Node an undesirable for use cases with relational databases. However, this situation is fast changing. High-quality ORM libraries like Sequealize and TypeORM are already available for Node developers. It may not take long to scratch this point out of the when-not-to-use-node.js list.
https://livecodestream.dev/post/when-you-should-and-should-not-use-nodejs-for-your-project/#:~:text=Node's%20single%2Dthreaded%20nature%20makes%20programs%20highly%20scalable.&text=js%20being%20single%2Dthreaded.,forward%20for%20a%20longer%20period.
 
SHA-1 hasher is CPU-bound
An example of a CPU-bound application would be a service that calculates SHA-1 checksums. The majority of the time is spent crunching the hash - shifting and bitwise xorring the input string.
This kind of application leads to trouble in Node.js. If the application spends too much time performing a CPU-intensive task, all other requests are being held up.
https://bytearcher.com/articles/io-vs-cpu-bound/
 


Keith: 

Node.js is single-threaded, event-driven, and asynchronous. 
Data-driven, I/O-driven, event-driven, and non-blocking applications benefit the best from a Node.js implementation.

In the default configuration Node.js can only do one thing at once so it will not handle any other requests or tasks while it is doing the CPU-bound task.

This is because Node.js's concurrency model is a single threaded event loop. Most other languages will split the CPU time between concurrent tasks, so if two long running CPU-bound requests come in they will process a little from the first request then a little from the second request, then back to the first and so on. Node.js will complete all of the first request, and then all of the second request. This is "faster" because there is less CPU wastage flicking between requests, but can give worse user experience because if a short request comes in while a long request is processing then the short request will take a lot longer than it would otherwise while Node.js completes the long running request.

There are, of course, ways of handling this. One way is to split your application into separate Node.js sub-applications one for each request type. This means that non-CPU-bound requests do not hold up CPU-bound requests. The other is to use worker threads (similar to web-workers) so that long running requests are handled in the background.

Is Node.js good for CPU intensive applications?
Nodejs is good for IO intensive tasks but bad for CPU intensive tasks. The reason Nodejs is bad for CPU intensive task is that it runs on the event loop, which runs on a single thread. The event loop is responsible for everything that runs on the user-land of Nodejs. This event loop runs on a single thread.
Why Node.js is not used in CPU intensive applications?
When you launch a Node process, you are running a single process with a single thread on a single core. So your code will not be executed in parallel, only I/O operations are parallel because they are executed asynchronous. As such, long running CPU tasks will block the whole server and are usually a bad idea.

What is CPU intensive?
So what are CPU Intensive tasks? They are complex user actions that eat up more RAM. A few of such processes can shut down your server entirely. Naturally, you want to make sure that your app or website is ‘smart’ enough to handle different kinds of tasks, for each individual user request.

Is Node.js memory intensive?
js assumes a memory ceiling of 1.5GB per node process. If you are running your node app in a memory constrained environment, e.g. a low-cost VPS server or PaaS instance, it’s necessary to inform the v8 runtime that you have a reduced memory ceiling.

What are the disadvantages of Node.js?
Node.js Disadvantages: What is Node.js not good for
Reduces performance when handling Heavy Computing Tasks.
Node.js invites a lot of code changes due to Unstable API.
Node.js Asynchronous Programming Model makes it difficult to maintain code.
Choose Wisely – Lack of Library Support can Endanger your Code.

What are the most CPU intensive applications?
Total War: Warhammer 2.
Rainbow Six Siege – Single-Core.
Shadow Of The Tomb Raider.
Far Cry 5 – Single-core.
Red Dead Redemption 2.
Best CPUs For CPU Demanding Games.

What apps use CPU intensive?
To cite another example of a CPU intensive task that is an everyday task instead of running a benchmark is any of the video editing, rendering, compression, or transcoding tasks.

How to solve CPU intensive requests in JavaScript?
You don’t want your CPU intensive code to execute async, you want it to execute in parallel. You need to get the processing work out of the thread that’s serving HTTP requests. It’s the only way to solve this problem. With Node.js the answer is the cluster module, for spawning child processes to do the heavy lifting.

What makes a Node.js server so fast?
Here’s a good rule of thumb for keeping your Node.js server speedy: Node.js is fast when the work associated with each client at any given time is “small”. This applies to callbacks on the Event Loop and tasks on the Worker Pool.

How does Node.js decide what to run next?
When submitting one of these tasks to the Worker Pool, Node.js provides a pointer to the corresponding C++ function in the Node.js C++ bindings. 

How do you handle CPU intensive tasks in Node.js?
Since Node.js is single-threaded and non-blocking, you can achieve higher concurrency with the same resources”. And when you read about what it’s bad at it usually goes like this: “Since Node.js is single-threaded, CPU-intensive tasks will block all requests from completing, until the task is completed.”

Why is Node.js not good for CPU intensive?
The single-threaded implementation makes Node a bad choice for CPU-intensive programs. When a time-consuming task is running in the program it blocks the event loop from moving forward for a longer period.

What are CPU intensive applications?
Is Node.js good for heavy computation?
Not Suitable for Heavy-Computing Apps Node.js doesn’t support multi-threaded programming yet. It is able to serve way more complicated applications than Ruby, but it’s not suitable for performing long-running calculations. Heavy computations block the incoming requests, which can lead to decrease of performance .

Is Node.js good for heavy computation?
Is Photoshop CPU or GPU intensive?
Photoshop is a very heavily CPU based application, and GPU acceleration is rarely utilised. Adobe has introduced more and more GPU accelerated tools and filters in recent years, but at this time, we recommend focusing more budget towards your memory and CPU.

How does Node.js maintain high level of concurrency?
Node.js apps manage to maintain a high level of concurrency by working as a series of events. When an event handler is waiting for something to happen (such as reading from the database), it tells Node to go ahead and process another event in the meantime.

What do you need to know about NodeJS?
With Node.js the answer is the cluster module, for spawning child processes to do the heavy lifting. (As far as I know Node.js doesn’t have any concept of threads/shared memory; it’s processes or nothing). You have two options for how you structure your application.


















```

## Submission Guidelines

- Cite any relevant sources consulted during your research
- Solve the problems using your own code
- Do not copy and paste solutions from the source material
- Submit your assignment to black board.
