# CS340 mp7

### `chunkservice.py`

```python
from aiohttp.web import Application, run_app, RouteTableDef, Request, Response, FileResponse, StreamResponse
import asyncio
import os

routes = RouteTableDef()

@routes.get('/')
async def index(req : Request) -> StreamResponse:
    return FileResponse(path="index.html")

# TO DO: add more API endpoints here

@routes.post('/extract')
async def extract(req: Request) -> Response:
    os.makedirs('temp', exist_ok=True)
    temp = 'temp'

    reader = await req.multipart()
    png_part = None

    try:
        post = await req.post()
    except Exception as e:
        return Response(status=415, text="Unsupported Media Type")

    png_file = post.get('png')

    if png_file is None:
        return Response(status=400, text="No PNG file uploaded.")

    png_data = png_file.file.read()

    if not png_data.startswith(b'\x89PNG\r\n\x1a\n'):
        return Response(status=415, text="Invalid PNG image file")

    unique_id = id(asyncio.current_task())
    input_png_path = os.path.join(temp, f"input{unique_id}.png")
    output_file_path = os.path.join(temp, f"output{unique_id}")

    os.makedirs(os.path.dirname(input_png_path), exist_ok=True)

    with open(input_png_path, 'wb') as fp:
        fp.write(png_data)

    process = await asyncio.create_subprocess_exec(
        './extractuiucchunk',
        input_png_path,
        output_file_path,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE
    )
    stdout, stderr = await process.communicate()

    if process.returncode != 0:
        stderr_text = stderr.decode()
        if "No uiuc chunk found" in stderr_text:
            if os.path.exists(input_png_path):
                os.remove(input_png_path)
            return Response(status=422, text="No uiuc chunk found in the PNG image")
        elif "Missing IHDR or IEND chunk" in stderr_text:
            if os.path.exists(input_png_path):
                os.remove(input_png_path)
            return Response(status=400, text="Incomplete PNG file structure.")
        else:
            if os.path.exists(input_png_path):
                os.remove(input_png_path)
            return Response(status=500, text="Failed to extract data from PNG image.")

    if not os.path.exists(output_file_path) or os.path.getsize(output_file_path) == 0:
        if os.path.exists(input_png_path):
            os.remove(input_png_path)
        return Response(status=422, text="No uiuc chunk found in the PNG image.")

    with open(output_file_path, 'rb') as fp:
        extracted_data = fp.read()

    charset = None
    if extracted_data.startswith(b'\x89PNG\r\n\x1a\n'):
        content_type = 'image/png'
    elif extracted_data.startswith(b'\xFF\xD8\xFF'):
        content_type = 'image/jpeg'
    elif extracted_data.startswith(b'GIF87a') or extracted_data.startswith(b'GIF89a'):
        content_type = 'image/gif'
    elif extracted_data.startswith(b'%PDF-'):
        content_type = 'application/pdf'
    elif extracted_data.startswith(b'\x50\x4B\x03\x04'):
        content_type = 'application/zip'
    else:
        try:
            extracted_data.decode('utf-8')
            content_type = 'text/plain'
            charset = 'utf-8'
        except UnicodeDecodeError:
            content_type = 'application/octet-stream'

    return Response(body=extracted_data, content_type=content_type, charset=charset)

@routes.post('/insert')
async def insert(req: Request) -> Response:
    os.makedirs('temp', exist_ok=True)
    temp = 'temp'

    reader = await req.multipart()
    png_part = None
    hide_part = None

    try:
        post = await req.post()
    except Exception as e:
        return Response(status=415, text="Unsupported Media Type")

    png_file = post.get('png')
    hide_file = post.get('hide')

    if png_file is None or hide_file is None:
        return Response(status=400, text="Missing PNG image and data to hide")

    png_data = png_file.file.read()
    hide_data = hide_file.file.read()

    if not png_data.startswith(b'\x89PNG\r\n\x1a\n'):
        return Response(status=415, text="Invalid PNG image file")

    unique_id = id(asyncio.current_task())
    input_png_path = os.path.join(temp, f"input{unique_id}.png")
    hide_data_path = os.path.join(temp, f"hide{unique_id}")
    output_png_path = os.path.join(temp, f"output{unique_id}.png")

    os.makedirs(os.path.dirname(input_png_path), exist_ok=True)

    with open(input_png_path, 'wb') as fp:
        fp.write(png_data)

    os.makedirs(os.path.dirname(hide_data_path), exist_ok=True)

    with open(hide_data_path, 'wb') as fp:
        fp.write(hide_data)

    process = await asyncio.create_subprocess_exec(
        './insertuiucchunk',
        input_png_path,
        hide_data_path,
        output_png_path,
        stdout=asyncio.subprocess.PIPE,
        stderr=asyncio.subprocess.PIPE
    )
    stdout, stderr = await process.communicate()

    if process.returncode != 0:
        stderr_text = stderr.decode()
        if "Missing IHDR or IEND chunk" in stderr_text:
            if os.path.exists(input_png_path):
                os.remove(input_png_path)
            if os.path.exists(hide_data_path):
                os.remove(hide_data_path)
            return Response(status=400, text="Incomplete PNG file structure.")
        else:
            if os.path.exists(input_png_path):
                os.remove(input_png_path)
            if os.path.exists(hide_data_path):
                os.remove(hide_data_path)
            return Response(status=500, text="Failed to insert data")

    if not os.path.exists(output_png_path) or os.path.getsize(output_png_path) == 0:
        if os.path.exists(input_png_path):
            os.remove(input_png_path)
        if os.path.exists(hide_data_path):
            os.remove(hide_data_path)
        return Response(status=500, text="Failed to insert data")

    with open(output_png_path, 'rb') as fp:
        new_png_data = fp.read()

    if os.path.exists(input_png_path):
        os.remove(input_png_path)
    if os.path.exists(hide_data_path):
        os.remove(hide_data_path)
    if os.path.exists(output_png_path):
        os.remove(output_png_path)

    return Response(body=new_png_data, content_type='image/png')

def setup_app(app):
    os.makedirs('temp', exist_ok=True)
    app.add_routes(routes)
    
# To facilitate testing, do not change anything below this line
if __name__ == '__main__': 
    app = Application()
    setup_app(app)
    run_app(app, host='0.0.0.0', port=5000) # this function never returns
```

### `insertuiucchunk.c`

```c
#include "pnglib.h"

int main(int argc, char *argv[]) {
    if (argc != 4) {
        fprintf(stderr, "USAGE: %s input.png insertfile output.png\n", argv[0]);
        return 1;
    }
    // argv[1] is the input .png file
    // argv[2] is the filename to store in the uiuc chunk
    // argv[3] is the output .png file

    const char *input_png_filename = argv[1];
    const char *insert_file = argv[2];
    const char *output_png_filename = argv[3];

    FILE *fp_in = fopen(input_png_filename, "rb");
    if (fp_in == NULL) {
        fprintf(stderr, "Fail to open the file %s\n", input_png_filename);
        exit(2);
    }

    if (!PNGSignatureValidate(fp_in, input_png_filename)) {
        fclose(fp_in);
        exit(3);
    }

    if (fseek(fp_in, 0, SEEK_SET) != 0) {
        fprintf(stderr, "Failed to rewind file %s\n", input_png_filename);
        fclose(fp_in);
        exit(1);
    }

    FILE *fp_out = fopen(output_png_filename, "wb");
    if (fp_out == NULL) {
        fprintf(stderr, "Fail to open the file %s\n", output_png_filename);
        fclose(fp_in);
        exit(2);
    }

    unsigned char signature[8];
    if (fread(signature, 1, 8, fp_in) != 8) {
        fprintf(stderr, "Failed to read PNG signature from %s\n", input_png_filename);
        fclose(fp_in);
        fclose(fp_out);
        exit(1);
    }
    if (fwrite(signature, 1, 8, fp_out) != 8) {
        fprintf(stderr, "Failed to write PNG signature to %s\n", output_png_filename);
        fclose(fp_in);
        fclose(fp_out);
        exit(1);
    }

    char type[5];
    int replaced = 0;
    int exist_IHDR = 0;
    int exist_IEND = 0;

    while (1) {
        uint32_t length = readChunkDataLength(fp_in, input_png_filename);

        if (!readChunkType(fp_in, type, input_png_filename)) {
            fprintf(stderr, "Failed to read chunk type from %s\n", input_png_filename);
            fclose(fp_in);
            fclose(fp_out);
            exit(4);
        }

        unsigned char *data = NULL;
        if (length > 0) {
            data = malloc(length);
            if (data == NULL) {
                fprintf(stderr, "Memory allocation failed\n");
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (fread(data, 1, length, fp_in) != length) {
                fprintf(stderr, "Failed to read chunk data from %s\n", input_png_filename);
                free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
        }

        unsigned char crc_bytes[4];
        if (fread(crc_bytes, 1, 4, fp_in) != 4) {
            fprintf(stderr, "Failed to read CRC from %s\n", input_png_filename);
            if (data) free(data);
            fclose(fp_in);
            fclose(fp_out);
            exit(1);
        }

        uint32_t read_crc = (crc_bytes[0] << 24) | (crc_bytes[1] << 16) | (crc_bytes[2] << 8) | crc_bytes[3];
        uint32_t calculated_crc = calculateCRC(type, data, length);
        if (calculated_crc != read_crc) {
            fprintf(stderr, "CRC mismatch for chunk %s in file %s\n", type, input_png_filename);
            if (data) free(data);
            fclose(fp_in);
            fclose(fp_out);
            exit(5);
        }

        if (strncmp(type, "IHDR", 4) == 0) {
            if (exist_IHDR) {
                fprintf(stderr, "Multiple IHDR chunks exist\n");
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(4);
            }
            exist_IHDR = 1;
        } else if (strncmp(type, "IEND", 4) == 0) {
            exist_IEND = 1;
        }

        if (strncmp(type, "uiuc", 4) == 0 && !replaced) {
            FILE *fp_insert = fopen(insert_file, "rb");
            if (fp_insert == NULL) {
                fprintf(stderr, "Fail to open the file %s\n", insert_file);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(2);
            }

            if (fseek(fp_insert, 0, SEEK_END) != 0) {
                fprintf(stderr, "Failed to seek to end of insert file %s\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            long insert_size_long = ftell(fp_insert);
            if (insert_size_long < 0) {
                fprintf(stderr, "Failed to determine size of insert file %s\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (insert_size_long > UINT32_MAX) {
                fprintf(stderr, "Insert file %s is too large\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            uint32_t insert_size = (uint32_t)insert_size_long;
            if (fseek(fp_insert, 0, SEEK_SET) != 0) {
                fprintf(stderr, "Failed to seek to beginning of insert file %s\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            unsigned char *insert_data = malloc(insert_size);
            if (insert_data == NULL) {
                fprintf(stderr, "Memory allocation failed\n");
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            if (fread(insert_data, 1, insert_size, fp_insert) != insert_size) {
                fprintf(stderr, "Failed to read insert data from %s\n", insert_file);
                free(insert_data);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            fclose(fp_insert);

            uint32_t uiuc_length_be = htonl(insert_size);
            if (fwrite(&uiuc_length_be, 4, 1, fp_out) != 1) {
                fprintf(stderr, "Failed to write 'uiuc' chunk length to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (fwrite("uiuc", 1, 4, fp_out) != 4) {
                fprintf(stderr, "Failed to write 'uiuc' chunk type to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (fwrite(insert_data, 1, insert_size, fp_out) != insert_size) {
                fprintf(stderr, "Failed to write 'uiuc' chunk data to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            uint32_t crc = calculateCRC("uiuc", insert_data, insert_size);
            uint32_t crc_be = htonl(crc);
            if (fwrite(&crc_be, 4, 1, fp_out) != 1) {
                fprintf(stderr, "Failed to write 'uiuc' chunk CRC to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            free(insert_data);
            replaced = 1;

            if (data) free(data);
            continue;
        }

        if (strncmp(type, "IEND", 4) == 0 && !replaced) {
            FILE *fp_insert = fopen(insert_file, "rb");
            if (fp_insert == NULL) {
                fprintf(stderr, "Fail to open the file %s\n", insert_file);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(2);
            }

            if (fseek(fp_insert, 0, SEEK_END) != 0) {
                fprintf(stderr, "Failed to seek to end of insert file %s\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            long insert_size_long = ftell(fp_insert);
            if (insert_size_long < 0) {
                fprintf(stderr, "Failed to determine size of insert file %s\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (insert_size_long > UINT32_MAX) {
                fprintf(stderr, "Insert file %s is too large\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            uint32_t insert_size = (uint32_t)insert_size_long;
            if (fseek(fp_insert, 0, SEEK_SET) != 0) {
                fprintf(stderr, "Failed to seek to beginning of insert file %s\n", insert_file);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            unsigned char *insert_data = malloc(insert_size);
            if (insert_data == NULL) {
                fprintf(stderr, "Memory allocation failed\n");
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            if (fread(insert_data, 1, insert_size, fp_insert) != insert_size) {
                fprintf(stderr, "Failed to read insert data from %s\n", insert_file);
                free(insert_data);
                fclose(fp_insert);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            fclose(fp_insert);

            uint32_t uiuc_length_be = htonl(insert_size);
            if (fwrite(&uiuc_length_be, 4, 1, fp_out) != 1) {
                fprintf(stderr, "Failed to write 'uiuc' chunk length to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (fwrite("uiuc", 1, 4, fp_out) != 4) {
                fprintf(stderr, "Failed to write 'uiuc' chunk type to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }
            if (fwrite(insert_data, 1, insert_size, fp_out) != insert_size) {
                fprintf(stderr, "Failed to write 'uiuc' chunk data to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            uint32_t crc = calculateCRC("uiuc", insert_data, insert_size);
            uint32_t crc_be = htonl(crc);
            if (fwrite(&crc_be, 4, 1, fp_out) != 1) {
                fprintf(stderr, "Failed to write 'uiuc' chunk CRC to %s\n", output_png_filename);
                free(insert_data);
                if (data) free(data);
                fclose(fp_in);
                fclose(fp_out);
                exit(1);
            }

            free(insert_data);
            replaced = 1;
        }

        uint32_t length_be = htonl(length);
        if (fwrite(&length_be, 4, 1, fp_out) != 1) {
            fprintf(stderr, "Failed to write chunk length to %s\n", output_png_filename);
            if (data) free(data);
            fclose(fp_in);
            fclose(fp_out);
            exit(1);
        }
        if (fwrite(type, 1, 4, fp_out) != 4) {
            fprintf(stderr, "Failed to write chunk type to %s\n", output_png_filename);
            if (data) free(data);
            fclose(fp_in);
            fclose(fp_out);
            exit(1);
        }
        if (length > 0 && fwrite(data, 1, length, fp_out) != length) {
            fprintf(stderr, "Failed to write chunk data to %s\n", output_png_filename);
            if (data) free(data);
            fclose(fp_in);
            fclose(fp_out);
            exit(1);
        }
        if (fwrite(crc_bytes, 1, 4, fp_out) != 4) {
            fprintf(stderr, "Failed to write chunk CRC to %s\n", output_png_filename);
            if (data) free(data);
            fclose(fp_in);
            fclose(fp_out);
            exit(1);
        }

        if (data) free(data);

        if (strncmp(type, "IEND", 4) == 0) {
            break;
        }
    }

    fclose(fp_in);
    fclose(fp_out);

    if (!exist_IHDR || !exist_IEND) {
        fprintf(stderr, "IHDR or IEND chunk is missing\n");
        exit(4);
    }

    return 0;
}
```

### `extractuiucchunk.c`

```c
#include "pnglib.h"

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "USAGE: %s existingfilename.png outputfile\n", argv[0]);
        return 1;
    }
    // argv[1] is the input .png file
    // argv[2] is the filename to save the uiuc chunk data to

    const char *input_png_filename = argv[1];
    const char *output_filename = argv[2];
    FILE *fp = fopen(input_png_filename, "rb");
    if (fp == NULL) {
        fprintf(stderr, "Fail to open the file %s\n", input_png_filename);
        exit(2);
    }

    if (!PNGSignatureValidate(fp, input_png_filename)) {
        fclose(fp);
        exit(3);
    }

    int exist_IHDR = 0;
    int exist_IEND = 0;
    char type[5];
    int found = 0;

    while (1) {
        uint32_t length = readChunkDataLength(fp, input_png_filename);

        if (!readChunkType(fp, type, input_png_filename)) {
            fclose(fp);
            exit(4);
        }

        unsigned char *data = NULL;
        if (length > 0) {
            data = malloc(length);
            if (data == NULL) {
                fprintf(stderr, "Memory allocation failed\n");
                fclose(fp);
                exit(1);
            }
            if (fread(data, 1, length, fp) != length) {
                fprintf(stderr, "Failed to read data of chunk %s\n", type);
                free(data);
                fclose(fp);
                exit(1);
            }
        }

        unsigned char crc_bytes[4];
        if (fread(crc_bytes, 1, 4, fp) != 4) {
            fprintf(stderr, "Failed to read CRC of chunk %s in file %s\n", type, input_png_filename);
            if (data) free(data);
            fclose(fp);
            exit(1);
        }

        uint32_t read_crc = (crc_bytes[0] << 24) | (crc_bytes[1] << 16) | (crc_bytes[2] << 8) | crc_bytes[3];
        uint32_t calculated_crc = calculateCRC(type, data, length);
        if (calculated_crc != read_crc) {
            fprintf(stderr, "CRC mismatch for chunk %s in file %s\n", type, input_png_filename);
            if (data) free(data);
            fclose(fp);
            exit(5);
        }

        if (strncmp(type, "IHDR", 4) == 0) {
            exist_IHDR = 1;
        } else if (strncmp(type, "IEND", 4) == 0) {
            exist_IEND = 1;
            if (fgetc(fp) != EOF) {
                fprintf(stderr, "Data exists after IEND chunk\n");
                if (data) free(data);
                fclose(fp);
                exit(4);
            }
            if (data) free(data);
            break;
        }

        if (strncmp(type, "uiuc", 4) == 0) {
            FILE *out_fp = fopen(output_filename, "wb");
            if (out_fp == NULL) {
                fprintf(stderr, "Fail to open the file %s\n", output_filename);
                if (data) free(data);
                fclose(fp);
                exit(2);
            }

            if (fwrite(data, 1, length, out_fp) != length) {
                fprintf(stderr, "Failed to write to %s\n", output_filename);
                if (data) free(data);
                fclose(out_fp);
                fclose(fp);
                exit(1);
            }

            fclose(out_fp);
            if (data) free(data);
            found = 1;
            continue;
        }

        if (data) free(data);

        if (feof(fp)) {
            break;
        }
    }

    fclose(fp);

    if (!exist_IHDR || !exist_IEND) {
        fprintf(stderr, "IHDR or IEND chunk is missing\n");
        exit(4);
    }

    if (!found) {
        fprintf(stderr, "No uiuc chunk found in %s\n", input_png_filename);
        exit(4);
    }

    return 0;
}
```

### `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">

    <title>CS 340: PNG Microservice</title>
  </head>
  <body>
    <div class="container">
      <h1>Extract hidden data</h1>

      <form method="POST" enctype="multipart/form-data" action="/extract">
        <div class="form-group">
          <label for="png">PNG File</label>
          <input type="file"  class="form-control" id="png" name="png">
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
      </form>
    </div>

    <div class="container">
      <h1>Hide data</h1>

      <form method="POST" enctype="multipart/form-data" action="/insert">
        <div class="form-group">
          <label for="png">PNG File</label>
          <input type="file"  class="form-control" id="png" name="png">
          <label for="hide">File to insert</label>
          <input type="file"  class="form-control" id="hide" name="hide">
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
      </form>
    </div>
  </body>
</html>
```
