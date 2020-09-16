# Process

## Generalities

* Anything running
* Each process has an address space and an id \(_PID_\) 
* Each process has a parent. If it dies, the previous one is attributed as parent
  * First process : before `init`, now [`systemd`](https://zcugni.gitbook.io/notes/theory/systemd)
* _Uid_ : Who owns the process
* _Euid_ : Allows you to give other permissions than the ones of the process's owner
* _Niceness_ : Determines its priority in the cpu/resource usage \(the nicer it is, the less it's gonna use it\)

## Signals

* Signals can be sent to processes by the kernel or other processes
* "Terminate" ask the process to terminate itself, "kill" just does it directly.
* Processes can decide to block and ignore signals, except for `SIGKILL` & `SIGSTOP`

## **State**

* Runnable : Eligible to be scheduled for CPU time
* Sleeping : Waiting for something
* Zombie state : Finished doing what it should and waiting to give back the info and be killed
* Stopped : Was doing something but `SIGSTOP` received and waiting for `SIGCON`

## Commands

| Command | Definition |
| :--- | :--- |
| `kill -l` | List signal |
| `kill <signal_nb> <pid>` | Send signals \(default `SIGTERM`\) |
| `killall <name>` | Sends to all processes starting by name |
| `pkill -u <username>` | Kill all username's processes |
| `nice` | Set niceness of process |
| `renice` | Change niceness of process |
| `ps` | List running processes |
| `which <name>` | Tell where the binary is located |
| `top` | List running processes \*\(1\) |

* \*\(1\) By default the ones using the most power in first

### PS

* Display information on active process
* Accept multiple syntax for parameters
  * Unix \(par ex `-a`\)
  * BSD \(sans \`-\` : `a`\)
  * Les long \(`--a`\)
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

