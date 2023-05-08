Download Link: https://assignmentchef.com/product/solved-ve280-project-5-list-stack-and-queue
<br>



After playing Blackjack and beating Stardust Crusade, Haruhi got interested in computer science and wanted to design and implement her own game. Therefore, she started learning basic data structures and computer organization. Now, she decides to implement a templated double-ended doubly-linked list, or Dlist and then use it to build a stack machine. In this project, you will help her with these two tasks.

<h1>Task 1: The Double-Ended, Doubly-Linked List</h1>

The double-ended doubly-linked list, or Dlist, is a templated container. It supports the following operational methods:

<ul>

 <li>isEmpty: a predicate that returns true if the list is empty, false otherwise.</li>

 <li>insertFront/insertBack: insert an object at the front/back of the list, respectively.</li>

 <li>removeFront/removeBack: remove an object from the front/back of a nonempty list, respectively; throws an exception if the list is empty.</li>

</ul>

Note that while this list is templated across the contained type, T, it is a container of <strong>pointers-to-T</strong>, not a container of instances of T. Insertion takes a <strong>pointer-to-T</strong> as an argument and put <strong>that pointer</strong> into the node to be inserted. Removal removes a node and returns the pointer-to-T kept in that node. The implementation of Dlist abides by the following principle: it owns inserted objects, it is responsible for copying them if the list is copied, and it must destroy them if the list is destroyed.

The complete interface of the Dlist class is provided in Dlist.h, which is available in Project-5-Related-Files.zip on Canvas.

The definition of struct node, the private type for the nodes of the container list, is given in the private section of class Dlist. This is so to prevent the clients of the class from using that type.

In addition to the five operational methods, there are the usual four maintenance methods: the default constructor, the copy constructor, the assignment operator, and the destructor. Make sure that your copy constructor and assignment operator do <strong>full</strong> deep copies, <strong>including making copies of </strong><strong>T’s owned by the list</strong>.

Finally, the class defines two private utility methods removeAll and copyAll that implement the behaviors common to two or more of the maintenance methods.

<strong>You must implement each </strong><strong>Dlist method in a file called </strong><strong>DlistImpl.h.</strong> We will test your Dlist implementation separately from the other components of this project, so it must work independently of the application described below.

To instantiate a Dlist of pointers-to-int, for example, you would declare the list:

Dlist&lt;int&gt; il;

This instructs the compiler to instantiate a version of Dlist that contains pointersto-int, and the compiler compiles a version of the Dlist template that contains such pointers-to-int.

<h1>Task 2: Stack Machines</h1>

<h2><em>Problem </em></h2>

Most computers are built according to the von Neumann architecture, which consists of a central processing unit (CPU), a memory unit, and I/O devices. This architecture is fast and robust in general. Therefore, many instruction set architectures (ISAs) like ARM, MIPS, and so on, are designed based on it. However, another type of architecture is popular for virtual machines: stack machine. One of its main components is a stack to store operands. All operations can be executed just using this stack. Therefore, this architecture is simple to implement. Java virtual machine (JVM) and WebAssembly (Wasm), which are widely used nowadays, are both based on abstract stack machines. In this part, we plan to use our own Dlist class to implement a simple but powerful stack machine by ourselves.

<h2><em>General Computational Model </em></h2>

Our computational model is based on a simplified stack machine. It contains an operand stack, an instruction queue (or list), and a memory array. The structure is shown below.




To simplify our model, we only have 32-bit integers in the stack and memory. The memory is only 64 bytes (recall a byte = 8 bits), hence, we have 16 addresses where each address corresponds to a 32-bit integer. To compute on this machine, we always pop the first instruction from the queue and execute this instruction accordingly. We now introduce those instructions.

<h2><em>Instruction Set </em></h2>

This machine has an instruction set that contains many operations like arithmetic operations, control operations, memory operations, and so on.

<h2>Arithmetic Operations: ADD,NOR</h2>

This kind of operations is designed to deal with the operand stack. For example, ADD means popping up two elements from the stack and then pushing the sum of these two elements into the stack. The detail is shown below:




At first, the stack is [n1, n2, …]. And after executing ADD, the stack is [n1+n2, …].

Another operation is the bitwise boolean operator NOR. Its truth table is shown below:

<table width="148">

 <tbody>

  <tr>

   <td width="31">n1</td>

   <td width="31">n2</td>

   <td width="87">n1 NOR n2</td>

  </tr>

  <tr>

   <td width="31">0</td>

   <td width="31">0</td>

   <td width="87">1</td>

  </tr>

  <tr>

   <td width="31">0</td>

   <td width="31">1</td>

   <td width="87">0</td>

  </tr>

  <tr>

   <td width="31">1</td>

   <td width="31">0</td>

   <td width="87">0</td>

  </tr>

  <tr>

   <td width="31">1</td>

   <td width="31">1</td>

   <td width="87">0</td>

  </tr>

 </tbody>

</table>

For example, 5 NOR 7 = -8 because 5 is represented in binary by 00000101, 7 by

00000111, and 00000101 NOR 00000111 = 11111000, which is -8 in the 2’s

complement representation. In C++, you may use int result = ~(a|b); to perform the NOR operation. This instruction runs similar to ADD, hence, [n1, n2, …] -&gt; [n1 NOR n2, …].

<h2>Control Operations: IFZ,HALT</h2>

A control operation is used to control the instruction queue. For example, HALT means to halt the whole machine, hence, stop the program. Another non-trivial instruction is IFZ. It also has an argument n. When the front of the queue is IFZ n, we first pop up the first element in the stack. If this element is zero, we pop up the following n instructions in the queue. If this element is not zero, we continue executing the next instruction. An example is shown below.




<h2>Memory Operations: LOAD,STORE</h2>

A memory operation is designed for operating with the memory. For example, we want to get a value stored at an address or store a value to an address.

LOAD is to get the value stored in the memory. When executing it, we first pop up an element from the stack. This first element is the address. Then, we find the value stored at this address and push it into the stack. STORE means to store a value in the memory. When executing it, we first pop up <strong>two elements</strong>. The first element is the address and the second is the value. If we declare the memory as int mem[16];.

This means mem[address] = value. An example is shown below.




<h2>Stack Operations: POP, PUSHI</h2>

These two operations can control the elements in the stack directly. POP means to pop up an element directly and PUSHI n means to push a constant n into the stack.

<h2>Other Operation: NOOP</h2>

NOOP does nothing. Just read the operation and skip it.

<h3>Setup</h3>

As an ideal model, we assume that the stack and queue have an unlimited capacity. At the beginning, the instruction queue, operand stack and memory are initialized. Then, the stack machine starts by executing the instructions from the queue. Further details are provided in the I/O Format section below.

<em>I/O Format </em>

<h2>Input</h2>

In the first line, there are two numbers n, m where n is the number of elements in the stack, m is the number of elements in the queue. In the second line, there are n numbers divided by a space. You may push them in sequence in the stack. <strong>Hence, </strong><strong>1 </strong>

<strong>2 3 4 means that </strong><strong>4 is on the top and </strong><strong>1 is at bottom of the stack.</strong> In the next following m lines, there are m instructions. In the header file Instr.h, we provide a data structure Instr to hold values representing instructions. You can use the functions provided in it directly, e.g.,

Instr ins; std::cin &gt;&gt; ins;

In the last line, there are 16 numbers. They are shown as mem[0] mem[1] … mem[15]. Here is a sample input:

5 14

0 1 2 3 4

PUSHI 0

LOAD

PUSHI 1

LOAD

PUSHI 1

LOAD

NOR

PUSHI 1

ADD

ADD

PUSHI 2

STORE

NOOP

HALT

999 888 1 2 0 0 0 0 0 0 0 0 0 0 0 1

You do not need to check for errors. Whenever there is an instruction like ADD, the number of elements in the stack would be enough. Also, when the instruction is LOAD or STORE, the top of the stack is between 0 and 15. The input will ensure the machine will halt.

<h2>Output</h2>

When there is no argument provided, <strong>after</strong> executing <strong>each</strong> instruction, you should print the machine status. It consists of four lines: instruction that executed, stack, queue, memory. A sample is provided below:

PUSHI 1

1 2 3 4 999 1

LOAD PUSHI 1 LOAD NOR PUSHI 1 ADD ADD PUSHI 2 STORE NOOP HALT  999 888 1 2 0 0 0 0 0 0 0 0 0 0 0 1

Similarly, the output for Instr is provided, you can use it by:

Instr ins;

<em>// initialize ins</em> std::cout &lt;&lt; ins;

You may also use the operator &lt;&lt; overloaded for Dlist&lt;T&gt; to output:

Dlist&lt;int&gt; intList; std::cout &lt;&lt; intList;

<h2>Argument</h2>

When the program gets an argument -s, it doesn’t need to output anything after executing each instruction. You only need to print the machine status when the machine halts. The output consists of <strong>two</strong> lines: stack and memory. Here is an example for a sample input:

$ ./sam -s &lt; ./sample.in

1 2 3 4

999 888 111 2 0 0 0 0 0 0 0 0 0 0 0 1

<h3>General Steps</h3>

<ul>

 <li>You may first implement two classes Stack and Queue by using your own DList&lt;T&gt; They may contain methods like push(T x) to operate on elements.</li>

 <li>Initialize three data structures (i.e., stack, queue, array) by reading from the inputs. You may use push for queue and stack to add elements. The format is shown in the I/O Format section.</li>

 <li>Implement the stack machine. After executing each instruction, you should print the machine status.</li>

</ul>

<h1>Implementation Requirements and Restrictions</h1>

<ul>

 <li>You must use your Dlist container to implement both your stack and queue. However, remember that you only insert and remove <strong>pointers-to-objects</strong>. You must use the at-most-once invariant plus the Existence, Ownership, and Conservation rules when using your Dlist. Therefore, you can only insert dynamic objects.</li>

 <li>You may not leak memory in any way. To help you see if you are leaking memory, you may wish to call valgrind, which can tell whether you have any memory leaks. The command to check memory leak is: valgrind –leak-check=full &lt;PROGRAM_COMMAND&gt;</li>

</ul>

You should change &lt;PROGRAM_COMMAND&gt; to the actual command you use to run the program under testing.

Another way to check memory leaks is to compile with the -fsanitize flag in g++. The program compiled in this way will report an error if it exits with memory leak. An example:

g++ -Wall -Werror -O2 –std=c++17 -fsanitize=leak -fsanitize=addres s -o sam sam.cpp

See http://gavinchou.github.io/experience/summary/syntax/gcc-addresssanitizer/ for details if you are interested.

<ul>

 <li>You must fully implement the Dlist Note that the implementations of the stack machine may not exercise all of a Dlist’s functionality, but this is fine.</li>

 <li>You may #include &lt;iostream&gt;, &lt;string&gt;, &lt;sstream&gt;, &lt;cstdlib&gt;, &lt;algorithm&gt; and &lt;cassert&gt;. No other system header files may be included, and you may not make any call to any function in any other library.</li>

 <li>Input and output should only be done where it is specified.</li>

 <li>You may not use the goto</li>

 <li>You may not have any global variables that are not const.</li>

</ul>

<h1>Source Code Files and Compiling</h1>

There are two header files Dlist.h, Instr.h located in Project-5-RelatedFiles.zip from our Canvas Resources. Please do not modify them. We will use your DlistImpl.h and sam.cpp to compile. So, no other files will be compiled.