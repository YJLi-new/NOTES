# CS340 mp3

### `pnglib.c`

```c
#include "pnglib.h"

static uint32_t crc_table[256];
static int crc_table_result = 0;

int PNGSignatureValidate(FILE *fp, const char *png_filename) {
    unsigned char png_signature[8] = {0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A};
    unsigned char file_signature[8];
    
    if (fread(file_signature, 1, 8, fp) != 8) {
        fprintf(stderr, "The file %s is too short to be a valid PNG\n", png_filename);
        return 0;
    }
    
    if (memcmp(file_signature, png_signature, 8) != 0) {
        fprintf(stderr, "The file %s has an invalid PNG signature\n", png_filename);
        return 0;
    }
    
    return 1;
}

uint32_t readChunkDataLength(FILE *fp, const char *png_filename) {
    uint32_t length;
    
    if (fread(&length, 4, 1, fp) != 1) {
        fprintf(stderr, "Unexpected end was met when reading the data length in the file %s\n", png_filename);
        exit(4);
    }

    return ntohl(length);
}

int readChunkType(FILE *fp, char *type, const char *png_filename) {
    unsigned char chunk_types_space[4];
    if (fread(chunk_types_space, 1, 4, fp) != 4) {
        fprintf(stderr, "Unexpected end was met when reading the chunk types in the file %s\n", png_filename);
        exit(4);
    }
    memcpy(type, chunk_types_space, 4);
    type[4] = '\0';

    return 1;
}

uint32_t update_crc(uint32_t original_crc, const unsigned char *buf, int len) {
    uint32_t new_crc = original_crc;
    int n;
    
    if (!crc_table_result) {
        uint32_t c;
        for (int i = 0; i < 256; i++) {
            c = (uint32_t)i;
            for (int j = 0; j < 8; j++) {
                if (c & 1)
                    c = 0xEDB88320L ^ (c >> 1);
                else
                    c = c >> 1;
            }
            crc_table[i] = c;
        }
        crc_table_result = 1;
    }
    for (n = 0; n < len; n++) {
        new_crc = crc_table[(new_crc ^ buf[n]) & 0xFF] ^ (new_crc >> 8);
    }
    return new_crc;
}

uint32_t calculateCRC(const char *type, const unsigned char *data, uint32_t length) {
    uint32_t result_crc = 0xFFFFFFFF;

    result_crc = update_crc(result_crc, (const unsigned char *)type, 4);

    if (length > 0) {
        result_crc = update_crc(result_crc, data, length);
    }

    result_crc ^= 0xFFFFFFFF;
    
    return result_crc;
}
```

### `pngchunklist.c`

```c
#include "pnglib.h"

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "USAGE: %s existingfilename.png\n", argv[0]);
        return 1;
    }
    // argv[1] is the input .png file

    const char *input_png_filename = argv[1];
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
    int chunk_count = 0;

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
                exit(4);
            }
            if (fread(data, 1, length, fp) != length) {
                fprintf(stderr, "Unexpected end when reading data of chunk %s in file %s\n", type, input_png_filename);
                free(data);
                fclose(fp);
                exit(4);
            }
        }

        unsigned char crc_space[4];
        if (fread(crc_space, 1, 4, fp) != 4) {
            fprintf(stderr, "Unexpected end when reading CRC of chunk %s in file %s\n", type, input_png_filename);
            if (data) free(data);
            fclose(fp);
            exit(4);
        }

        uint32_t read_crc = (crc_space[0] << 24) | (crc_space[1] << 16) | (crc_space[2] << 8) | crc_space[3];
        uint32_t calculated_crc = calculateCRC(type, data, length);
        if (calculated_crc != read_crc) {
            printf("%s %u\n", type, length);
            fprintf(stderr, "CRC mismatch for the chunk %s in file %s\n", type, input_png_filename);
            if (data) free(data);
            fclose(fp);
            exit(5);
        }

        if (chunk_count == 0 && strncmp(type, "IHDR", 4) != 0) {
            fprintf(stderr, "IHDR chunk is not the first chunk\n");
            if (data) free(data);
            fclose(fp);
            exit(4);
        }

        printf("%s %u\n", type, length);

        if (strncmp(type, "IHDR", 4) == 0) {
            if (exist_IHDR) {
                fprintf(stderr, "Multiple IHDR chunks exist\n");
                if (data) free(data);
                fclose(fp);
                exit(4);
            }
            exist_IHDR = 1;
            if (length != 13) {
                fprintf(stderr, "IHDR chunk has incorrect length %u\n", length);
                if (data) free(data);
                fclose(fp);
                exit(4);
            }
            uint32_t width = (data[0] << 24) | (data[1] << 16) | (data[2] << 8) | data[3];
            uint32_t height = (data[4] << 24) | (data[5] << 16) | (data[6] << 8) | data[7];
            printf("\twidth %u\n\theight %u\n", width, height);
        } else if (strncmp(type, "IEND", 4) == 0) {
            exist_IEND = 1;
            if (fgetc(fp) != EOF) {
                fprintf(stderr, "There is data exists after IEND chunk\n");
                if (data) free(data);
                fclose(fp);
                exit(4);
            }
            if (data) free(data);
            break;
        }

        if (data) free(data);
        chunk_count++;
        if (feof(fp)) { break; }
    }

    fclose(fp);

    if (!exist_IHDR) {
        fprintf(stderr, "IHDR chunk is missing\n");
        exit(4);
    }
    if (!exist_IEND) {
        fprintf(stderr, "IEND chunk is missing\n");
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
