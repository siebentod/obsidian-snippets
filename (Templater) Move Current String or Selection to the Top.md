See also [Move String or Selection Under Next Heading](https://github.com/siebentod/obsidian-snippets/blob/main/(Templater)%20Move%20all%20completed%20tasks%20in%20file%20A%20to%20file%20B.md).

```
<%*
const editor = this.app.workspace.activeLeaf.view.editor;
const file = this.app.workspace.activeLeaf.view.file;
let textToLift =''
if (tp.file.selection()) textToLift = tp.file.selection();
else {
	const getCurSelection = editor.getSelection();
	const getCur = editor.getCursor("from");
	const curLineNo = getCur.line;
	textToLift = editor.getLine(curLineNo);
    editor.setSelection({line: curLineNo, ch: 0}, {line: curLineNo + 1, ch: 0});
}

const curContent = await this.app.vault.read(file);
const newContents = textToLift + '\n' + curContent;
this.app.vault.modify(file, newContents);
editor.replaceSelection('');
%>
```
