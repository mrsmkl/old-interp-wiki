Here is some pseudo-code about the filesystem.
The input and output blocks have to be in a format that can be easily read by smart contracts.
In the following, I assume that there are the following merkle roots:
* `names`: file names. There is a fixed maximum file name length, for example 1024
* `sizes`: sizes of the files
* `data`: data blocks with given size

The names and data require two level merkle proofs. There are three instructions for inputting data:
* `inputName(n, i)` returns the `i`th character in the name of the `n`th file.
* `inputSize(n)` returns the size of the `n`th file.
* `inputData(n, i)` returns the `i`th byte of the `n`th file.

We need the following global structure to manage the filesystem:
```c
struct system {
  int next_fd;
  int ptr[1024]; // Pointers to the data blocks for each fd
  int pos[1024]; // Location inside the block
  char *file_name[1024];
  char *file_data[1024];
  int file_length[1024];
};

// Global variable that will store our system
system *sys;
```

Initializing the global structure:
```c
int getNameLength(int ptr) {
  int res = 0;
  while (inputName(ptr, res) != 0) res++;
  return res;
}

char *getName(int ptr) {
  int sz = getNameLength(ptr);
  char *res = malloc(sz+1);
  for (int i = 0; i < sz; i++) res[i] = inputName(ptr, i);
  res[sz] = 0;
  return res;
}

char *getData(int ptr) {
  int sz = inputSize(ptr);
  char *res = malloc(sz+1);
  for (int i = 0; i < sz; i++) res[i] = inputData(ptr+sz);
  return res;
}

void initSystem() {
  sys = malloc(sizeof(system));
  sys.nextfd = 3; // 0:stdin, 1:stdout, 2:stderr
  // Actually we should here have a list of file names?
  // Read input byte by byte, it includes file names and data
  int loc = 0;
  int index = 0;
  int nextLength = getNameLength(index);
  while (nextLength > 0) {
     sys->file_name[index] = getName(index);
     sys->file_size[index] = inputSize(index);
     sys->file_data[index] = getData(index);
     index++;
     nextLength = getNameLength(index);
  }
}
```

`___syscall5` is for opening files.

```c
int ___syscall5(int which, int *varargs) {
  char *name = varargs[0];
  int flags = varargs[1];
  int mode = varargs[2];
  // No empty names allowed
  if (!name || !name[0]) return -1;
  int index = 0;
  while (sys->file_name[index]) {
      if (strcmp(sys->file_name[index], name)) {
              int fd = sys->next_fd;
              sys->ptr[fd] = index;
              sys->loc[fd] = 0;
              sys->next_fd++;
              return fd;
      }
      index++;
  }
  // No such file
  return -1;
}
```

Need to think about creating new files.

