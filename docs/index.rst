=======================
 Qyri Linking Language
=======================
--------------
 Installation
--------------

Install Rust
============

To begin using the Qyri Linking Language (QLL), you need to download Rustup and install Rust. You can do so `here <https://www.rust-lang.org/tools/install>`_. Follow the instructions there to begin using cargo. Next, with `git installed <https://git-scm.com/book/en/v2/Getting-Started-Installing-Git>`_, open your terminal and change directories to where you want to clone the ``qll-based`` branch of the Qyri Programming Language repository using the following command:

::
	$ git clone -b qll-based https://github.com/Xinovaz/qyri-lang.git

(do not use the '$' in the command, this is the command prompt)

Testing with QLL
================

Once you have cloned the repository, access the ``arclight`` crate, and locate the file named either ``test.qll`` or ``main.qll`` (name may vary between versions of QLL). This file can be renamed if you wish, but the contents therein are what should be edited to customise your program. When you have made the changes you needed to make, and are ready to run the program, run the following command in the ``arclight`` directory:

::
	$ cargo run -- <name>.qll

The parser will parse your file, and then the virtual machine will run your QLL code.