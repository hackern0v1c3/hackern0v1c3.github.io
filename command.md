## Shell Shortcuts
* `Alt + F` _Go forward one word_
* `Alt + B` _Go backwards one word_
* `Ctrl + A` _Go to beggining of line_
* `Ctrl + E` _Go to end of line_
* `Ctrl + U` _Delete everything left of cursor_
* `Ctrl + K` _Delete everything right of cursor_
* `Ctrl + W` _Delete current word_
* `Ctrl + R` _Search bash history_
* `history` _Show all bash history_

## Vim Commands
* `? {string}` _Go to previous instance of string_
* `/ {string` _Go to next instance of string_

## Tmux Commands
* `tmux new -s {name}` _Make a new tmux session with name_
* `Ctrl + b` _Default prefix key_
* `{prefix} + c` _Create new window_
* `{prefix} + {window number}` _Change window_
* `{prefix} + %` _Vertical split_
* `{prefix} + "` _Horizontal split_
* `{prefix} + arrow` _Change pane.  Or don't release CTRL to resize pane with arrows_
* `{prefix} + z` _Toggle pane full screen zoom_
* `{prefix} + {` _Move pane left_
* `{prefix} + }` _Move pane right_
* `{prefix} + spacebar` _Toggle layout options_
* `tmux join-pane -t {window}`_Move current pane to window_
* `tmux join-pane -s {session}:{window}.{pane} -t {session}:{window}.{pane}`_Move source pane to target pane_
* `{prefix} + d` _Detach session_