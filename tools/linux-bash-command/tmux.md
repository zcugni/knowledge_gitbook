---
description: A Terminal Multi-Plexer
---

# TMUX

## Généralités

* Lets you :
  * Cut your term's windows into panes
  * Detach a session from a shell, making it \(for example\) possible to close an ssh connexion but keep the command running.
  * Commands are send to the tmux server by prefixing them with `ctrl-b`
* Launched with `tmux`
* The \* at the bottom shows in which window we are

## Window

| Commande | Effet |
| :--- | :--- |
| `ctrl-b c` | Creates a new window |
| `ctrl-b ,` | Rename window |
| `ctrl-b p` | Previous window |
| `ctrl-b n` | Next window |
| `ctrl-b w` | List windows |
| `ctrl-b &` | Kill current window |

## Pane

| Commande | Effet |
| :--- | :--- |
| `ctrl-b %` | Split pane vertically |
| `ctrl-b "` | Split pane horizontally |
| `ctrl-b x` | Kill pane |
| `ctrl-b q` | Show pane numbers |
| `ctrl-b o` | Switch to next pane |
| `ctrl-b-o` | Inverse les panes |
| `ctrl-b arrow keys` | Resize panes |
| `ctrl-b space` | Cycle layout |

## Session

| Commande | Effet |
| :--- | :--- |
| `tmux new -s session_name` | Creates a new session |
| `ctrl-b d` | Detach the session from the shell |
| `tmux attach -t session_name` | Attach the tmux session to the shell |
| `tmux list-sessions` | List all sessions |

## Misc

| Commande | Effet |
| :--- | :--- |
| `ctrl-b ]` | Paste |
| `ctrl-b ?` | **List all key bindings** |

## View Mode

In this mode you can go up the terminal & search/copy, etc.

| Commande | Effet |
| :--- | :--- |
| `ctrl-b [` | Enter view mode |
| `q` | quit |
| `ctrl-space` | Highlight mode |
| `alt-w` | Copy |



