This template adds a task to the top of a specified "Quick Tasks.md" file (used to parse it with Tasks queries). Task description taken from prompt

```
<%*
let filePath = 'Path/Quick Tasks.md';  // Insert your 'Path/Filename'! Directory must exist
let file = this.app.vault.getAbstractFileByPath(filePath);
if(!file) {
    file = await this.app.vault.create(filePath, '');
}

let newTask = await tp.system.prompt("New Task");
let appendTask = '- [ ] ' + newTask;
let curContent = await this.app.vault.read(file);
let newContents = appendTask + '\n' + curContent;
this.app.vault.modify(file, newContents);
%>
```
