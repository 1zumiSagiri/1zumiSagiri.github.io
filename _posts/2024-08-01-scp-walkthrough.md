---
layout: post
title: Moving files to remote server using SCP
date: 2024-08-01 15:00:00
description: this is what included pseudo code could look like
tabs: true
---

Feel free to come to office hours if you're new to using command line!

## 0 - Make sure you connect to a UB network

CSE department server `emon.cse.buffalo.edu` is accessible only from the UB campus. Go to next section if you are on campus or living in dorms.

If you are off campus, you will need to use the [UB VPN](https://www.buffalo.edu/ubit/service-guides/connecting/vpn/computer.html).

## 1 - Open a terminal

For Linux/Mac users, use the `Terminal.app` or any other terminal emulator you have.

For Windows users, you may want to use [PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/windows-powershell/starting-windows-powershell). To set up `PowerShell` to use `scp`, you need to install `OpenSSH`, see this [tutorial](https://learn.microsoft.com/en-us/windows/terminal/tutorials/ssh) for details. To connect to the CSE server, you only need to install the `OpenSSH client` feature.

## 2 - Locate the directory where your template files are

Once you have downloaded the template files, use command `cd` to navigate to the parent directory of the folder where the template codes are located. For example, if the files are located in the directory `HW0C++`, it would look like this:

```Markdown
vincent@local_machine ~ % cd Downloads/331
vincent@local_machine 331 % ls
HW0C++
vincent@local_machine 331 % ls HW0C++
Driver.cpp  HW0Utility.h  outputs  Solution.cpp  testcases  Utility.h
vincent@local_machine 331 %
```

Check out the [link](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Command_line#navigation_on_the_command_line) if you are not familiar with basic commands.

## 3 - Transfer files to the remote server

To transfer files to the remote server, use command `scp`. For example, my UBIT name is `vc331` and I want to transfer the folder `HW0C++` to the remote server. The command would look like this:

```Markdown
vincent@local_machine 331 % scp -r HW0C++ vc331@emon.cse.buffalo.edu:~
vc331@emon.cse.buffalo.edu's password:
Solution.cpp                   100%  884    37.6KB/s   00:00
input5.txt                     100%   25KB 555.2KB/s   00:00
input1.txt                     100%  250    10.9KB/s   00:00
input2.txt                     100%  501    20.0KB/s   00:00
Utility.h                      100%  159     6.7KB/s   00:00
Driver.cpp                     100%  653    30.5KB/s   00:00
output5.txt                    100%   36KB 504.6KB/s   00:00
output1.txt                    100%  318    13.1KB/s   00:00
output2.txt                    100%  728    31.0KB/s   00:00
HW0Utility.h                   100%  647    29.3KB/s   00:00
vincent@local_machine 331 %
```

Terminal will prompt you to enter your password, you won't see the password as you type it.

## 4 - SSH into the remote server

Now you can login to the server using command `ssh`. It is better to keep your current terminal tab open for file transfer and open an additional tab for `ssh` and test your implementation on the `emon` server. For example, my UBIT name is `vc331`, the command would look like this:

```Markdown
vincent@local_machine 331 % ssh vc331@emon.cse.buffalo.edu
vc331@emon.cse.buffalo.edu's password:
vc331@emon:~$
```

To check if the files have been successfully transferred, use command `ls` to list the folders and files in the current directory. You should see the folder `HW0C++` in the list:

```Markdown
vc331@emon:~$ ls
HW0C++ HW0Java HW0Python
vc331@emon:~$
```

## 5 - Compile and run your code

To run your code, navigate to the directory where your code is located and compile it. To compile and run your code on `input1.txt`, you can use the following commands, depending on the language you are using:

{% tabs lang %}

{% tab lang C++ %}

```bash
vc331@emon:~$ cd HW0C++
vc331@emon:~/HW0C++$ g++ -std=c++11 Driver.cpp
vc331@emon:~/HW0C++$ ./a.out testcases/input1.txt
```

{% endtab %}

{% tab lang Java %}

```bash
vc331@emon:~$ cd HW0Java
vc331@emon:~/HW0Java$ javac src/ub/cse/algo/*.java
vc331@emon:~/HW0Java$ java -cp "src" ub.cse.algo.Driver testcases/input1.txt
```

{% endtab %}

{% tab lang Python %}

```bash
vc331@emon:~$ cd HW0Python
vc331@emon:~/HW0Python$ python Driver.py testcases/input1.txt
```

{% endtab %}

{% endtabs %}

Compare your output with `outputs/output1.txt` to see if your code is correct, and test your code with other test cases.
