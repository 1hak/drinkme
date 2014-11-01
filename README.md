# drinkme



_drinkme_ is a very simple tool for testing shellcode. You feed it shellcode on stdin and it will execute it. With the -p switch _drinkme_ will print (but not execute) the newly formated shellcode. _drinkme_ ignores comments, quotes, stray semi-colons, etc.

_drinkme_ was designed as a test harness for shellcode developers. However, if you wanted to test random bits of shellcode from the internet (and you know what you are doing enough to feel safe with this) then _drinkme_ allows you to cut and paste easily from any of the shellcode repositories out there.

##Examples##

**write(STDOUT_FILENO, "Hello world!\n", strlen("Hello world!\n"))**

	empty@monkey:~$ cat hello_world.x86_64 
	\xeb\x1d\x5e\x48\x31\xc0\xb0\x01\x48\x31\xff\x40\xb7\x01\x48\x31\xd2\xb2\x0d\x0f\x05\x48\x31\xc0\xb0\x3c\x48\x31\xff\x0f\x05\xe8\xde\xff\xff\xff\x48\x65\x6c\x6c\x6f\x20\x77\x6f\x72\x6c\x64\x21\x0a
	
	empty@monkey:~$ drinkme <hello_world.x86_64 
	Hello world!


**execve("/bin/sh")**

	empty@monkey:~/code/drinkme$ cat execve_bin_sh.x86_64 
	    "\x48\x31\xd2"                                  // xor    %rdx, %rdx
	    "\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68"      // mov	$0x68732f6e69622f2f, %rbx
	    "\x48\xc1\xeb\x08"                              // shr    $0x8, %rbx
	    "\x53"                                          // push   %rbx
	    "\x48\x89\xe7"                                  // mov    %rsp, %rdi
	    "\x50"                                          // push   %rax
	    "\x57"                                          // push   %rdi
	    "\x48\x89\xe6"                                  // mov    %rsp, %rsi
	    "\xb0\x3b"                                      // mov    $0x3b, %al
	    "\x0f\x05";                                     // syscall
		
	empty@monkey:~$ drinkme -p <execve_bin_sh.x86_64 
	\x48\x31\xd2\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05
	
	empty@monkey:~/code/drinkme$ echo $$
	3880
	
	empty@monkey:~/code/drinkme$ ./drinkme <execve_bin_sh.x86_64 
	
	$ echo $$
	18613
	
##Usage##

	usage: drinkme [-p] [-h]
		-p	Print the formatted shellcode. Don't execute it.
		-h	Print this help message.
	
		Example:	drinkme <hello_world.x86_64
	
