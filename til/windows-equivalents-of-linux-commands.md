# A Table of Windows Equivants of Linux Commands

I spend most of my life in *nix-type operating systems. Mostly MacOS for my daily driver at work, but my home laptop is Windows. Sometimes I like to work in powershell to keep myself nimble, but Windows has both Powershell "cmdlets", and binary executables that predate powershell.  These commands were used in the original _cmd_ terminal, and are still useful (and faster than powershell equivalents). The pleasant thing about these commands is that they'll work on any configuration of windows or powershell, and so are probably preferable for scripting cross-platform solutions in Windows. Shamelessly stolen from [geeks for geeks](https://www.geeksforgeeks.org/linux-unix/linux-vs-windows-commands/):

| Windows |	Linux |	Description |
|-------|------|---------|
| dir |	ls | -l	Directory listing |
| ren |	mv |	Rename a file |
| copy |	cp |	Copying a file |
| move |	mv |	Moving a file |
| cls |	clear |	Clear Screen |
| del |	rm |	Delete file |
| fc |	diff |	Compare contents of files |
| find |	grep |	Search for a string in a file |
| command | /? |	man command	Display the manual/help details of the command |
| chdir |	pwd |	Returns your current directory location |
| time |	date |	Displays the time |
| cd |	cd |	Change the current directory |
| md |	mkdir |	To create a new directory/folder |
| echo |	echo |	To print something on the screen |
| edit |	vim | (depends on editor)	To write in to files. |
| exit |	exit |	To leave the terminal/command window. |
| format |	mke2fs | or mformat	To format a drive/partition. |
| free |	mem |	To display free space. |
| rmdir |	rm | -rf/rmdir	To delete a directory. |
| taskkill |	kill |	To kill a task. |
| tasklist |	ps | x	To list running tasks. |
| set | var |=value	export var=value	To set environment variables. |
| attrib |	chown |/chmod	To change file permissions. |
| tracert |	traceroute |	To print the route packets trace to network host. |
| at |	cron |	daemon to execute scheduled commands. |
| type |	cat |	To print contents of a file. |
| ping |	ping |	To send ICMP ECHO_REQUEST to network hosts. |
| nslookup |	nslookup |	To query Internet name servers interactively. |
| chdisk |	du | -s	For disk usage. |
| tree |	ls | -R	To list directory recursively. |

What other little programs exist in windows?  I used to use `handle.exe`, but that appears to have been removed at least as of Windows 11.  `more.exe` still exists to read files from the terminal.
