Completed tasks are moved to the top of 'Archive' file, from special 'Quick Tasks' file. See also [Quick tasks script](https://github.com/siebentod/obsidian-snippets/blob/main/(Templater)%20Add%20New%20Task%20To%20'Quick%20Tasks'%20File.md)

```
<%*
let archiveFilePath = '! Tagebuch/Tasks/Archive/Archived Tasks (Archive).md'; //Here you can input your path and filename!
let archiveFile = this.app.vault.getAbstractFileByPath(archiveFilePath);
let archiveContent = await this.app.vault.read(archiveFile);

let quickFilePath = '! Tagebuch/Tasks/Quick Tasks.md' //Here you can input your path and filename!
let quickFile = this.app.vault.getAbstractFileByPath(quickFilePath);
let quickContent = await this.app.vault.read(quickFile);

let newArchiveContent = '';
let regexp = /- \[x\].*/;

let matched = quickContent.match(regexp);
let archive = '';
let firstMatch;

while (matched != null) {
    firstMatch = matched[0];
    newArchiveContent = newArchiveContent + '\n' + firstMatch;
    quickContent = quickContent.replace(firstMatch, '')
    matched = quickContent.match(regexp);
}

while (quickContent.includes('\n\n')) {
	quickContent = quickContent.replace('\n\n', '\n')
}

if (firstMatch != undefined) {
	archiveContent = newArchiveContent + '\n' + archiveContent;
	await this.app.vault.modify(archiveFile, archiveContent);
	await this.app.vault.modify(quickFile, quickContent);
}
%>
```
