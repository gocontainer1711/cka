# vim Shortcuts for CKA


- Display Line Number

`:set nu`

- How go to a particular word in a file

`:/<word>`

- How to go to a particular line in a file
  
  `:2` to jump to line 2
  `:123` to jump to line 123
::i::
- Auto Indentation

    Type `:set filetype=yaml` to set the file type to YAML. This will enable syntax highlighting and indentation for YAML files.
    To enable auto-indentation, type `:set autoindent` or `:set ai` and press Enter. This will enable automatic indentation for the current buffer.
    If you want Vim to also automatically adjust the indentation level when you press Enter to start a new line, type `:set smartindent` or `:set si`

- w – go to the next word.
- e – go to the end of the current word.
- b – go to the previous (before) word.
- 0 – go to the starting of the current line.
- ^ – go to the first non blank character of the line.
- $ – go to the end of the current line.


- Delte an entire line
    To delete a line in Vim, you can use the following commands:

   **dd: This command deletes the current line**. Place the cursor on the line you want to delete and type dd to delete the line.


- Delete an entire yaml block
  To delete a YAML block in Vim, you can use the following steps:

  Move the cursor to the first line of the YAML block you want to delete.
  Enter visual mode by typing `v`.
  Move the cursor to the last line of the YAML block you want to delete. This will highlight the block in visual mode.
  Type d to delete the highlighted YAML block.


- Undo last change

  Here are the steps to undo the last change:

  Press the Esc key to ensure you're in normal mode.
  Type **u** and press Enter.
  This will undo the last change and revert the document to its previous state. You can also use the **Ctrl + r command to redo** the last undone change.

- Delete current line and edit
  If you want to delete a line and replace it with new text, you can use the cc 










