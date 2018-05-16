## Say a project having ` include ` ` src ` folder
```sh
gcc src/*.c -I/$PWD/include -o vice

gcc src/*.c -I./include -o vice

gcc ./src/*.c -I./include -o vice
```
