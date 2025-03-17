
# module 6
 
## Commit 1 Reflection
To process incoming HTTP requests from a browser, I wrapped the TCP stream with a BufReader. This enables me to read the stream line-by-line using the .lines() method. Each line corresponds to a component of the HTTP request, such as the method, path, or headers.
I used .map(|result| result.unwrap()) to transform the iterator from Result<String> into plain String values, and .take_while(|line| !line.is_empty()) to halt reading at the first empty line, which marks the separation between headers and the request body in HTTP.
By gathering these lines into a Vec<String>, I was able to clearly observe how a browser constructs and sends a request to the server, line by line. This provided me with a deeper understanding of the low-level structure of HTTP requests.

## Commit 2 Reflection Notes
 
![Commit 2 screen capture](/assets/media/commit2.png)
 
I discovered how a web server can return an actual HTML file instead of just plain text. By updating the handle_connection method, I was able to read the contents of an hello.html file and include it in the HTTP response. Using the fs::read_to_string() function, I could easily load the entire file into memory, and by combining it with the appropriate headers, I created a response that browsers can properly display.
One key insight I gained was the importance of the Content-Length header in the HTTP response. Without it, some browsers might fail to render the page correctly. I also learned that every HTTP response must adhere to a specific structure: it begins with a status line (e.g., HTTP/1.1 200 OK), followed by headers, an empty line, and finally the body content.

## Commit 3 Reflection Notes
 
![Commit 3 screen capture](/assets/media/commit3.png)
 
I implemented conditional response logic in the handle_connection method to determine the appropriate response based on the request path. If the request line from the browser matches 'GET / HTTP/1.1', the server responds with a 200 OK status and serves hello.html. For any other path (e.g., /bad), it returns a 404 NOT FOUND status along with a custom 404.html error page.

In this implementation, only the root path / is considered valid, while any other URI results in a not-found response. To improve code maintainability, refactoring was introduced in the tutorial, breaking the logic into smaller, reusable components. This restructuring enhances readability and makes it easier to scale the web server in the future.

## Commit 4 Reflection Notes

To simulate a performance bottleneck, I added a /sleep route to the web server. When accessed, this route triggers a thread::sleep() call, causing the server to pause for 10 seconds before responding. This experiment demonstrated how a single-threaded server handles delays and high processing loads.

I noticed that while /sleep was running, the server became unresponsive to all other requests. Even attempting to load the normal / route in a different browser tab resulted in a delay until the /sleep request completed. This highlighted the limitations of a single-threaded architecture, which is not scalable.

Through this, I gained a deeper understanding of how real-world web servers handle concurrency. To prevent slow requests from blocking others, we need multithreading or asynchronous processing, ensuring better performance and responsiveness.

## Commit 5 Reflection Notes

To enhance server performance, we introduced a ThreadPool, enabling the server to handle multiple incoming connections simultaneously. Instead of spawning a new thread for each request, we initialized a fixed number of worker threads—four in this case—using ThreadPool::new(4). These threads remain active and are efficiently reused for handling requests.

Within the main loop, we assigned tasks to the thread pool using pool.execute. This approach is similar to thread::spawn() in Rust, where a closure is passed for execution. However, rather than continuously creating new threads, the thread pool distributes tasks among the pre-initialized worker threads, improving efficiency.

For the thread count parameter in ThreadPool::new(), we used the usize type, which represents non-negative whole numbers. Since thread counts cannot be negative, usize is a natural choice, aligning well with how we manage worker threads in a vector.

In the execute method, we defined the closure type using FnOnce(), ensuring that each closure is executed only once. The parentheses after FnOnce indicate that the closure takes no arguments and returns nothing (()), making it functionally equivalent to a standard parameterless function.4

## Commit Bonus Reflection notes

I added a build() function to the ThreadPool implementation as an alternative to the standard new() constructor. This was an experiment in naming conventions and API design in Rust. Functionally, build() is identical to new(), performing the same steps: creating a channel, wrapping it in a Mutex and Arc, and initializing worker threads.

Using a name like build() can be beneficial when constructing objects that require more complex setup or configuration. It also aligns with the builder pattern, which is common in many Rust libraries and frameworks. Although both new() and build() perform the same operations in this case, build() can make the code more readable and expressive.

The main distinction between new() and build() is in naming conventions. new() is the standard Rust idiom for straightforward object creation, while build() is typically used when the construction process is more flexible or configurable, often associated with the builder pattern.

