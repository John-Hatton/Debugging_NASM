# GDB - The GNU Debugger

* [Getting Started](#getting-started-with-gdb)
* [GDB Commands](#gdb-commands)
    + [Set Break Point](#set-break-point)
    + [Change to Intel Flavor](#change-disassembly-flavor-to-intel)
    + [GDB Run](#gdb-run)
    + [GDB Disassemble](#gdb-disassemble)
    + [Check Registers Status](#info-registers)
    + [Step Forward](#step-forward)
    + [Extracting Values from Dissasembly](#extracting-values)
* [Now What?](#now-what)


## Getting Started with GDB

Once you've successfully assembled and compiled your executable 
(ex: HelloWorld), you can run it in the GNU Debugger with the following command: 

    >: gdb ./HelloWorld



GDB will welcome you with a request to enable "auto-downloading 
debuginfo:


    >: ...(y or [n]) 
    >: ...(y or [n]) y


Once you arrive at the debugger, you might think something is wrong, 
because it will remind you that no debugging symbols were found in
the file you're working with. Don't mind that. You'll know it's all good when it looks like this:

    (gdb) _



---

## GDB Commands


The first Command we will want to run is break. This will set a break point.

### Set Break Point

    Set a break point
    (gdb) break <symbol>

    ex: 
    (gdb) break main

Executing the above command will set a break point in your assembly file, where the main subroutine begins. At this point we could do one of a couple things. We could step forward in the code, simply moving the program counter, or we could get info about the current state of the registers. Lets start with disassembly though. 

By default, GDB prints in AT&T syntax. There are a few major differences between AT&T and Intel syntax, however since we've written our code with Intel syntax, we'll be switching the session of GDB to Intel syntax. In the a few steps we'll disassemble. If you're feeling brave, skip this step, and view the code in AT&T syntax, but refer back, if you become confused.

### Change Disassembly Flavor to Intel

    Change disassembly flavor to Intel
    (gdb) set disassembly-flavor intel

Now we're ready to proceed with the debugging. At this point, we have only set the disassembly flavor, but we cannot do anything else until we tell the debugger to start debugging.

### GDB Run

    (gdb) run

Hurray! We've successfully started the debugger. If we refer back to the list of things to do, we're gonna want to disassemble first. This is because we haven't changed anything yet. The debugger will be set at the beginning of main. It will show an arrow, pointing at its position. After we disassemble, we can check the state of the registers, and then step forward, and repeat.


    (gdb) disassemble

### GDB Disassemble
This will give a response something like the following: 


<pre>
(gdb) disassemble
Dump of assembler code for function main:
=> 0x0000000000401130 <+0>:     push   rbp
   0x0000000000401131 <+1>:     movabs rdi,0x40509b
   0x000000000040113b <+11>:    movabs rsi,0x405044
   0x0000000000401145 <+21>:    xor    rax,rax
   0x0000000000401148 <+24>:    call   0x401030 <printf@plt>
   0x000000000040114d <+29>:    pop    rbp
   0x000000000040114e <+30>:    push   rbp
   0x000000000040114f <+31>:    movabs rdi,0x40509b
   0x0000000000401159 <+41>:    movabs rsi,0x405024
   0x0000000000401163 <+51>:    xor    rax,rax
   0x0000000000401166 <+54>:    call   0x401030 <printf@plt>
   0x000000000040116b <+59>:    pop    rbp
   0x000000000040116c <+60>:    push   rbp
   0x000000000040116d <+61>:    movabs rdi,0x40509b
   0x0000000000401177 <+71>:    movabs rsi,0x40502c
   0x0000000000401181 <+81>:    xor    rax,rax
   0x0000000000401184 <+84>:    call   0x401030 <printf@plt>
   0x0000000000401189 <+89>:    pop    rbp
   0x000000000040118a <+90>:    push   rbp
   0x000000000040118b <+91>:    movabs rdi,0x40509b
   0x0000000000401195 <+101>:   movabs rsi,0x405044
   0x000000000040119f <+111>:   xor    rax,rax
   0x00000000004011a2 <+114>:   call   0x401030 <printf@plt>
   0x00000000004011a7 <+119>:   pop    rbp
   0x00000000004011a8 <+120>:   push   rbp
   0x00000000004011a9 <+121>:   movabs rdi,0x405098
   0x00000000004011b3 <+131>:   movabs rsi,0x40507e
   0x00000000004011bd <+141>:   xor    rax,rax
   0x00000000004011c0 <+144>:   call   0x401030 <printf@plt>
   0x00000000004011c5 <+149>:   pop    rbp
   0x00000000004011c6 <+150>:   push   rbp
--Type <RET> for more, q to quit, c to continue without paging--
</pre>


You can press q to exit, obviously. Now that we know where the instruction pointer is pointing, lets get info about the state of the registers. Do so with: 

    (gdb) info registers


### Info Registers
A lovely response looks something like: 


<pre>
(gdb) info registers
rax            0x401130            4198704
rbx            0x7fffffffdaa8      140737488345768
rcx            0x404e18            4214296
rdx            0x7fffffffdab8      140737488345784
rsi            0x7fffffffdaa8      140737488345768
rdi            0x1                 1
rbp            0x1                 0x1
rsp            0x7fffffffd998      0x7fffffffd998
r8             0x0                 0
r9             0x7ffff7fcbb20      140737353923360
r10            0x7fffffffd6b0      140737488344752
r11            0x202               514
r12            0x0                 0
r13            0x7fffffffdab8      140737488345784
r14            0x404e18            4214296
r15            0x7ffff7ffd000      140737354125312
rip            0x401130            0x401130 <main>
eflags         0x246               [ PF ZF IF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
</pre>


### Step Forward
As you can see, our general purpose registers RAX, RBX, RCX, RDX, etc do not have integer values yet. The next logical step would be to step forward. Do so with: 

    (gdb) stepi

Note that this just moves the eip forward one unit. I'm not sure off the top of my head how large each "unit" is, but for now let's just stay abstract.


If we repeat the process from above, with disassemble and info registers, you will see the Program Counter is pointing at a different line...


<pre>
(gdb) disassemble
Dump of assembler code for function main:
   0x0000000000401130 <+0>:     push   rbp
=> 0x0000000000401131 <+1>:     movabs rdi,0x40509b
   0x000000000040113b <+11>:    movabs rsi,0x405044
   0x0000000000401145 <+21>:    xor    rax,rax
   0x0000000000401148 <+24>:    call   0x401030 <printf@plt>
   0x000000000040114d <+29>:    pop    rbp
   0x000000000040114e <+30>:    push   rbp
   0x000000000040114f <+31>:    movabs rdi,0x40509b
   0x0000000000401159 <+41>:    movabs rsi,0x405024
   0x0000000000401163 <+51>:    xor    rax,rax
   0x0000000000401166 <+54>:    call   0x401030 <printf@plt>
</pre>


### Extracting Values

As you'll likely note, the mov commands use memory addresses. We have to dereference this pointer, so to speak. The command to do so is a bit strange, but just try it out:

    (gdb) x/s 0x405044

We'll be hapy to see the friendly response from GDB:

<pre>
(gdb) x/s 0x405044
0x405044:       " "
(gdb)
</pre>


No, you're not having a brain aneurysm. In this example here, I'm simply passing a single space to the printf function. You can move along and step through the code, trying to pick apart what is going on in there...


---

## Now What?

Well I guess the only thing would be to find some sort of Context file that can regulate what we want when we say run, so it generates a wonderful looking report. Sadly, as is common with this crap, you have to heavily moderate whether the stuff you're messing with is x86 or x86_64. Watch out for that 32-bit schwag. Don't mess with that crap. It's a "for later" thing.



