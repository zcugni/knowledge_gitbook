---
description: A Terminal Multi-Plexer
---

# Tmux

## Generalities

* Lets you :
  * Cut your term's windows into panes
  * Attach/Detach a shell \(client\) to a session
    * This lets you keep a session running on a remote machine even if your ssh connection closes \(for example\)
    * You can share sessions with others
    * A session is displayed on screen by a client and all sessions are managed by a single server. The server and each client are separate processes which communicate through a socket in `/tmp`
* Launched with `tmux`, while in it, prefix commands with `ctrl-b`
* The `*` at the bottom shows in which window you are
* To use Vi key bindings \(default is emac\) after a ctrl-b type `:set-window-option -g mode-keys vi` 
  * Add this to the `~/.tmux.conf` file \(without the `:)`
* `ctrl-b ?` List all key bindings

## Windows shortcuts

These also exist in written cmd, but that's less practical.

| Command | Effect |
| :--- | :--- |
| `ctrl-b c` | Creates a new window |
| `ctrl-b ,` | Rename window |
| `ctrl-b p` | Previous window |
| `ctrl-b n` | Next window |
| `ctrl-b w` | List windows |
| `ctrl-b &` | Kill current window |

## Panes shortcuts 

These also exist in written cmd, but that's less practical.

| Command | Effect |
| :--- | :--- |
| `ctrl-b %` | Split pane vertically |
| `ctrl-b "` | Split pane horizontally |
| `ctrl-b x` | Kill pane |
| `ctrl-b o` | Switch to next pane |
| `ctrl-b <arrow_key>` | Switch to the pane in this direction |
| `ctrl-b q <nb>` | Show pane numbers and then switch to the given one |
| `ctrl-b-o` | Switch the position of the panes |
| `ctrl-b arrow keys` | Resize panes |
| `ctrl-b space` | Cycle layout |

## Session commands

| Command | Effect |
| :--- | :--- |
| `tmux new -s <session_name>` | Creates a new session |
| `ctrl-b d` | Detach the shell from the session |
| `tmux ls` | List all sessions |
| `tmux lsc` | List all clients |
| `tmux attach [-t <session_name>]` | Attach the shell to the tmux session |
| `tmux attach [-dt <session_name>]` | First detach other clients \(shells\) and then attach our shell to the session |
| `tmux detach [-s <session_name>] [-t <client>]` | Detach everyone from a session or a specific client |
| `tmux kill-server` | Kill the server, clients & sessions |
| `tmux kill-session [-t <session_name>]` | Destroy the session, closing all windows linked only to it & detach all client from it. |
| `tmux rename [-t <session_name>] <new_name>` | Rename a session |

## Copy Mode

This mode allows you to navigate the screen and search & copy information

| Command | Effect |
| :--- | :--- |
| `ctrl-b [` | Enter view mode |
| `q` | Quit |
| `space` | Highlight mode \(with vi key bindings\) |
| `enter` | Copy \(with vi key bindings\) |
| `ctrl-b ]` | Paste in another tmux pane/window |
| `/` | Search \(with vi key bindings\) |
| \(in search mode\) `n` | Next result \(with vi key bindings\) |
| \(in search mode\) `shift-n` | Previous result |

## Sources

* Man page
* Misc research

