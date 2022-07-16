# Making Something From Nothing

# How do you make something from... nothing?
When you want to learn how to program, you will come upon two different types of
resources. One of those resources will start with the assumption that you know
how to program already, you're just trying to learn a different programming
language. Moving from one programming language to another is much easier to do,
as you already know most of the fundamental concepts of how computers work, how
your code works, and how to reason programmatically about things. This can be
likened to changing from a Windows computer to a MacOS computer. Much of the
things are the same, there are just different words, or placement of buttons.

The second type of resource you're going to come upon is the tutorial from the
ground up. This is the thing you're looking for, but often the author uses
jargon, or they assume other knowledge that you have. Alternatively, the author
will just brush certain things off, and just plow ahead, telling you "oh, just
ignore that for now, you'll understand what it means later." Unfortunately for
me, and you might be in the same camp, that doesn't really cut it. I don't like
just doing something without understanding the inner workings of things. I'm
going to be slightly hypocritical here and show you an example.

```java
public class Example {
	public static void main(String[] args) {
		system.out.println("Hello World!");
	}
}
```
_Figure 1: Java code for printing the phrase "Hello World!" to the terminal._

This snippet of code (Java is the language, BTW), was shown to me when I first
started programming. Lots of things to unpack here, and my teacher would gloss
over many of the details. I asked "what does public mean"? and admittedly, they
did try to answer, but the answer came with so many more concepts that I didn't
know or understand, the answer was pretty meaningless. So it's understandable
that most teachers just say to memorize certain things. My intro Java teacher
told me to just memorize "public static void main paren string bracket bracket
args end paren", for example. This was irksome to me, so I want to straighten
some of this out and try to really get a "from the ground up" approach to this.

# The computer runs on 0's and 1's
You always see this in shows, and when you're on the computer... you see
Facebook, you see the windows desktop, etc. I see no zeros and ones, so what's
the deal with that?

Well... it is true, technically... But we need to take a step back in order to
really put it in perspective and understand what that statement even really
means. so, let's back up a little bit just to think of the
theory of information. Take this string of characters for example:

```
ORDLAXSEAIADATLHNLBOS
```
_Figure 2: A long sequence of seemingly-random capital letters._

What do these characters mean? Well, as it stands, not much. However, if we
break them up in a certain way, it might become more useful.

```
ORD LAX SEA IAD ATL HNL BOS
```
_Figure 3: A series of three capital letter sequences. One interpretation of this might be a list of acronyms for airports in the United States._

Okay, does this mean anything to you? This is much more useful to me. Once
broken up with spaces like this, I actually recognize those three letter
groupings as acronyms for airports in the USA. (It's okay if you didn't
recognize that, I'm just a freak, it's fine).

## Interpreting information
Great, that's really cool, we can recognize some meaning from a string of what
seemed like random characters. This is effectively what a computer does with
0's and 1's. And we can too, if we went with similar (what I'll call) "rules".

```
0100100001100101011011000110110001101111001011000010000001110010011001010110000101100100011001010111001000100001
```
_Figure 4: A long string of 0's and 1's._

Here's a string of 0's and 1's. What does it mean though? Not necessarily
anything, unless we apply some of the right rules to interpret this data.

Well... a long time ago, back in like the 60's (1963 is when the first edition
of the ASCII standard was published), a bunch of people came together and made
some hard decisions. They decided what a grouping of 0's and 1's would be, and
they also decided what each group would correspond to. The American Standard
Code for Information Interchange, or ASCII (pronounced Ask-y) is one such set
of "rules" for how to interpret this series of ones and zeros. Basically, we
told the computer that whenever it sees `01100001`, it should display the
character `a` (under ASCII). ASCII describes an entire mapping of each
permutation of eight 0's and 1's. We call each 0 or 1 "slot", a `bit`. A
grouping of 8 bits is one `byte`. Let's break up the previous string of bits
into bytes.

```
01001000 01100101 01101100 01101100 01101111 00101100 00100000 01110010 01100101 01100001 01100100 01100101 01110010 00100001
```
_Figure 5: A long string of 0's and 1's broken up at 8 character marks; i.e. byte marks._

And if we interpret these bytes as ASCII...

```
H e l l o ,  r e a d e r !
```
_Figure 6: The string "Hello, reader!" in ASCII, with spacing between characters; i.e. byte marks._

Boom!

I added spaces just for the visualization, but of course the computer doesn't
need to break this up with spaces, since it can figure out the bytes by itself.


# Okay... but why 0's and 1's???
Long story short, because electricity.

The longer story, it's about voltage, and our ability to confidently measure
that. I'm not going to pretend that I'm an electrical engineer, but I will try
my best to describe what goes on here. We set voltage high or voltage low in
order to store information, like a light switch—the light is either on or off,
and we don't really have any intermediate states (shush about those weird
slider lights, this is just an example/analogy). Therefore we're storing
information in a series of either on's or off's.

There are people researching how to do quantum computing, which would
theoretically allow us to store information in 4 states, or base 4. This is
still experimental... but the gist of it is that it's looking at the quantum
states of an electron and how it exists in order to encode information.


# So how come I so rarely actually see 0's and 1's?

Typically, we humans can't easily tell what a byte means when displayed as a
string of 0's and 1's, so often we display it in another format: Hexadecimal.
Although, that's usually not amazingly better, I've (unfortunately) begun to
recognize various hexadecimal for its ASCII representation. For example, if I
see the bytes `4D 5A`, I know those represent the letters "MZ", since those come
up a lot in my work. It is admittedly easier on the eyes to look at as opposed
to looking at the 0's and 1's representation.
For our `Hello, reader!` example, in hexadecimal it would look like:

```
48 65 6c 6c 6f 2c 20 72 65 61 64 65 72 21
```
_Figure 7: The phrase "Hello, reader!" displayed as hexadecimal._

How is this equivalent...? Well, math. The system of numbers we all know and
love is in "base 10", and is thus called "decimal". Remember the decimal point?
that was so we could break something down to be less than 1. Let's take the
number 127 as an example. Essentially, we had the following in decimal:

| 100's | 10's | 1's |
|--------------------|
|  1    |   2  |  7  |

_Table 1: The number 127 broken down into base 10 (decimal) columns_

In order to get the number 127. Which means there are 1 groupings of 100, 2
groupings of 10, 7 groupings of 1. Each time we hit a "10 boundary", we remove
all elements from the first "bucket" we're in, and mark one in the next highest
bucket. So when we get to 10 1's, we increment the 10's column, and set the 1's
column back to 0. This should be familiar to you.

Well binary is just base 2, and hexadecimal is base 16. so that means the
boundary is at 2 for binary, and the buckets go 1, 2, 4, 8, etc. In hexadecimal,
the boundary is 16, so each "bucket" or column can hold up to 15 in it before it
resets and increments the next column over.

All of these things are succinctly described with math—like so, from right to
left. Base 10:

| 10<sup>2</sup> | 10<sup>1</sup> | 10<sup>0</sup> |
|--------------------------------------------------|
|     1          |       2        |       7        |

_Table 2: The number 127 broken down into base 10 columns by powers of 10_

Basically, for any base, you just make that the big number and put the power
next to it of the column index from the right to left and those are your
"buckets". Pretty neat, huh?

So, what does 127 look like in bases 2 and 16?

| 128's | 64's | 32's | 16's | 8's | 4's | 2's | 1's |
|----------------------------------------------------|
|   0   |  1   |  1   |  1   |  1  |  1  |  1  |  1  |

_Table 3: the number 127 broken down into base 2 (binary) columns_

| 16's | 1's |
|------------|
|  7   |  f  |

_Table 4: the number 127 broken down into base 16 (hexadecimal) columns_

For hexadecimal, it should be noted that since they run out of digits (0-9 being
used already) they just leak over into the alphabet and start taking from the
top. That is, letters A-F are used to denote 10-15 in hexadecimal.

# Still not sure how this makes the computer work?

Okay, so we covered how information is represented in different ways using 1's
and 0's, but we don't really talk about how the code that you write in X
programming language actually makes the computer do things.

Well, turns out, just like how we can take a bunch of 0's and 1's and make those
represent letters of the English alphabet, we too can use these 1's and 0's to
make the various hardware components of our computer do things. Mostly, the
Central Processing Unit, or CPU. Every CPU that gets made will have a mapping of
0's and 1's "hard-coded" or "hard-wired" into it that makes it do a thing. So
just like how we have

```
48 65 6c 6c 6f 2c 20 72 65 61 64 65 72 21
```
_Figure 8: The phrase "Hello, reader!" displayed as hexadecimal._

mean "Hello, reader!" if we interpret it as ascii, we can also have those bytes
mean something else in different CPU languages, called machine languages,
somewhat analogous to the term "architecture", though.

Taking the above bytes and interpreting as x86 assembly, a very popular machine
language results in the following machine code

```asm
48656C                          INS BYTE PTR GS:[RDI],DX
6C                              INS BYTE PTR [RDI],DX
6F                              OUTS DX,DWORD PTR [RSI]
2C20                            SUB AL,20
7265                            JB 000000000000006E
61                              ???
64657221                        JB 000000000000002F
```
_Figure 9: The phrase "Hello, reader!" interpreted as x86 assembly... it doesn't really work._

on the left, we have a breakdown of the bytes corresponding to the "instruction"
on the right, per line. Essentially, at machine languages, the operations are
generally "atomic"; that is, they cannot be broken down into smaller steps. This
tends to make individual assembly instructions fairly meaningless. Take for
example, the fourth line of the "disassembly":

```asm
2C20                            SUB AL,20
```
_Figure 10: The ", " of "Hello, reader!" interpreted as x86 assembly._

This line is saying the bytes `2C 20` correspond to the instruction `SUB AL,20`.
This instruction, translated to "English" (rather, nerd English) means "Subtract
20 from the 'AL' register and store it into the 'AL' register". (In assembly
language, they have a limited set of places to store information temporarily,
these places are called "registers", and they all have simple little names like
"al", "bl", "cl", etc. Put another way, think of how in math you might have
`2x + 1 = 15`, where `x` represents some variable. Registers in assembly are
basically variables, but in assembly they just have a limited set of variables
that are available. As if you could only use "x", "y", and "z" as variable
characters.) This doesn't mean too much, but honestly, the whole disassembly of
the phrase "Hello, reader!" doesn't mean too much. But as you can see, your CPU
is basically running all of these instructions, and that's what the frequency
corresponds to. Some CPU's will say that they have a clock speed of, say,
3.5 GHz. This means that they are able to read and execute 3.5 billion of those
instructions per second. That's a lot... but they're also pretty small.

## Sure, so assembly is a thing... a weird thing... how does code make it to that though?

Right, well... let's take a programming language like C. I take this as my
example, since it is considered a "lower level" language.

```c
void main() {
	printf("Hello, world!\n");
}
```
_Figure 11: C code that will print the phrase "Hello, world!" to the screen in a
terminal window, once it has been "compiled"._

Just to go over what's happening here, the line `void main() {` is creating a
"function", a bit of executable code that can run, and naming it "main". In C,
and many other languages, a pre-requisite for having a program/app is having a
function named "main". That's just what the people who invented C wanted. the
word `void` just means that this function doesn't "produce" anything. In this
context, that's kinda useless, but when you have many functions, you'll want
them to produce something versus just "do" something. For example, think about a
coal miner. That coal miner is going to mine coal, but you'll want the coal
miner to not just mine the coal, but also bring the coal back out of the mine,
not just lose it from the rocks in the mine. In this case, we have our function
"main" just scrawling "Hello, world!" onto the wall, but we don't need/want it
to bring anything back to us. The line `printf("Hello, world!\n");` is the part
that actually prints to the screen the string "Hello, world!". it does this by
"calling" the "printf" "function" with the "parameter" `Hello, world!\n`. printf
is a function just like our "main" function is a function, but printf was
created by the people who made the C programming language. The "\n" is for
making a "new line" after printing "Hello, world!", so that anything written
after we've written "Hello, world!" will be at the beginning of another line.
The final line `}` is just ending the function we named main. Kinda a lot to
unpack, I know, I tried to make the example as simple/straight-forward as
possible though.

With a low level language like C, we have to do a thing called "compiling".
Compilation is a fancy term for "translation", (it's called compiling because
usually programs are much more complicated than this and comprise of many
different code files referring to each other, and compilation takes all those
things and makes into one executable file versus a ton of text files with C code
written in them. I should note, sometimes compiling/compilation is referred to
as "building". Essentially, the same thing.

After doing compilation on this, we are given an executable file. Since I'm
using Linux, it might look a bit different than if this code were compiled for a
system like Windows or MacOS, but here is the disassembly of the code in the
executable file after I've compiled it.

```asm
55                      push   rbp
48 89 e5                mov    rbp,rsp
bf c4 05 40 00          mov    edi,0x4005c4
e8 d5 fe ff ff          call   400410 <puts@plt>
5d                      pop    rbp
c3                      ret    
0f 1f 00                nop    DWORD PTR [rax]
```
_Figure 12: Disassembly of our "Hello, World!" C program._

Cool. What does that all mean? Well, some of this is actually kinda useless to
the actual "direct" functioning of the program. I'll break it out into the
different parts of this.

```asm
; Function prologue, basically just announcing the fact that we are entering a
; function.
55                      push   rbp
48 89 e5                mov    rbp,rsp

; Move the value 0x4005c4 into the register "edi".
; Call the function located at 0x400410 in memory. (this is the function called
; "puts")
; What's happened here is that our C compiler was being clever and replaced our
; function call to "printf" with "puts", since it's easier and we weren't using
; any of the fancy stuff that printf _can_ do.
; The value 0x4005c4 is probably the location in memory that our string
; "Hello, World!" is located so that "puts" can find it when it looks at the
; register "edi".
bf c4 05 40 00          mov    edi,0x4005c4
e8 d5 fe ff ff          call   400410 <puts@plt>

; Function epilogue, basically just cleaning up the fact that we were just in a
; function
5d                      pop    rbp
c3                      ret    

; Totally useless. This is probably just a "compiler optimization". Generally,
; it's done in order to get our code to "align", or be at least 4 bytes wide.
; Counting the bytes above, there are 12, which is a multiple of 4 so... idk.
; Compilers are weird sometimes :P
0f 1f 00                nop    DWORD PTR [rax]
```
_Figure 13: Disassembly of our "hello world" C program with comments and spacing._

If you're following this, you're either fairly experienced with this stuff
already, or you're crazy akin to this stuff. If you're staring at this, and just
thinking "WTF", you're not in the wrong. I would argue that that's a pretty
standard line of thinking at this point. In fact, I would argue that most
programmers in this day and age don't know _any_ assembly or how it works, why
their code runs... etc, at all. So, good job, you probably already
know/understand some of the inner workings of their code/programming more than
they do.
