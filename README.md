<h1 align="center">📄 get_next_line</h1>

<p align="center">
  <img src="https://img.shields.io/badge/language-C-blue" alt="Language: C">
  <img src="https://img.shields.io/badge/norminette-passing-brightgreen" alt="Norminette">
  <img src="https://img.shields.io/badge/42-Common%20Core-black" alt="42 Common Core">
</p>

<p align="center">
  A function that reads a file descriptor one line at a time.
</p>

---

## 📖 Description

**get_next_line** is a 42 Common Core project whose goal is to write a function that returns the next line read from a file descriptor, each time it is called. It introduces **static variables** in C and the handling of reads of arbitrary size with `read`.

This repository contains the **mandatory part only**: a single file descriptor is handled at a time.

### Prototype

```c
char	*get_next_line(int fd);
```

| Return value       | Meaning                                                    |
|--------------------|------------------------------------------------------------|
| A line             | The line read, including the terminating `\n` if present  |
| `NULL`             | Nothing left to read, or an error occurred                 |

> [!NOTE]
> The returned line is allocated with `malloc`: the caller is responsible for freeing it.

## 🛠️ Instructions

### Compilation

There is no Makefile: compile the sources together with your own `main`, and define the buffer size with the `-D` flag:

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl
```

If `BUFFER_SIZE` is not defined at compile time, a default value is set in `get_next_line.h`.

> [!TIP]
> Test with very different buffer sizes (`1`, `42`, `10000000`) to make sure the function behaves correctly in every case.

### Usage

```c
#include <fcntl.h>
#include <stdio.h>
#include "get_next_line.h"

int	main(void)
{
	int		fd;
	char	*line;

	fd = open("file.txt", O_RDONLY);
	if (fd < 0)
		return (1);
	line = get_next_line(fd);
	while (line)
	{
		printf("%s", line);
		free(line);
		line = get_next_line(fd);
	}
	close(fd);
	return (0);
}
```

It also works on standard input:

```c
line = get_next_line(0);
```

## ⚙️ How it works

1. A **static variable** keeps whatever was read beyond the last returned line, so it is not lost between calls.
2. `read` is called with `BUFFER_SIZE` bytes, and the result is appended to the stash until it contains a `\n` or the end of the file is reached.
3. The line (up to and including the `\n`) is extracted and returned.
4. The rest of the stash is kept for the next call.
5. At the end of the file, or on error, the stash is freed and `NULL` is returned.

### Constraints

- `lseek` and global variables are forbidden.
- The libft cannot be used: helper functions live in `get_next_line_utils.c`.
- The file is read as little as possible: reading stops as soon as a `\n` is found.
- No memory leaks, including when an error occurs.

## 📚 Resources

- `man 2 read` and `man 2 open`
- [cppreference — Storage duration (static)](https://en.cppreference.com/w/c/language/storage_duration)
- [Valgrind quick start](https://valgrind.org/docs/manual/quick-start.html) to check for leaks

### Use of AI

AI was used to help structure and format this README. All code was written by hand, without AI assistance.
