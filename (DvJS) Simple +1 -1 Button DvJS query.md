This DataviewJS query takes all notes with #repetition tag and "repetition" property with value less then 5. And presents it with buttons +1 and -1, which affect "repetition" property. Works with inline fields

```dataviewjs
const {update} = this.app.plugins.plugins["metaedit"].api
const {createButton} = app.plugins.plugins["buttons"]

dv.table(["Title", "Times", "", ""], dv.pages("#repetition")
    .where(t => t.repetition < 5)
    .map(t => [t.file.link, t.repetition,
    createButton({app, el: this.container, args: {name: "+1"}, clickOverride: {click: update, params: ['repetition', t.repetition+1, t.file.path]}}),
	createButton({app, el: this.container, args: {name: "-1"}, clickOverride: {click: update, params: ['repetition', t.repetition-1, t.file.path]}})])
    )
```
