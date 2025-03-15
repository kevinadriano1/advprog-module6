# module 6

## Commit 1 Reflection notes
To handle incoming HTTP requests from a browser, I wrapped the TCP stream with BufReader. This allows me to read the stream line-by-line using .lines(). Each line from the stream represents a part of the HTTP request (e.g., the method, path, headers, etc.).
I used .map(|line| line.unwrap()) to convert the iterator from Result<String> to just String, and .take_while(|line| !line.is_empty()) to stop reading at the first empty line, which separates headers from the request body in HTTP.
By collecting these lines into a Vec<String>, I could clearly see how a browser sends a request to the server line by line. This helped me understand the internal structure of HTTP requests at a low level.


## Commit 2 Reflection Notes

![Commit 2 screen capture](/assets/images/commit2.png)

I learned how a web server can return an actual HTML file instead of just plain text. By modifying the handle_connection method, I was able to read the contents of an hello.html file and send it as part of the HTTP response. The fs::read_to_string() function made it very easy to read the entire file into memory, and by formatting it together with proper headers, I could return a response that browsers can correctly display.
One important thing I learned is the significance of Content-Length in the HTTP response. Without this header, some browsers might not render the page correctly. I also learned that every HTTP response needs to follow a particular structure, starting with a status line (HTTP/1.1 200 OK), followed by headers, and then an empty line before the actual body content.

## Commit 3 Reflection Notes

![Commit 3 screen capture](/assets/images/commit3.png)

I implemented conditional response logic in the handle_connection method. The key idea was to check the request line from the browser and return different pages depending on the path. If the path is /, the server responds with a 200 OK status and shows hello.html. If the path is anything else (e.g., /bad), it returns a 404 NOT FOUND status and serves a custom 404.html file.  In this example we only treat the URI '/' as a valid request, hence if the status line is 'GET / HTTP/1.1' then we give them the normal hello.html, with a success status code. If it is anything else, that means that it is an invalid URI, in which case we return an error not found status code and error page. Refactoring is introduced in the tutorial to clean up the code and improve maintainability. Instead of keeping all logic in one function, we can break it into smaller, reusable components. This makes the code easier to understand and allows us to scale our web server in the future.


## commit 4 Reflection Notes

I simulated a performance bottleneck by creating a /sleep route in the web server. When the /sleep route is accessed, the server uses thread::sleep() to pause for 10 seconds before responding. This effectively shows how a single-threaded server behaves under delay or high processing load. When I opened /sleep in one browser tab, the server did not respond to other requests until the 10-second delay finished. Even when accessing the normal / route in another tab, the page did not load until /sleep was done.  I realized that single-threaded architecture is not scalable. In real web servers, we must use multithreading or asynchronous handling so that slow or delayed requests do not block other users. This helped me understand how concurrency and performance go hand-in-hand in web server design.

## commit 5 Reflection Notes

In this part of the project, we improved the performance of our server by introducing a ThreadPool, which allows the server to handle multiple incoming connections at the same time. Instead of creating a new thread for each request, we created a fixed number of threads (in this case, four) using ThreadPool::new(4). These threads stay active and are reused to handle incoming requests efficiently.

Inside the main loop, we used pool.execute to assign a task (a closure) to the thread pool. This is similar to how thread::spawn() works in Rust, where a closure is passed to be executed. However, instead of constantly creating new threads, the thread pool sends the task to one of the pre-initialized worker threads to execute it.

We used the type usize for the thread count parameter in ThreadPool::new() because it represents only non-negative whole numbers. This makes sense since the number of threads can't be negative, and usize also fits well with how we manage the worker threads using a vector.

In the execute method, we defined the closure type using FnOnce(). This means the closure will only be called once, which fits our use case of handling a single connection per task. The parentheses after FnOnce indicate that the closure takes no arguments and returns nothing (unit type ()), just like a normal function with no parameters.

## Commit Bonus Reflection notes
I added a build() function to the ThreadPool implementation, which acts as an alternative to the standard new() constructor. The goal of this addition was to experiment with naming conventions and better understand API design practices in Rust. The build() method is functionally identical to new(), performing the same steps of creating a channel, wrapping it in a Mutex and Arc, and initializing the worker threads.
Using a function name like build() can be useful when the object being created involves more complex setup or configuration, or when the library is expected to expand into a builder pattern in the future. This is common in many Rust libraries and frameworks. Although in this case both new() and build() perform the same actions, using build() can make it more expressive for developers reading the code.
The difference between new() and build() lies in naming convention—new() is the standard idiomatic method for simple object creation in Rust, while build() is often used to imply a more configurable or complex construction process, commonly seen in builder patterns.