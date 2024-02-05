This template adds a task to the top of specified 'Quick Tasks.md' file (used to parse it with Tasks queries). Task description taken from prompt.
See also [a quick way to clean 'Quick tasks' file of completed tasks](https://github.com/siebentod/obsidian-snippets/blob/main/(Templater)%20Move%20all%20completed%20tasks%20in%20file%20A%20to%20file%20B.md).

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
