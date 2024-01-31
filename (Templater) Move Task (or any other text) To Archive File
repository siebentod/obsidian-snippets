This template moves a highlighted task (or any other text) to the top of a specified "Tasks Archive.md" file. I used it to manually archive completed tasks

```
<%*
let filePath = 'Path/Tasks Archive.md';  // Insert your 'Path/Filename'! Directory must exist
let file = this.app.vault.getAbstractFileByPath(filePath);
if(!file) {
    file = await this.app.vault.create(filePath, '');
}

let cmEditorAct = this.app.workspace.activeLeaf.view.editor;
let selectedText = tp.file.selection();
tR = selectedText;

const curContent = await this.app.vault.read(file);
let newContents;
let selectLine;
newContents = selectedText + '\n' + curContent;
await this.app.vault.modify(file, newContents);
cmEditorAct.replaceSelection('');
tR = '';
%>
```
