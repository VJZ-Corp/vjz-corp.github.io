---
title: How to Debug Memory in Visual Studio 2019
layout: default
filename: vs2019-debug.md
---

# How to Debug Memory in Visual Studio 2019

Microsoft’s Visual Studio has undoubtedly changed development for C/C++, C#, and other .NET languages. It introduces many innovative debugging features that previous IDEs do not support. One particular highlight of VS2019 debugging is its ability to modify a program’s memory directly.

---

You can modify your program’s memory by following this guide. First, begin by creating a new project in Visual Studio. In this example, we are going to generate a C++ solution. *Keep in mind that these steps also work for C# solutions.* Once you load your project, add the following lines of code:

```cpp
#include <iostream>

int main() 
{
    int num = 4;
    int* ptr = &num;
    std::cout << *ptr;
    return 0;
}
```

This will be our example code to debug. A quick review of what this code does. First, it will include the iostream library:

```cpp
#include <iostream>
```

Next, within the main function, we first initialize the variable `num` to 4. Then we create a pointer `ptr` and set that equal to the memory address of `num`:

```cpp
int num = 4;
int* ptr = &num;
```

Finally, we return the value of `*ptr` by printing it to the console:

```cpp
std::cout << *ptr;
return 0;
```

Now that you know how this C++ program works, it is time to debug the program. First, add a breakpoint on line 6. Then, make sure that you are building this solution in *Debug Mode and NOT Release Mode*. This is a critical step as building your solution in Release Mode will omit many debugging tools that comes with Debug Mode. Now run the program by pressing **Ctrl + F5**. You should see a bunch of things pop up. Depending on how you setup your IDE, the diagnostic tools should be on the right hand side, your watches and call stack should be on the bottom left and your output should be on the bottom right.

![image](https://user-images.githubusercontent.com/73851560/185765984-dc477813-6264-440a-8ea5-2c40d9c75afc.png)

Once you see this window, go to Debug → Windows → Memory → Memory 1, to open the memory view for this process.

![image](https://user-images.githubusercontent.com/73851560/185765998-564c1358-523b-47da-a669-12e5aff600f5.png)

We are now very close to viewing our program’s memory. After you have selected “Memory 1”, you might see something like this:

![image](https://user-images.githubusercontent.com/73851560/185766003-43831bc8-e06b-4f14-a19c-94c65f600492.png)

[1] is the physical memory view of your computer, the reason why it has so many question marks (?) is because that portion of your computer’s memory cannot be accessed by Visual Studio. If you want to view your program’s memory, copy the value at [2] and paste it into the memory address bar (right above [1]).

![image](https://user-images.githubusercontent.com/73851560/185766009-2cb6f100-3a18-4ccc-b892-314c9da7587a.png)

[3] shows the physical location of variable num once you input the address of num (stored in pointer at [4]). This demonstrates the use of pointers and allows us to see what is going on behind the scenes. You can see at [3], the memory address and the “Locals” tab have the same value, and is also stored in num at line 5, which is 4. That is wonderful! We now can read memory but how about writing to memory? Can we modify the memory? Of course! Right click on the byte of data (the 2 hex digits) and you can change it to whatever you want, I am going to change mine to `0x23`.

![image](https://user-images.githubusercontent.com/73851560/185766018-e87efb2f-ab1a-42a1-903c-e96aa6ae78f9.png)

These values are stored in hexadecimal (base 16) which means that `0x23` = 35 in base 10 (decimal).

![image](https://user-images.githubusercontent.com/73851560/185766037-b748e025-ee3a-4132-a60b-c66cdb99f621.png)

[5] confirms that the change in memory has been made (noting the red highlight) and you can see that in the “Locals” section, the value has been sucessfully changed to 35 (`0x23`).

![image](https://user-images.githubusercontent.com/73851560/185766041-558dba9b-ad49-446b-8c33-f5da65d83ac6.png)

Finally, if you press the “Continue” button, the program will run normally and now you can see that the window displays 35 instead of 4. Congratulations! You have just edited your program’s memory and successfully changed the output of a basic program. This is one of the many tools Visual Studio 2019 has to offer when it comes to debugging.
