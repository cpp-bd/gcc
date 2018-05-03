## Static Linking
* Linking a demo program with ` hiredis ` (Redis C Client)
```sh
gcc -static example.c -I/$PWD -L/$PWD -lhiredis
```
