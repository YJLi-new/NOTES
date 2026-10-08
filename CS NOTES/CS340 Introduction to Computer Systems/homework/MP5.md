# CS340 mp5

### `http.c`

```c
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <stdio.h>
#include "http.h"

/**
 * Parse HTTP request headers from the buffer and populate the HTTPRequest structure.
 * Returns the number of bytes used from the buffer.
 */
ssize_t httprequest_parse_headers(HTTPRequest *req, char *buffer, ssize_t buffer_len) {
    memset(req, 0, sizeof(HTTPRequest));

    char *line_end = strstr(buffer, "\r\n");
    if (!line_end) {
        return -1;
    }
    *line_end = '\0';

    char *method = strtok(buffer, " ");
    char *path = strtok(NULL, " ");
    char *version = strtok(NULL, " ");

    if (!method || !path || !version) {
        return -1;
    }

    req->action = strdup(method);
    req->path = strdup(path);
    req->version = strdup(version);

    req->headers = malloc(sizeof(char *) * 100);
    if (!req->headers) {
        return -1;
    }
    req->header_num = 0;

    char *header_line = line_end + 2;

    while (header_line < buffer + buffer_len && *header_line != '\0') {
        line_end = strstr(header_line, "\r\n");
        if (!line_end) {
            break;
        }
        if (header_line == line_end) {
            break;
        }
        *line_end = '\0';

        req->headers[req->header_num] = strdup(header_line);
        if (!req->headers[req->header_num]) {
            return -1;
        }
        req->header_num++;

        header_line = line_end + 2;
    }

    return header_line - buffer + 2;
}

/**
 * Read an HTTP request from the socket and populate the HTTPRequest structure.
 * Returns the total number of bytes read.
 */
ssize_t httprequest_read(HTTPRequest *req, int sockfd) {
    size_t buffer_size = 8192;
    size_t total_read = 0;
    char *buffer = malloc(buffer_size);
    if (!buffer) {
        return -1;
    }

    ssize_t bytes_read;
    char *headers_end = NULL;

    while (1) {
        bytes_read = read(sockfd, buffer + total_read, buffer_size - total_read - 1);
        if (bytes_read <= 0) {
            free(buffer);
            return -1;
        }
        total_read += bytes_read;
        buffer[total_read] = '\0';

        headers_end = strstr(buffer, "\r\n\r\n");
        if (headers_end) {
            break;
        }

        if (total_read >= buffer_size - 1) {
            buffer_size *= 2;
            char *new_buffer = realloc(buffer, buffer_size);
            if (!new_buffer) {
                free(buffer);
                return -1;
            }
            buffer = new_buffer;
        }
    }

    size_t headers_length = headers_end - buffer + 4;

    ssize_t parse_result = httprequest_parse_headers(req, buffer, headers_length);
    if (parse_result < 0) {
        free(buffer);
        return -1;
    }

    const char *content_length_str = httprequest_get_header(req, "Content-Length");
    if (content_length_str) {
        long content_length = atol(content_length_str);
        if (content_length < 0) {
            free(buffer);
            return -1;
        }
        req->length = content_length;

        size_t payload_already_read = total_read - headers_length;

        req->payload = malloc(content_length);
        if (!req->payload) {
            free(buffer);
            return -1;
        }

        if (payload_already_read > 0) {
            memcpy((void *)req->payload, buffer + headers_length, payload_already_read);
        }

        size_t payload_remaining = content_length - payload_already_read;
        while (payload_remaining > 0) {
            bytes_read = read(sockfd, (void *)req->payload + content_length - payload_remaining, payload_remaining);
            if (bytes_read <= 0) {
                free((void *)req->payload);
                free(buffer);
                return -1;
            }
            payload_remaining -= bytes_read;
            total_read += bytes_read;
        }
    } 
    else {
        req->payload = NULL;
        req->length = 0;
    }

    free(buffer);
    return total_read;
}

/**
 * Get the HTTP action verb from the request.
 */
const char *httprequest_get_action(HTTPRequest *req) {
    return req->action;
}

/**
 * Get the value of a specific header from the request.
 */
const char *httprequest_get_header(HTTPRequest *req, const char *key) {
    for (size_t i = 0; i < req->header_num; i++) {
        char *header = req->headers[i];
        size_t key_len = strlen(key);

        if (strncasecmp(header, key, key_len) == 0 && header[key_len] == ':') {
            char *value = header + key_len + 1;
            while (*value == ' ') {
                value++;
            }
            return value;
        }
    }
    return NULL;
}

/**
 * Get the requested path from the request.
 */
const char *httprequest_get_path(HTTPRequest *req) {
    return req->path;
}

/**
 * Free all memory associated with the HTTPRequest structure.
 */
void httprequest_destroy(HTTPRequest *req) {
    if (req->action) {
        free((void *)req->action);
    }
    if (req->path) {
        free((void *)req->path);
    }
    if (req->version) {
        free((void *)req->version);
    }
    if (req->payload) {
        free((void *)req->payload);
    }

    for (size_t i = 0; i < req->header_num; i++) {
        free((void *)req->headers[i]);
    }
    free(req->headers);
}
```

### `server.c`

```c
#include "http.h"

#include <string.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <netinet/in.h>
#include <pthread.h>

void *client_thread(void *vptr) {
    int fd = (int)(ssize_t)vptr;

    HTTPRequest req;
    ssize_t read_result = httprequest_read(&req, fd);
    if (read_result <= 0) {
        close(fd);
        pthread_exit(NULL);
    }

    const char *path = httprequest_get_path(&req);
    if (strcmp(path, "/") == 0) {
        path = "/index.html";
    }

    char full_path[1024];
    snprintf(full_path, sizeof(full_path), "static%s", path);

    FILE *file = fopen(full_path, "rb");
    if (!file) {
        const char *response_404 = "HTTP/1.1 404 Not Found\r\n"
                                   "Content-Type: text/plain\r\n"
                                   "Content-Length: 13\r\n"
                                   "\r\n"
                                   "404 Not Found";
        write(fd, response_404, strlen(response_404));
    }
    else {
        fseek(file, 0, SEEK_END);
        long file_size = ftell(file);
        rewind(file);

        char *file_contents = malloc(file_size);
        if (!file_contents) {
            fclose(file);
            close(fd);
            pthread_exit(NULL);
        }

        fread(file_contents, 1, file_size, file);
        fclose(file);

        const char *content_type = "application/octet-stream";
        if (strstr(full_path, ".html")) {
            content_type = "text/html";
        }
        else if (strstr(full_path, ".png")) {
            content_type = "image/png";
        }

        char header_buffer[1024];
        int header_length = snprintf(header_buffer, sizeof(header_buffer),
                                     "HTTP/1.1 200 OK\r\n"
                                     "Content-Type: %s\r\n"
                                     "Content-Length: %ld\r\n"
                                     "\r\n", content_type, file_size);

        write(fd, header_buffer, header_length);

        write(fd, file_contents, file_size);

        free(file_contents);
    }

    httprequest_destroy(&req);
    close(fd);
    pthread_exit(NULL);
}

int main(int argc, char *argv[]) {
  if (argc != 2) {
    printf("Usage: %s <port>\n", argv[0]);
    return 1;
  }
  int port = atoi(argv[1]);
  printf("Binding to port %d. Visit http://localhost:%d/ to interact with your server!\n", port, port);

  int sockfd = socket(AF_INET, SOCK_STREAM, 0);

  struct sockaddress_in {
    server_address, 
    client_address;
  }

  memset(&server_address, 0x00, sizeof(server_address));
  server_address.sin_family = AF_INET;
  server_address.sin_addr.s_addr = INADDR_ANY;
  server_address.sin_port = htons(port);  
  bind(sockfd, (const struct sockaddress *)&server_address, sizeof(server_address));

  listen(sockfd, 10);

  socklen_t client_addr_len;
  while (1) {
    client_addr_len = sizeof(struct sockaddress_in);
    int fd = accept(sockfd, (struct sockaddress *)&client_address, &client_addr_len);
    printf("Client connected (fd=%d)\n", fd);

    pthread_t tid;
    pthread_create(&tid, NULL, client_thread, (void *)(ssize_t)fd);
    pthread_detach(tid);
  }

  return 0;
}
```
