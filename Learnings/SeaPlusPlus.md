**Executing Programs with Command Line**
``` bash
g++ -o helloworld helloworld.cpp #Compile and Link
./helloworld #Execute
```

**Build vs Release Configurations**
![[Pasted image 20260821141546.png]]

**For every C++ project**
Set `"-pedantic-errors",` in `tasks.json`
```c++
"-pedantic-errors",
"${file}",
```