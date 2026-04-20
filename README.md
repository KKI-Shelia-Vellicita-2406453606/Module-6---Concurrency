## Commit 1 Reflection Notes 

The 'handle_connection' function uses a 'BufReader' to buffer the raw 'TcpStream' in memory, significantly improving performance by reducing direct system calls. It employs 'take_while(|line| !line.is_empty())' to recognize the empty line that marks the end of HTTP headers in a standard request. This stop condition is essential because, without it, the server would wait indefinitely for more data, causing a deadlock where the browser expects a response while the server remains stuck in a reading loop.

## Commit 2 Reflection Notes

The 'handle_connection' function was updated to return a functional HTTP response by reading the 'hello.html' file using 'fs::read_to_string'. To comply with the HTTP/1.1 protocol, a response string is constructed using the 'format!' macro, which includes a status line ('HTTP/1.1 200 OK'), a 'Content-Length' header, and a crucial double line break ('\r\n\r\n') that acts as a delimiter between the headers and the HTML body. This formatted string is then converted into bytes via 'as_bytes()' and sent through 'stream.write_all', ensuring the browser receives all necessary metadata to correctly interpret and render the payload.

## Commit 3 Reflection Notes

The handle_connection function was refactored to introduce basic routing logic by validating the initial request line. By utilizing 'buf_reader.lines().next()', the server efficiently isolates the specific HTTP method and path requested by the browser without needing to parse the entire header block. This allows the implementation of conditional branching: if the request line matches GET / HTTP/1.1, the server returns a 200 OK status with the content of 'hello.html'. Otherwise, it defaults to an HTTP/1.1 404 NOT FOUND status serving '404.html'. This change is a significant step forward, as it enables the server to provide context-aware responses and properly inform the client when a requested resource does not exist.