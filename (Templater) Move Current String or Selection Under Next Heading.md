See also [Move Current String or Selection to the Top](https://github.com/siebentod/obsidian-snippets/blob/main/(Templater)%20Move%20Current%20String%20or%20Selection%20to%20the%20Top.md).
If string or selection is not unique, it could choose incorrect heading.

```
<%*
const editor = this.app.workspace.activeLeaf.view.editor;
const file = this.app.workspace.activeLeaf.view.file;
let textToLift = '';

if (tp.file.selection()) {
    textToLift = tp.file.selection();
} else {
    const getCurSelection = editor.getSelection();
    const getCur = editor.getCursor("from");
    const curLineNo = getCur.line;
    textToLift = editor.getLine(curLineNo);
    editor.setSelection({ line: curLineNo, ch: 0 }, { line: curLineNo + 1, ch: 0 });
}

const curContent = await this.app.vault.read(file);
const regex = new RegExp(`([\\n\\s\\S]+?${textToLift}[\\n\\s\\S]+?#.*)`);
const newContents = curContent.replace(regex, `$1\n${textToLift}`);

await this.app.vault.modify(file, newContents);
editor.replaceSelection('');
%>
```
