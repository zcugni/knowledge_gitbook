# Process

## Generalities

* A process is a program in execution
* Each process has an address space and an id \(**PID**\) 
* Each process has a parent. If it dies, the previous one is attributed as parent
  * First process : before `init`, now [`systemd`](https://zcugni.gitbook.io/notes/theory/systemd)
* Different UID :
  * Real \(**RUID**\) : Who owns the process, affects permission for sending signals
  * Effective \(**EUID**\) : In the name of whom the process runs \(may differ from the owner thanks to a suid bit set\)
    * Use for most access check
    * Determine the owner of a file created by a process
  * Saved UID \(**SUID**\) ****: In this context, it serve to save the precedent euid when a process drop privileges for a moment.
    * It's kind of linked to the concept of suid bit on files, but not exactly the same thing
* **Niceness** : Determines its priority in the cpu/resource usage \(the nicer it is, the less it's gonna use it\)

## Signals

* Signals can be sent to processes by the kernel or other processes
* When a process receives a signal, its flow of execution is interrupted by the os to call a signal handler
  * For exemple, 
* Signals are identified by a number, and each one has a default signal handler
* "Terminate" ask the process to terminate itself, "kill" just does it directly.
* Processes can decide to block and ignore signals, except for `SIGKILL` & `SIGSTOP`
* Interrupts are a type of signals

## **State**

* Runnable : Eligible to be scheduled for CPU time
* Sleeping : Waiting for something
* Zombie state : Finished doing what it should and waiting to give back the info and be killed
* Stopped : Was doing something but `SIGSTOP` received and waiting for `SIGCON`

## Commands

| Command | Definition |
| :--- | :--- |
| `nice` | Set niceness of process |
| `renice` | Change niceness of process |
| `ps` | List running processes |
| `which <name>` | Tell where the binary is located |
| `top` | List running processes \*\(1\) |

* \*\(1\) By default the ones using the most power in first

### Kill & co

* General syntax : `kill [options] <pid>`
* Sends `SIGTERM` by default
* Shell might have a built-in kill, theses options works for the original `/bin/kill`

| Option | Description |
| :--- | :--- |
| `-<signal_nb>` | Use this signal |
| `-s <signal_nb>` | Same |
| `-l` | List signal |
| `-l <signal_nb | signal_name>` | Give the corresponding name to a signal number and vice-versa |

* Other kill cmd :
  * `killall <signal>` Sends signal to all processes starting by name
  * `pkill -u <uid>` Kill all the process of this user

### PS

* Display information on active process
* List all running processes `ps aux`
* Accept multiple syntax for parameters
  * Unix \( `-a`\)
  * BSD \(without \`-\` : `a`\)
  * Long \(`--a`\)
  * Because of that, there's redundancy between parameters
* By default, ps select all processes with the same EUID as the current user and associated with the same terminal as the invoker.

| Option | Effect |
| :--- | :--- |
| `a` | Lift the BSD "only yourself" restriction |
| `-A`/`-e` | Select all processes |
| `-N ..` | Not ... |
| `x` | Lift the BSD "must have a terminal" restriction |
| `T` | Select all processes associated with this terminal |
| `r` | Only running processes |
| `-p <pid_list>` / `p <pid_list>` | Select by PID |
| `-c <cmd_list>` | Select by cmd |
| `-t <terminal_list>` / `t <terminal_list>` | Select by terminal |
| `-u <euid_list>` / `U <euid_list>` | Select by EUID |
| `u` | Display in user-oriented format |
| `-f` | Display in full format listing |

