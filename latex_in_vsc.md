# Latex in VSCode
Installing latex can be a pain. Let me summarize how I did it after 2 hours of fiddling.

## Installation
To setup latex development in VSCode, on Linux, follow this steps:
1. Purge latex with `apt-get remove tex-common texlive` and `apt-get autoremove`
2. Follow [quickinstall](https://www.tug.org/texlive/quickinstall.html) to install `texlive`,
3. Update the enviroment PATH variable by adding `PATH="/usr/local/texlive/2024/bin/x86_64-linux:$PATH"` at the bottom of `~/.profile`,
4. Log out & log in,
5. Update build settings and recipes as indicated in [vscode-and-latex](https://mathjiajia.github.io/vscode-and-latex/),
6. Remember, don't rely on `apt` to install anything latex-related, otherwise you would end up with conflics between multiple versions.

## Keybinding
You can add custom keybinding by hitting `Ctrl+Shift+P` -> `Open Keyboard Shortcuts (JSON)` and adding the following at the bottom of the file:
```json
{
    "key": "ctrl+i",
    "command": "editor.action.insertSnippet",
    "when": "editorTextFocus && editorLangId == 'latex'",
    "args": {
        "snippet": "\\textit{${TM_SELECTED_TEXT}}"
    }
}
```

## Snippets
You can add your custom snippets at the bottom of `Ctrl+Shift+P` -> `Configure Snippets` -> `LaTeX`.

For instance, an `img` snippet may look like the following:

```json
"Insert Image": {
    "prefix": "img",
    "body": [
        "\\begin{figure}[ht]",
        "    \\centering",
        "    \\includegraphics[width=1\\textwidth]{${2:path/to/image}}",
        "    \\caption{${3:Caption}}",
        "    \\label{fig:${2:path/to/image}}",
        "\\end{figure}"
    ],
    "description": "Insert a figure with an image"
}
```