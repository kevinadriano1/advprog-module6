# module 6

## Commit 1 Reflection notes
To handle incoming HTTP requests from a browser, I wrapped the TCP stream with BufReader. This allows me to read the stream line-by-line using .lines(). Each line from the stream represents a part of the HTTP request (e.g., the method, path, headers, etc.).
I used .map(|line| line.unwrap()) to convert the iterator from Result<String> to just String, and .take_while(|line| !line.is_empty()) to stop reading at the first empty line, which separates headers from the request body in HTTP.
By collecting these lines into a Vec<String>, I could clearly see how a browser sends a request to the server line by line. This helped me understand the internal structure of HTTP requests at a low level.