# module 6

## Commit 1 Reflection notes
To handle incoming HTTP requests from a browser, I wrapped the TCP stream with BufReader. This allows me to read the stream line-by-line using .lines(). Each line from the stream represents a part of the HTTP request (e.g., the method, path, headers, etc.).
I used .map(|line| line.unwrap()) to convert the iterator from Result<String> to just String, and .take_while(|line| !line.is_empty()) to stop reading at the first empty line, which separates headers from the request body in HTTP.
By collecting these lines into a Vec<String>, I could clearly see how a browser sends a request to the server line by line. This helped me understand the internal structure of HTTP requests at a low level.


## Commit 2 Reflection Notes

![Commit 2 screen capture](/assets/images/commit2.png)

I learned how a web server can return an actual HTML file instead of just plain text. By modifying the handle_connection method, I was able to read the contents of an hello.html file and send it as part of the HTTP response. The fs::read_to_string() function made it very easy to read the entire file into memory, and by formatting it together with proper headers, I could return a response that browsers can correctly display.
One important thing I learned is the significance of Content-Length in the HTTP response. Without this header, some browsers might not render the page correctly. I also learned that every HTTP response needs to follow a particular structure, starting with a status line (HTTP/1.1 200 OK), followed by headers, and then an empty line before the actual body content.