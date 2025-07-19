# The Lempel-Ziv Algorithm
The Lempel-Ziv Algorithm is a lossless data compression technique that forms the basis for many widely used compression formats, like ZIP, gzip, and PNG. Developed by Abraham Lempel and Jacob Ziv in the late 1970s, it works by identifying and replacing repeated patterns in data with shorter references, reducing the overall size of the data without losing any information.

## How It Works
The LZ77 File Compressor operates using the sliding window technique of the LZ77 algorithm:
1. **Input Processing**: The program reads the input file into a sliding window, split into a search buffer (past data) and a lookahead buffer (upcoming data).
2. **Pattern Matching**: It scans the search buffer for the longest substring that matches the start of the lookahead buffer.
3. **Encoding**:
   - If a match is found, it outputs a triple `(offset, length, next_char)`, where `offset` is the distance to the match, `length` is the number of matching characters, and `next_char` is the character following the match.
   - If no match is found, it outputs `(0, 0, current_char)` for the current character.
4. **Output**: The sequence of triples is written to a compressed file, optionally with additional encoding (e.g., bit-packing) to optimize size.
5. **Decompression**: The program reverses the process, reconstructing the original file by resolving references and copying literal characters.

The tool supports both compression (`compress <input> <output>`) and decompression (`decompress <input> <output>`) modes.

## When It's Useful
- **Text Files**: Excels at compressing text with repetitive patterns, such as logs, source code, or configuration files.
- **Archival**: Ideal for reducing storage needs for backups or transferring files over networks.
- **Embedded Systems**: Suitable for environments with limited memory, as LZ77 is lightweight and fast.
- **Pre-processing**: Useful as a first stage in pipelines where further compression (e.g., with Huffman coding) is applied, like in ZIP or gzip.

## Advantages
- **Lossless**: Guarantees exact reconstruction of the original file with no data loss.
- **Fast**: Offers quick compression and decompression, making it suitable for real-time applications.
- **Adaptive**: Automatically adjusts to data patterns without requiring pre-defined dictionaries.
- **Simple Implementation**: Easy to integrate into other systems due to its straightforward algorithm.
- **Low Memory Footprint**: Operates with a fixed-size sliding window, efficient for resource-constrained environments.

## Limitations
- **Compression Ratio**: Less effective for files with little repetition (e.g., already compressed files like JPEGs or MP4s), as it relies on redundant patterns.
- **Window Size Trade-off**: Smaller windows reduce memory use but may miss longer repetitions, lowering compression efficiency. Larger windows increase memory and computation needs.
- **Single-Pass**: Only examines local patterns within the sliding window, potentially missing global redundancies compared to algorithms like LZ78 or LZW.
- **No Entropy Coding**: Without additional encoding (e.g., Huffman), compression may not be optimal for some data types.
- **Not Ideal for Streaming**: The fixed window limits its ability to handle continuous data streams compared to modern algorithms like Brotli.

## Compile

### Linux

Use `make` command in the project's root directory to build the project.
```
$ make
```

### Windows

Run the following command in the project's root directory to build the project from the source.
```
gcc ./src/*.c main.c -Wall -g -o ./bin/lz7
```

## Usage
Use the following flags:
- `-c`: compress file
- `-d`: decompress file
- `-o`: output file path
- `-w`: sliding window (dictionary) size (default: 16 kb)
- `-b`: compressed buffer (reader/writer) size (default: 2048 bytes)
- `-B`: decompressed buffer (chunk reader) size (default: 4096 bytes)

Example:
- `./lz7 -c c:/picture.bmp -o c:/picture.bmp.lz7`
- `./lz7 -d ./picture.bmp.lz7`

Note: When you don't specify an output when using the `-d` flag to decompress a file, if the file extention is not `.lz7`, it will decompress and **OVERWRITE** the original file.

## Test

For testing the program, I have written a test in c, which looks for every file in `test_files` directory and does a compression, decompression and comparison process for each file then prints the result. In order to test this, create `test_files` directory and put some files (i.e bitmap image file) in it, then compile `test.c`. You can use `make test` command if you are on linux.

```
--------------------------|TEST 01|--------------------------
[TEST 1/3]: Compressing pic-1024.bmp
Finished processing (0.419653 s): 3145782 bytes -> 199602 bytes (-93.65%)

        --->> Compression completed!

[TEST 2/3]: Decompressing pic-1024.bmp.lz7
Finished Processing (0.132252 s): 199602 bytes -> 3145782 bytes.

        --->> Decompression completed!

[TEST 3/3]: Verifying pic-1024.bmp
--- [PASSED] - Decompressed file matches original

--------------------------|TEST 02|--------------------------
[TEST 1/3]: Compressing pic-256.bmp
Finished processing (0.012253 s): 196662 bytes -> 13812 bytes (-92.98%)

        --->> Compression completed!

[TEST 2/3]: Decompressing pic-256.bmp.lz7
Finished Processing (0.009301 s): 13812 bytes -> 196662 bytes.

        --->> Decompression completed!

[TEST 3/3]: Verifying pic-256.bmp
--- [PASSED] - Decompressed file matches original

--------------------------|TEST 03|--------------------------
[TEST 1/3]: Compressing pic-64.bmp
Finished processing (0.001368 s): 12342 bytes -> 4128 bytes (-66.55%)

        --->> Compression completed!

[TEST 2/3]: Decompressing pic-64.bmp.lz7
Finished Processing (0.000657 s): 4128 bytes -> 12342 bytes.

        --->> Decompression completed!

[TEST 3/3]: Verifying pic-64.bmp
--- [PASSED] - Decompressed file matches original

-------------------------------------------------------------
Testing complete.
```

## TODO
- [x] feature: CLI
- [x] Improve performance - using hash table
- [ ] feature: Multi-Threading
