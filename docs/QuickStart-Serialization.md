---
id: quickstart-api-serialization
title: Serialization
layout: docs
category: Quick Start
next: advanced-topics-entities
permalink: docs/quickstart-serialization.html
---

# Serialization

Because a text editor doesn't exist in a vacuum and it's important to save
contents for storage or transmission, you will want to be able
[to convert `ContentState` into a plain JS object, and vice versa](/docs/api-reference-data-conversion.html#content).
For storing it in a database, it may be easiest to store it in a string field as json.

```js
import {Editor, EditorState, convertToRaw} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
    this.onSave = () => {
      var content = this.state.editorState.getCurrentContent();
      var raw = JSON.stringify(convertToRaw(content));
      $.post('/api/comment', {comment: raw}, () => {
        alert('Saved');
      });
    };
  }
  render() {
    return (
      <div>
        <Editor editorState={this.state.editorState} onChange={this.onChange} />
        <button onClick={this.onSave}>Save</button>
      </div>
    );
  }
}
```


You can then load the contents back into an `EditorState`.

```js
var editorState =
  EditorState.createWithContent(convertFromRaw(JSON.parse(raw)));
```

To display the contents statically, you can create an Editor component with
the `readOnly` prop set to `true`.

```js
<Editor editorState={editorState} readOnly />
```

## Why not HTML or markdown?

At Facebook, we do not use HTML or markdown to store rich text contents.
This allows us to avoid storing style information or metadata within the
raw text contents of the stored state.

This in turn enables us to render styles and metadata anywhere -- on
native platforms, for instance -- without needing to parse from HTML
or markdown.

The editor content is a rich object that contains the "what" and not the "how" for the content.
This allows you to change the rendering after the data is submitted by the user, and to include
interactive widgets in the display.

In addition, by using JavaScript objects and not HTML to represent our
editor, it would be possible to build or display `Editor` components on
non-web platforms.
