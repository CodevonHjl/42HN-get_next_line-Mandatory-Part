# 📜 42HN – get_next_line (Mandatory Part)

[![Language](https://img.shields.io/badge/Language-C-informational?logo=c&logoColor=white&color=00599C)](https://en.wikipedia.org/wiki/C_(programming_language))
[![Platform](https://img.shields.io/badge/Platform-Linux-success?logo=linux&logoColor=white)](https://www.kernel.org/)
[![School](https://img.shields.io/badge/42-Heilbronn-000000?logo=42&logoColor=white)](https://www.42heilbronn.de/)
[![Status](https://img.shields.io/badge/Project-get__next__line_(mandatory_only)-blueviolet)](#)

This repository contains my implementation of **`get_next_line`**, a function that reads a file descriptor line by line for the 42 curriculum.  
Only the **mandatory part** is implemented (no bonus / no multi-FD support).

---

## 🗂 What’s inside

The core of the project is the function `char *get_next_line(int fd);`, which provides a simple line-by-line interface on top of the low-level `read()` system call.

The function:

- Works on a **single file descriptor** at a time  
- Returns the **next line** (including the trailing `'\n'` if present)  
- Returns `NULL` on end-of-file or error  
- Uses only the allowed system calls: `read`, `malloc`, `free`  

Typical files in this repo:

- `get_next_line.c` – main logic (reading & line extraction)  
- `get_next_line_utils.c` – helper functions (string / buffer utils)  
- `get_next_line.h` – prototypes & `BUFFER_SIZE` definition  

---

## 🧠 What I learned from this project

Compared to the previous 42 C projects, the main new ideas in **`get_next_line`** were:

- 📚 **Static variables & persistent state**  
  - Using a `static` buffer to keep leftover data between calls.  
  - For beginners, it’s very helpful to write a small `main.c` that opens a file and calls `get_next_line` in a loop, so you can *see* how the static variable “remembers” what was read last time.

- 📦 **Buffered I/O around `read()`**  
  - Instead of reading the whole file at once, you read in chunks of size `BUFFER_SIZE` and assemble complete lines from those chunks.  
  - You have to handle edge cases such as:
    - Lines longer than `BUFFER_SIZE`  
    - Files that do not end with `'\n'`  
    - Empty files and read errors  

- 🧼 **Manual memory management**  
  - Repeatedly allocating, joining, trimming and freeing strings.  
  - Ensuring that every allocated buffer is either reused correctly or freed, including on error paths.

- 🔌 **File descriptors & system-call prototypes**  

  This project relies heavily on these functions (from `unistd.h` and `fcntl.h`):

  ```c
  ssize_t read(int fd, void *buf, size_t count);
  int     open(const char *pathname, int flags, ...);
  int     close(int fd);
  	•	The fd used by read usually comes from open() (files, pipes, sockets, …).
	•	write typically uses the standard file descriptors:
	•	0 – standard input (stdin)
	•	1 – standard output (stdout)
	•	2 – standard error (stderr)

When you write your own main to test get_next_line, you usually:
	•	Use open() to get a file descriptor to read from, and
	•	Use write or printf to print to stdout.

This makes it very clear that the FD you read from and the FD you write to are often different things.
