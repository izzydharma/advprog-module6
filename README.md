
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