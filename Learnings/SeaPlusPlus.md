**Executing Programs with Command Line**
``` bash
g++ -o helloworld helloworld.cpp #Compile and Link
./helloworld #Execute
g++ -std=c++20 -o helloworld helloworld.cpp #Use specific compiler

g++ -fdiagnostics-color=always -g -Wall -Weffc++ -Wextra -Wconversion -Wsign-conversion -pedantic-errors -std=c++20 helloworld.cpp -o helloworld
```

**Build vs Release Configurations**
![[Pasted image 20260821141546.png]]

**For every C++ project**
Set `"-pedantic-errors",` in `tasks.json`
- This makes sure that compiler extensions don't effect standard C++ practice
```c++
"-pedantic-errors",
"${file}",
```

File > Preferences > Settings > Type in search bar "insert final newline" 
- Check `Files: Insert Final Newline` for both `User` and `Workspace`
![[Pasted image 20260821142230.png]]

Adds additional diagnostic warnings
``` C++
"-Wall",
"-Weffc++",
"-Wextra",
"-Wconversion",
"-Wsign-conversion",
"${file}",
```

Bypass windows security for project
