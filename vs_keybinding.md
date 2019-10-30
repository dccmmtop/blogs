---
tags: [vscode]
---
```json
[
  {
    "key": "ctrl+shift+alt+down",
    "command": "editor.action.copyLinesDownAction",
    "when": "editorTextFocus && !editorReadonly"
  },
  {
    "key": "ctrl+alt+up",
    "command": "editor.action.copyLinesUpAction"
  },
  {
    "key": "ctrl+shift+i",
    "command": "editor.action.formatSelection",
    "when": "editorHasSelection && editorTextFocus && !editorReadonly"
  },
  {
    "key": "ctrl+k ctrl+f",
    "command": "-editor.action.formatSelection",
    "when": "editorHasSelection && editorTextFocus && !editorReadonly"
  },
  {
    "key": "f2",
    "command": "-editor.action.rename",
    "when": "editorHasRenameProvider && editorTextFocus && !editorReadonly"
  },
  {
    "key": "f2",
    "command": "-debug.renameWatchExpression",
    "when": "watchExpressionsFocused"
  },
  {
    "key": "f2",
    "command": "-debug.setVariable",
    "when": "variablesFocused"
  },
  {
    "key": "f2",
    "command": "-renameFile",
    "when": "explorerViewletVisible && filesExplorerFocus && !explorerResourceIsRoot && !explorerResourceReadonly && !inputFocus"
  },
  {
    "key": "f2",
    "command": "workbench.action.toggleSidebarVisibility"
  },
  {
    "key": "ctrl+b",
    "command": "-workbench.action.toggleSidebarVisibility"
  },
  {
    "key": "f3",
    "command": "-editor.action.nextMatchFindAction",
    "when": "editorFocus"
  },
  {
    "key": "f3",
    "command": "workbench.action.terminal.toggleTerminal"
  },
  {
    "key": "ctrl+`",
    "command": "-workbench.action.terminal.toggleTerminal"
  }
]
```

