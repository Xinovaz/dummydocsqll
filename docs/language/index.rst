========================
 Language Documentation
========================
------------
 Quickstart
------------

Hello, world!
=============

Writing a simple "Hello, world!" program in the Qyri Linking Language is straightforward, but deals with manipulating the stack in a way that won't be as intuitive as a traditional, higher-level programming language. Keep in mind that QLL is not meant to be written by humans, so even using it is a challenge within itself.

To write a program with QLL, you first need to understand the structure of a QLL program. All execution begins at the ``main:`` label. The ``main:`` label is **required** for a functioning QLL program. This can be placed anywhere in your program. However, it should be noted that it should end in a ``return`` statement, to delimit the label.

To begin managing data in the stack, you'll want to use stack instructions. A stack instruction always begins with a ``$`` sigil. If a stack instruction expects a parameter, two sigils should be used. Write the ``main:`` label and begin the program by pushing ``0`` to the stack with the ``$$pushi`` instruction:
::
	main:
		$$pushi 0
		return


Here, we begin program execution, push a value onto the stack, and end the program. This is great, but we want to print an ASCII character to the console. By default, the parser runs a virtual machine connected to stdin and stdout, so there's no special task that needs to be completed to speak to stdout. Simply use the ``$out`` stack instruction, which will pop an operand off of the stack and print its value as an ASCII character. Let's just print a lowercase 'a' for now. This has an ASCII value of 97.
::
	main:
		$$pushi 97
		return

If you run this, you should see Rust recompile the parser and print 'a' to the console. Now that we know how to print to the console, it may seem fairly straightforward to repeat this process for every character in the string "Hello, world!" However, we may want to create a function to repeat this task for us. Let's create a new label, above our main label, called ``.func.print``. This label is called automatically by the Arclight VM when a user of the Qyri Programming language calls to ``std.io.stdout.write``.
::
	.func.print:
		$$jmpz 3
		$out
		call .func.print
		return

	/* ~~snip~~ */

This will recursively print operands to the stack until it reaches one with a value of 0. Now let's modify our main function to print the letter 'a' with some more spacing.
::
	/* ~~snip~~ */

	main:
		$$pushi 0
		$$pushi 10
		$$pushi 10
		$$pushi 97
		$$pushi 10

		call .func.print

		return

When we run this program, we can see that we get what we want. Let's try replacing these instructions with the ones we need to print our celestial salutation.
::
	/* ~~snip~~ */

	main:
		$$pushi 0
		$$pushi 10
		$$pushi 33
		$$pushi 100
		$$pushi 108
		$$pushi 114
		$$pushi 111
		$$pushi 119
		$$pushi 32
		$$pushi 111
		$$pushi 108
		$$pushi 108
		$$pushi 101
		$$pushi 72
		$$pushi 10

		call.func.print

		return

This is it! That's your "Hello, world!" in the Qyri Linking Language. But, wait! There's a few issues with this program that cause it to be incompatible with other Qyri programs. First and foremost, we're missing our ``main`` function. This is different than the label we have. The function is an actual representation of the Qyri code, and it can be represented similarly to the our output function. Its job is to simply call the main label, like so:
::
	/* ~~snip~~ */

	.func.main:
		call main
		return

	/* ~~snip~~ */

This will allow us to call the main function from another Qyri program if we use this program as a library. However, if we did want to allow this functionality, we would need to give the program some front matter. Front matter is data accessed by a linker, in order to supply Qyri executables (including other .qll files) with data from this code. Front matter is too complicated to break down into simple descriptions here, but feel free to peruse the documentation for more information.
::
	:assembly[package]
	.public:
		:assembly[flags]
		.public.flags:
			addr 0
		  /*[~~~~~~~~~~~~~version number~~~~~~~~~~~~~]*/
			$$pushi 1; $$pushi 0; $$pushi 0; $$pushi 1
			$$pushi 0; $$pushi 1; $$pushi 1; $$pushi 0
		  /*[pshilgls]~[admnprms]~[rlswarns]~[wrnerrs]*/
			:transaction[read]
			.assoc.public.flags._read:
				addr 1
				$$pushi 8
				$$pushi 1
				return
			.assoc.public.flags._override:
				addr 2
				$$pushi 8
				$$pushi 0
				return
		return

	/* ~~snip~~ */

You would think that this would be our last step in completing, but it is not, as unfortunate as that is. Remember the ``$$jmpz 3`` instruction from our output function? That 3 represents the number of the instruction that we skip past when the operand at the top of the stack is equal to 0, which would get the instruction pointer to the ``return`` instruction. We need to change this to a different number, gotten by counting the number of instructions before the ``return`` instruction. The magic number here happens to be 28, as we do not count the comments. This process is very tedious to do by hand, which is why it is not suggested to write QLL. However, while moving the output function to the top of the program may seem like an ideal decision, it is considered unorthodox when considering the presentation of a QLL program.

The next thing we need to do to perfect this is merely attach a decorator to our ``.func.print`` label, which is done by prepending ``:illegal[builtin]`` to it, like so:
::
	/* ~~snip~~ */

	:illegal[builtin]
	.func.print:
		$$jmpz 29
		$out
		call .func.print
		return

Don't forget to increment that instruction number. The decorator is counted as an instruction by the virtual machine.

Finally, add this one line to your main label, at the start:
::
	/* ~~snip~~ */

	main:
		addr 16

		/* ~~snip~~ */

This simply allows memory allocation to occur properly when running the program. All QLL programs begin their memory allocation at address 16, as the first 16 bytes are allocated for front matter and other workings.

And that's it! Run the program and bask in glory. Check out the rest of the documentation to keep learning the Qyri Linking Language.
