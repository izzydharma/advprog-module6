
# module 6
 
## Commit 1 Reflection
To process incoming HTTP requests from a browser, I wrapped the TCP stream with a BufReader. This enables me to read the stream line-by-line using the .lines() method. Each line corresponds to a component of the HTTP request, such as the method, path, or headers.
I used .map(|result| result.unwrap()) to transform the iterator from Result<String> into plain String values, and .take_while(|line| !line.is_empty()) to halt reading at the first empty line, which marks the separation between headers and the request body in HTTP.
By gathering these lines into a Vec<String>, I was able to clearly observe how a browser constructs and sends a request to the server, line by line. This provided me with a deeper understanding of the low-level structure of HTTP requests.

