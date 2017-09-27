Here is some pseudo-code about the filesystem.
The input and output blocks have to be in a format that can be easily read by smart contracts.

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

int getStringLength(int ptr) {
  int res = 0;
  while (input(ptr) != 0) res++;
  return res;
}

char *getString(int ptr, int sz) {
  char *res = malloc(sz+1);
  for (int i = 0; i < sz; i++) res[i] = input(ptr+sz);
  res[sz] = 0;
  return res;
}

void initSystem() {
  sys = malloc(sizeof(system));
  sys.nextfd = 3; // 0:stdin, 1:stdout, 2:stderr
  // Actually we should here have a list of file names?
  // Read input byte by byte, it includes file names and data
  int loc = 0;
  int index = 0;
  int nextLength = getStringLength(loc);
  while (nextLength > 0) {
     char *str = getString(loc, nextLength);
     sys->file_name[index] = str;
     loc += nextLength;
     int sz = input(loc) + input(loc+1)<<8 + input(loc+2)<<16 + input(loc+3)<<24;
     sys->file_size[index] = sz;
     loc += 4;
     // Is it a good idea to read everything into memory?
     char *data = getString(loc, sz);
     sys->file_data[index] = data;
     loc += sz;
     index++;
     nextLength = getStringLength(loc);
  }
}
```

`___syscall5` is for opening files.

```
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

