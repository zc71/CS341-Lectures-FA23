# Welcome to Homework 0!

For these questions you'll need the mini course and  "Linux-In-TheBrowser" virtual machine (yes it really does run in a web page on your machine using javascript!) at -

https://cs-education.github.io/sys/

Let's take a look at some C code (with apologies to a well known song)-
```C
// An array to hold the following bytes. "q" will hold the address of where those bytes are.
// The [] mean set aside some space and copy these bytes into teh array array
char q[] = "Do you wanna build a C99 program?";

// This will be fun if our code has the word 'or' in later...
#define or "go debugging with gdb?"

// sizeof is not the same as strlen. You need to know how to use these correctly, including why you probably want strlen+1

static unsigned int i = sizeof(or) != strlen(or);

// Reading backwards, ptr is a pointer to a character. (It holds the address of the first byte of that string constant)
char* ptr = "lathe"; 

// Print something out
size_t come = fprintf(stdout,"%s door", ptr+2);

// Challenge: Why is the value of away equal to 1?
int away = ! (int) * "";


// Some system programming - ask for some virtual memory

int* shared = mmap(NULL, sizeof(int*), PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
munmap(shared,sizeof(int*));

// Now clone our process and run other programs
if(!fork()) { execlp("man","man","-3","ftell", (char*)0); perror("failed"); }
if(!fork()) { execlp("make","make", "snowman", (char*)0); execlp("make","make", (char*)0)); }

// Let's get out of it?
exit(0);
```

## So you want to master System Programming? And get a better grade than B?
```C
int main(int argc, char** argv) {
	puts("Great! We have plenty of useful resources for you, but it's up to you to");
	puts(" be an active learner and learn how to solve problems and debug code.");
	puts("Bring your near-completed answers the problems below");
	puts(" to the first lab to show that you've been working on this.");
	printf("A few \"don't knows\" or \"unsure\" is fine for lab 1.\n"); 
	puts("Warning: you and your peers will work hard in this class.");
	puts("This is not CS225; you will be pushed much harder to");
	puts(" work things out on your own.");
	fprintf(stdout,"This homework is a stepping stone to all future assignments.\n");
	char p[] = "So, you will want to clear up any confusions or misconceptions.\n";
	write(1, p, strlen(p) );
	char buffer[1024];
	sprintf(buffer,"For grading purposes, this homework 0 will be graded as part of your lab %d work.\n", 1);
	write(1, buffer, strlen(buffer));
	printf("Press Return to continue\n");
	read(0, buffer, sizeof(buffer));
	return 0;
}
```
## Watch the videos and write up your answers to the following questions. For now just save them in a text document - we'll explain how to hand them later. 
They will be due in the second week of class.

**Important!**

The virtual machine-in-your-browser and the videos you need for HW0 are here:

https://cs-education.github.io/sys/

The coursebook:

https://cs341.cs.illinois.edu/coursebook/index.html

Questions? Comments? Use Ed: (you'll need to accept the sign up link I sent you)
https://edstem.org/

The in-browser virtual machine runs entirely in Javascript and is fastest in Chrome. Note the VM and any code you write is reset when you reload the page, *so copy your code to a separate document.* The post-video challenges (e.g. Haiku poem) are not part of homework 0 but you learn the most by doing (rather than just passively watching) - so we suggest you have some fun with each end-of-video challenge.

HW0 questions are below. Copy your answers into a text document (which the course staff will grade later) because you'll need to submit them later in the course. More information will be in the first lab.

## Chapter 1

In which our intrepid hero battles standard out, standard error, file descriptors and writing to files.

### Hello, World! (system call style)
1. Write a program that uses `write()` to print out "Hi! My name is `<Your Name>`".
#include <unistd.h>

int main(){	
	write(1,"Hi! My name is Leo\n",19);
	return 0;
}

### Hello, Standard Error Stream!
2. Write a function to print out a triangle of height `n` to standard error.
   - Your function should have the signature `void write_triangle(int n)` and should use `write()`.
   - The triangle should look like this, for n = 3:
   ```C
   *
   **
   ***
   ```
#include <unistd.h>
void write_triangle(int n){
	int a = 1;
	while(a <= n){
		for (int i = 0; i < a; i++){
			write(1,"*",1);
		}
		write(1,"\n",1);
		a++;
	}
}

int main(){	
	write_triangle(5);
	return 0;
}

### Writing to files
3. Take your program from "Hello, World!" modify it write to a file called `hello_world.txt`.
   - Make sure to to use correct flags and a correct mode for `open()` (`man 2 open` is your friend).
#include <fcntl.h> 
#include <unistd.h>
#include <sys/stat.h> 

int main() {
    int fd = open("hello_world.txt");
    write(fd, "Hello, World!\n", 13);
    close(fd);
    return 0;
}

### Not everything is a system call
4. Take your program from "Writing to files" and replace `write()` with `printf()`.
   - Make sure to print to the file instead of standard out!
#include <fcntl.h> 
#include <unistd.h>
#include <sys/stat.h> 

int main() {

    FILE *fd = fopen("hello_world.txt", "w");
    fprintf(fd, "hihihi\n");
    fclose(fd);
    return 0;
}

5. What are some differences between `write()` and `printf()`?
write(): A low-level, unbuffered system call that writes data directly to a file or output device. It is defined in the unistd.h header file, typically used in UNIX/Linux systems.
printf(): A high-level, buffered function that is part of the standard input/output library, stdio.h. It provides formatted output to the standard output stream, usually the screen.

## Chapter 2

Sizing up C types and their limits, `int` and `char` arrays, and incrementing pointers

### Not all bytes are 8 bits?
1. How many bits are there in a byte?
It is often to be at least 8 bits and in C it may over 8.
2. How many bytes are there in a `char`?
Char is 8 bits   
3. How many bytes the following are on your machine?
   - `int`, `double`, `float`, `long`, and `long long`
Int:4
Double:8
Float:4
Long:4
long long:8

### Follow the int pointer
4. On a machine with 8 byte integers:
```C
int main(){
    int data[8];
} 
```
If the address of data is `0x7fbd9d40`, then what is the address of `data+2`?

int main() {
    int data[8];
    printf("%p\n",(void*)&data);
    printf("%p",(void*)&data+2);
    return 0;
}
00000000005ffe80
00000000005ffe82

so the answer is 0x7fbd9d42

5. What is `data[3]` equivalent to in C?
   - Hint: what does C convert `data[3]` to before dereferencing the address?
data [3] equals to data+3
### `sizeof` character arrays, incrementing pointers
  
Remember, the type of a string constant `"abc"` is an array.

6. Why does this segfault?
```C
char *ptr = "hello";
*ptr = 'J';
```
The content that p points to is read only.
7. What does `sizeof("Hello\0World")` return? 12
8. What does `strlen("Hello\0World")` return? 5
9. Give an example of X such that `sizeof(X)` is 3. str x = "xx"
10. Give an example of Y such that `sizeof(Y)` might be 4 or 8 depending on the machine. Long y;

## Chapter 3

Program arguments, environment variables, and working with character arrays (strings)

### Program arguments, `argc`, `argv`
1. What are two ways to find the length of `argv`?
2. What does `argv[0]` represent? 
### Environment Variables
3. Where are the pointers to environment variables stored (on the stack, the heap, somewhere else)?
### String searching (strings are just char arrays)
4. On a machine where pointers are 8 bytes, and with the following code:
```C
char *ptr = "Hello";
char array[] = "Hello";
```
What are the values of `sizeof(ptr)` and `sizeof(array)`? Why?

### Lifetime of automatic variables

5. What data structure manages the lifetime of automatic variables?

## Chapter 4

Heap and stack memory, and working with structs

### Memory allocation using `malloc`, the heap, and time
1. If I want to use data after the lifetime of the function it was created in ends, where should I put it? How do I put it there? Global Variables
2. What are the differences between heap and stack memory?
Stack: smaller, fixed size, automatically allocated, will be released automatically when the function exits, fitting for short-lived objects.
Heap: bigger, flexible size, manually allocated and deallocated(“free”), fit for long-live or shared objects.
3. Are there other kinds of memory in a process?
Code Memory (Text Segment)
Data Memory (Data Segment)
4. Fill in the blank: "In a good C program, for every malloc, there is a ___". free
### Heap allocation gotchas
5. What is one reason `malloc` can fail? When there is no enough memory
6. What are some differences between `time()` and `ctime()`?
time() is used to get the current calendar time, It returns the current time since the Epoch (00:00:00 UTC, January 1, 1970) measured in seconds, represented as a time_t value.
ctime() is used to convert a time_t value to a string representing the local time. It returns a string representing the readable version of the time, in the format: "Day Mon DD HH:MM:SS YYYY\n".   
7. What is wrong with this code snippet?
```C
free(ptr);
free(ptr);
```
Free a heap twice.
8. What is wrong with this code snippet?
```C
free(ptr);
printf("%s\n", ptr);
```
Still use the heap after free it.
9. How can one avoid the previous two mistakes? 
Do not use a pointer after it has been freed, or free it after you are done using it. If needed, you can also set the pointer to NULL to avoid inadvertently using it
### `struct`, `typedef`s, and a linked list
10. Create a `struct` that represents a `Person`. Then make a `typedef`, so that `struct Person` can be replaced with a single word. A person should contain the following information: their name (a string), their age (an integer), and a list of their friends (stored as a pointer to an array of pointers to `Person`s).
11. Now, make two persons on the heap, "Agent Smith" and "Sonny Moore", who are 128 and 256 years old respectively and are friends with each other.
#include <stdio.h>

int main() {

    struct Person {
    char *name;
    int age;
    struct Person **friends;
};

typedef struct Person Person;
    Person smith;
    Person moore;
    smith.name = "Agent Smith";
    smith.age = 128;
    moore.name = "Sonny Moore";
    moore.age = 256;
    smith.friends = (Person **)malloc(sizeof(Person *));
    moore.friends = (Person **)malloc(sizeof(Person *));
    smith.friends[0] = &moore;
    moore.friends[0] = &smith;
    printf("Name: %s, Age: %d\n", smith.name, smith.age);
    printf("Smith's friend: %s\n", smith.friends[0]->name);
    printf("Name: %s, Age: %d\n", moore.name, moore.age);
    printf("Smith's friend: %s\n", moore.friends[0]->name);
    free(smith.friends);
    free(moore.friends);
    return 0;
}

### Duplicating strings, memory allocation and deallocation of structures
Create functions to create and destroy a Person (Person's and their names should live on the heap).
12. `create()` should take a name and age. The name should be copied onto the heap. Use malloc to reserve sufficient memory for everyone having up to ten friends. Be sure initialize all fields (why?).
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Person {
    char *name;
    int age;
    struct Person **friends;
} Person;

Person *create(const char *name, int age) {
    Person *new_person = (Person *)malloc(sizeof(Person));
    if (!new_person) return NULL;

    new_person->name = strdup(name);
    if (!new_person->name) {
        free(new_person);
        return NULL;
    }

    new_person->age = age;
    new_person->friends = (Person **)malloc(10 * sizeof(Person *));
    if (!new_person->friends) {
        free(new_person->name);
        free(new_person);
        return NULL;
    }

    for (int i = 0; i < 10; i++)
        new_person->friends[i] = NULL;
    
    return new_person;
}

int main() {
    Person *person = create("John", 30);
    if (person) {
        printf("%d", person->age); 
    } else {
        printf("Person creation failed.\n");
    }
    return 0;
}

13. `destroy()` should free up not only the memory of the person struct, but also free all of its attributes that are stored on the heap. Destroying one person should not destroy any others.
void destroy(Person *person) {
    if (person) {
        if (person->name) {
            free(person->name);
        }
        if (person->friends) {
            free(person->friends);
        }
        free(person);
    }
}

## Chapter 5 

Text input and output and parsing using `getchar`, `gets`, and `getline`.

### Reading characters, trouble with gets
1. What functions can be used for getting characters from `stdin` and writing them to `stdout`?
Getchar():
Fgets():
Scanf():

2. Name one issue with `gets()`. buffer overflow vulnerabilities
### Introducing `sscanf` and friends
3. Write code that parses the string "Hello 5 World" and initializes 3 variables to "Hello", 5, and "World".
#include <stdio.h>

int main() {
    char str[] = "Hello 5 World";
    
    char str1[10], str2[10];
    int num;

    sscanf(str, "%s %d %s", str1, &num, str2);

    printf("str1: %s\n", str1); 
    printf("num: %d\n", num);   
    printf("str2: %s\n", str2); 
    
    return 0;
}
### `getline` is useful
4. What does one need to define before including `getline()`? <stdio.h>
5. Write a C program to print out the content of a file line-by-line using `getline()`.
#include <stdio.h>
#include <stdlib.h>

int main() {
    FILE *file;
    char *line = NULL;
    size_t len = 0;
    ssize_t read;

    file = fopen("filename.txt", "r");
    if (file == NULL) {
        perror("Error opening file");
        return 1;
    }

    while ((read = getline(&line, &len, file)) != -1) {
        printf("%s", line);
    }
    fclose(file);
    free(line);

    return 0;
}

## C Development

These are general tips for compiling and developing using a compiler and git. Some web searches will be useful here

1. What compiler flag is used to generate a debug build? -g
2. You modify the Makefile to generate debug builds and type `make` again. Explain why this is insufficient to generate a new build.
3. Are tabs or spaces used to indent the commands after the rule in a Makefile?
4. What does `git commit` do? What's a `sha` in the context of git?
5. What does `git log` show you?
6. What does `git status` tell you and how would the contents of `.gitignore` change its output?
7. What does `git push` do? Why is it not just sufficient to commit with `git commit -m 'fixed all bugs' `?
8. What does a non-fast-forward error `git push` reject mean? What is the most common way of dealing with this?

## Optional (Just for fun)
- Convert your a song lyrics into System Programming and C code and share on Ed.
- Find, in your opinion, the best and worst C code on the web and post the link to Ed.
- Write a short C program with a deliberate subtle C bug and post it on Ed to see if others can spot your bug.
- Do you have any cool/disastrous system programming bugs you've heard about? Feel free to share with your peers and the course staff on Ed.
