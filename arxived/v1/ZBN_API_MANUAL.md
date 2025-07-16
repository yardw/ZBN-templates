# Zotero Better Notes API Manual

This manual covers the core APIs for plugin developers working with Zotero Better Notes (ZBN). These APIs are exposed via `Zotero.BetterNotes.api` and provide comprehensive functionality for note manipulation, templating, and content conversion.

## Overview

The ZBN API consists of several modules:
- **`utils`** - Utility functions for localization and user feedback
- **`note`** - Note content parsing and manipulation
- **`editor`** - ProseMirror editor integration and control
- **`template`** - Template engine for automated note generation

## Utils API

### `Zotero.BetterNotes.api.utils`

#### `getString(localeString: string, options?)`
Get localized strings from ZBN's localization system.

**Parameters:**
- `localeString`: FluentMessageId - The locale key
- `options.branch?`: string - Optional branch name for sub-attributes  
- `options.args?`: Record<string, unknown> - Arguments for string interpolation

**Returns:** `string` - Localized string

**Example:**
```javascript
// Simple string
const title = Zotero.BetterNotes.api.utils.getString("workspace-title");

// With branch
const buttonText = Zotero.BetterNotes.api.utils.getString("button-text", "confirm");

// With arguments
const message = Zotero.BetterNotes.api.utils.getString("template-count", {
  args: { count: 5 }
});
```

#### `requireRestart()`
Shows a dialog prompting user to restart Zotero.

**Example:**
```javascript
Zotero.BetterNotes.api.utils.requireRestart();
```

## Note API

### `Zotero.BetterNotes.api.note`

#### `insert(noteItem: Zotero.Item, html: string, lineIndex?: number)`
Insert HTML content into a note at a specific line index.

**Parameters:**
- `noteItem`: Zotero.Item - The note item
- `html`: string - HTML content to insert
- `lineIndex?`: number - Line index (default: append to end)

**Returns:** `Promise<void>`

**Example:**
```javascript
const note = Zotero.Items.get(noteId);
await Zotero.BetterNotes.api.note.insert(note, "<p>New content</p>", 5);
```

#### `getLinesInNote(noteItem: Zotero.Item)`
Get all lines in a note as an array of HTML strings.

**Parameters:**
- `noteItem`: Zotero.Item - The note item

**Returns:** `Promise<string[]>` - Array of HTML line strings

**Example:**
```javascript
const note = Zotero.Items.get(noteId);
const lines = await Zotero.BetterNotes.api.note.getLinesInNote(note);
console.log(`Note has ${lines.length} lines`);
```

#### `getNoteTree(noteItem: Zotero.Item)`
Parse note structure into a hierarchical tree based on headings and links.

**Parameters:**
- `noteItem`: Zotero.Item - The note item

**Returns:** `Promise<TreeModel.Node<NoteNodeData>>` - Root node of the tree

**Example:**
```javascript
const note = Zotero.Items.get(noteId);
const tree = await Zotero.BetterNotes.api.note.getNoteTree(note);
console.log(`Tree has ${tree.all().length} nodes`);
```

#### `getNoteTreeFlattened(noteItem: Zotero.Item, options?)`
Get a flattened array of all nodes in the note tree.

**Parameters:**
- `noteItem`: Zotero.Item - The note item
- `options.keepRoot?`: boolean - Include root node (default: false)
- `options.keepLink?`: boolean - Include link nodes (default: false)
- `options.customFilter?`: (node) => boolean - Custom filter function

**Returns:** `Promise<TreeModel.Node<NoteNodeData>[]>` - Array of tree nodes

**Example:**
```javascript
const note = Zotero.Items.get(noteId);
const nodes = await Zotero.BetterNotes.api.note.getNoteTreeFlattened(note, {
  keepRoot: false,
  keepLink: true
});
```

#### `getNoteTreeNodeById(noteItem: Zotero.Item, id: number)`
Find a specific node in the note tree by ID.

**Parameters:**
- `noteItem`: Zotero.Item - The note item
- `id`: number - Node ID

**Returns:** `Promise<TreeModel.Node<NoteNodeData> | undefined>` - Found node or undefined

**Example:**
```javascript
const note = Zotero.Items.get(noteId);
const node = await Zotero.BetterNotes.api.note.getNoteTreeNodeById(note, 5);
if (node) {
  console.log(`Found node: ${node.model.name}`);
}
```

## Editor API

### `Zotero.BetterNotes.api.editor`

#### `getEditorInstance(noteId: number)`
Get the ProseMirror editor instance for a note.

**Parameters:**
- `noteId`: number - Note item ID

**Returns:** `Zotero.EditorInstance | undefined` - Editor instance or undefined if not open

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  console.log("Note is open in editor");
}
```

#### `insert(editor: Zotero.EditorInstance, content: string, position?, select?)`
Insert content at a specific position in the editor.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `content`: string - HTML content to insert
- `position?`: number | "end" | "start" | "cursor" - Position (default: "cursor")
- `select?`: boolean - Whether to select inserted content

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  Zotero.BetterNotes.api.editor.insert(editor, "<p>New content</p>", "cursor", true);
}
```

#### `del(editor: Zotero.EditorInstance, from: number, to: number)`
Delete content between two positions.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `from`: number - Start position
- `to`: number - End position

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  Zotero.BetterNotes.api.editor.del(editor, 10, 20);
}
```

#### `move(editor: Zotero.EditorInstance, from: number, to: number, delta: number)`
Move content from one position to another.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `from`: number - Start position of content to move
- `to`: number - End position of content to move
- `delta`: number - Offset to move content by

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  Zotero.BetterNotes.api.editor.move(editor, 10, 20, 50);
}
```

#### `scroll(editor: Zotero.EditorInstance, lineIndex: number)`
Scroll to a specific line in the editor.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `lineIndex`: number - Line index to scroll to

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  Zotero.BetterNotes.api.editor.scroll(editor, 15);
}
```

#### `scrollToSection(editor: Zotero.EditorInstance, sectionName: string)`
Scroll to a specific section (heading) in the editor.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `sectionName`: string - Name of the section to scroll to

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  await Zotero.BetterNotes.api.editor.scrollToSection(editor, "Introduction");
}
```

#### `getLineAtCursor(editor: Zotero.EditorInstance)`
Get the line index at the current cursor position.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance

**Returns:** `number` - Line index (-1 if invalid)

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const line = Zotero.BetterNotes.api.editor.getLineAtCursor(editor);
  console.log(`Cursor is at line ${line}`);
}
```

#### `getSectionAtCursor(editor: Zotero.EditorInstance)`
Get the section name at the current cursor position.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance

**Returns:** `Promise<string | undefined>` - Section name or undefined

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const section = await Zotero.BetterNotes.api.editor.getSectionAtCursor(editor);
  console.log(`Cursor is in section: ${section}`);
}
```

#### `getPositionAtLine(editor: Zotero.EditorInstance, lineIndex: number, type?)`
Get the position (offset) at a specific line.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `lineIndex`: number - Line index
- `type?`: "start" | "end" - Position type (default: "end")

**Returns:** `number` - Position offset

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const pos = Zotero.BetterNotes.api.editor.getPositionAtLine(editor, 5, "start");
  console.log(`Line 5 starts at position ${pos}`);
}
```

#### `getLineCount(editor: Zotero.EditorInstance)`
Get the total number of lines in the editor.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance

**Returns:** `number` - Total line count

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const count = Zotero.BetterNotes.api.editor.getLineCount(editor);
  console.log(`Editor has ${count} lines`);
}
```

#### `getTextBetween(editor: Zotero.EditorInstance, from: number, to: number)`
Get plain text content between two positions.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `from`: number - Start position
- `to`: number - End position

**Returns:** `string` - Plain text content

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const text = Zotero.BetterNotes.api.editor.getTextBetween(editor, 10, 20);
  console.log(`Text: ${text}`);
}
```

#### `getTextBetweenLines(editor: Zotero.EditorInstance, fromIndex: number, toIndex: number)`
Get plain text content between two line indices.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `fromIndex`: number - Start line index
- `toIndex`: number - End line index

**Returns:** `string` - Plain text content

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const text = Zotero.BetterNotes.api.editor.getTextBetweenLines(editor, 5, 10);
  console.log(`Lines 5-10: ${text}`);
}
```

#### `getRangeAtCursor(editor: Zotero.EditorInstance)`
Get the current selection range.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance

**Returns:** `{from: number, to: number}` - Selection range

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  const range = Zotero.BetterNotes.api.editor.getRangeAtCursor(editor);
  console.log(`Selection: ${range.from} to ${range.to}`);
}
```

#### `moveHeading(editor: Zotero.EditorInstance, currentNode: TreeModel.Node<NoteNodeData>, targetNode: TreeModel.Node<NoteNodeData>, as: "child" | "before" | "after")`
Move a heading and its content to a new position.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `currentNode`: TreeModel.Node<NoteNodeData> - Node to move
- `targetNode`: TreeModel.Node<NoteNodeData> - Target node
- `as`: "child" | "before" | "after" - Position relative to target

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
const tree = await Zotero.BetterNotes.api.note.getNoteTree(editor._item);
const nodes = tree.all();
if (editor && nodes.length >= 2) {
  Zotero.BetterNotes.api.editor.moveHeading(editor, nodes[0], nodes[1], "after");
}
```

#### `updateHeadingTextAtLine(editor: Zotero.EditorInstance, lineIndex: number, text: string)`
Update the text of a heading at a specific line.

**Parameters:**
- `editor`: Zotero.EditorInstance - The editor instance
- `lineIndex`: number - Line index of the heading
- `text`: string - New heading text

**Example:**
```javascript
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);
if (editor) {
  Zotero.BetterNotes.api.editor.updateHeadingTextAtLine(editor, 5, "New Heading");
}
```

## Template API

### `Zotero.BetterNotes.api.template`

#### `runTemplate(key: string, argString?: string, argList?: any[], options?)`
Execute a template with the given arguments.

**Parameters:**
- `key`: string - Template key/name
- `argString?`: string - Argument names as string (default: "")
- `argList?`: any[] - Array of argument values (default: [])
- `options?`: object - Execution options
  - `useDefault?`: boolean - Use default templates if key not found (default: true)
  - `dryRun?`: boolean - Dry run mode (default: false)
  - `stage?`: string - Template stage to execute (default: "default")

**Returns:** `Promise<string>` - Rendered template content

**Example:**
```javascript
const result = await Zotero.BetterNotes.api.template.runTemplate(
  "MyTemplate",
  "noteItem, customVar",
  [noteItem, "customValue"],
  { dryRun: false }
);
```

#### `runTextTemplate(key: string, options?)`
Execute a text template (simpler version for standalone templates).

**Parameters:**
- `key`: string - Template key/name
- `options?`: object - Options
  - `targetNoteId?`: number - Target note ID
  - `dryRun?`: boolean - Dry run mode

**Returns:** `Promise<string>` - Rendered template content

**Example:**
```javascript
const result = await Zotero.BetterNotes.api.template.runTextTemplate(
  "MyTextTemplate",
  { targetNoteId: noteId, dryRun: false }
);
```

#### `runItemTemplate(key: string, options?)`
Execute an item template (processes multiple items).

**Parameters:**
- `key`: string - Template key/name
- `options?`: object - Options
  - `itemIds?`: number[] - Array of item IDs to process
  - `targetNoteId?`: number - Target note ID
  - `dryRun?`: boolean - Dry run mode

**Returns:** `Promise<string>` - Rendered template content

**Example:**
```javascript
const selectedItems = Zotero.getActiveZoteroPane().getSelectedItems();
const result = await Zotero.BetterNotes.api.template.runItemTemplate(
  "MyItemTemplate",
  { 
    itemIds: selectedItems.map(item => item.id),
    targetNoteId: noteId,
    dryRun: false 
  }
);
```

#### `runQuickInsertTemplate(noteItem: Zotero.Item, targetNoteItem?: Zotero.Item, options?)`
Execute a quick insert template for creating note links.

**Parameters:**
- `noteItem`: Zotero.Item - Source note item
- `targetNoteItem?`: Zotero.Item - Target note item
- `options?`: object - Options
  - `lineIndex?`: number - Specific line index
  - `sectionName?`: string - Specific section name
  - `selectionText?`: string - Selected text
  - `dryRun?`: boolean - Dry run mode

**Returns:** `Promise<string>` - Rendered quick insert content

**Example:**
```javascript
const sourceNote = Zotero.Items.get(sourceNoteId);
const targetNote = Zotero.Items.get(targetNoteId);
const result = await Zotero.BetterNotes.api.template.runQuickInsertTemplate(
  sourceNote,
  targetNote,
  { lineIndex: 10, dryRun: false }
);
```

#### `getTemplateKeys()`
Get all available template keys.

**Returns:** `string[]` - Array of template keys

**Example:**
```javascript
const keys = Zotero.BetterNotes.api.template.getTemplateKeys();
console.log(`Available templates: ${keys.join(", ")}`);
```

#### `getTemplateText(key: string)`
Get the raw template text for a given key.

**Parameters:**
- `key`: string - Template key

**Returns:** `string` - Raw template text

**Example:**
```javascript
const templateText = Zotero.BetterNotes.api.template.getTemplateText("MyTemplate");
console.log(`Template content: ${templateText}`);
```

#### `setTemplate(template: NoteTemplate)`
Create or update a template.

**Parameters:**
- `template`: NoteTemplate - Template object
  - `name`: string - Template name/key
  - `text`: string - Template content

**Example:**
```javascript
const template = {
  name: "MyCustomTemplate",
  text: `<h1>\${topItem.getField("title")}</h1>
<p>Created: \${new Date().toISOString()}</p>`
};
Zotero.BetterNotes.api.template.setTemplate(template);
```

#### `removeTemplate(key: string)`
Remove a template.

**Parameters:**
- `key`: string - Template key to remove

**Example:**
```javascript
Zotero.BetterNotes.api.template.removeTemplate("MyOldTemplate");
```

#### Constants

##### `SYSTEM_TEMPLATE_NAMES`
Array of system template names that cannot be modified:
- `[QuickInsertV3]` - Quick insert template
- `[QuickImportV2]` - Quick import template  
- `[QuickNoteV5]` - Quick note template
- `[ExportMDFileNameV2]` - Markdown filename template
- `[ExportMDFileHeaderV2]` - Markdown header template
- `[ExportMDFileContent]` - Markdown content template
- `[ExportLatexFileContent]` - LaTeX content template

##### `DEFAULT_TEMPLATES`
Array of default template objects with `name` and `text` properties.

## Template Development

### Template Syntax

Templates use JavaScript template literal syntax with special markers:

#### Basic Syntax
```javascript
// Simple variable interpolation
${noteItem.getNoteTitle()}

// Complex expressions
${noteItem.getTags().map(t => t.tag).join(", ")}
```

#### Async Code Blocks
For complex operations, use `${{...}}$` blocks:
```javascript
${{
  const items = await Zotero.Items.getAsync(itemIds);
  return items.map(item => item.getField("title")).join("\n");
}}$
```

#### Pragmas
Templates support special pragma comments:

```javascript
// @use-markdown - Process output as markdown
// @use-refresh - Enable template refresh functionality
// @default-begin / @default-end - Default stage markers
// @beforeloop-begin / @beforeloop-end - Before loop stage
// @afterloop-begin / @afterloop-end - After loop stage
```

### Template Stages

Templates can have multiple stages for item processing:

1. **beforeloop** - Executed once before processing items
2. **default** - Executed for each item (main stage)
3. **afterloop** - Executed once after processing all items

### Available Variables

#### In `runTemplate()`:
- Custom variables passed via `argList`
- `_env` - Environment object with `dryRun` property

#### In `runTextTemplate()`:
- `targetNoteItem` - Target note item
- `sharedObj` - Shared object for data storage

#### In `runItemTemplate()`:
- `items` - Array of items (beforeloop/afterloop stages)
- `topItem` - Current item (default stage)
- `targetNoteItem` - Target note item
- `itemNotes` - Notes attached to current item
- `copyNoteImage` - Function to copy images
- `sharedObj` - Shared object for data storage

#### In `runQuickInsertTemplate()`:
- `link` - Generated note link
- `linkText` - Display text for link
- `subNoteItem` - Source note item
- `noteItem` - Target note item
- `lineIndex` - Line index (if specified)
- `sectionName` - Section name (if specified)
- `selectionText` - Selected text (if specified)

## Error Handling

All API methods may throw errors. Always wrap calls in try-catch blocks:

```javascript
try {
  const result = await Zotero.BetterNotes.api.template.runTemplate("MyTemplate");
  console.log(result);
} catch (error) {
  console.error("Template execution failed:", error);
}
```

## Best Practices

1. **Check for null/undefined values** before using API methods
2. **Use dry run mode** when testing templates
3. **Handle editor instance availability** - editors may not be open
4. **Validate template syntax** before saving templates
5. **Use appropriate template stages** for item processing
6. **Cache template results** when appropriate
7. **Handle async operations properly** in templates

## Examples

### Creating a Custom Template

```javascript
// Create a template that generates a reading list
const readingListTemplate = {
  name: "ReadingList",
  text: `# Reading List - \${new Date().toLocaleDateString()}

${{
  const items = await Zotero.Items.getAsync(itemIds);
  let html = "";
  
  for (const item of items) {
    const title = item.getField("title");
    const authors = item.getCreators()
      .filter(c => c.creatorType === "author")
      .map(c => c.lastName + ", " + c.firstName)
      .join("; ");
    
    html += \`<h2>\${title}</h2>\`;
    html += \`<p><strong>Authors:</strong> \${authors}</p>\`;
    
    // Add notes if available
    const notes = Zotero.Items.get(item.getNotes());
    if (notes.length > 0) {
      html += "<h3>Notes:</h3>";
      for (const note of notes) {
        html += \`<blockquote>\${note.getNote()}</blockquote>\`;
      }
    }
  }
  
  return html;
}}$`
};

// Save the template
Zotero.BetterNotes.api.template.setTemplate(readingListTemplate);

// Use the template
const selectedItems = Zotero.getActiveZoteroPane().getSelectedItems();
const result = await Zotero.BetterNotes.api.template.runItemTemplate(
  "ReadingList",
  { itemIds: selectedItems.map(item => item.id) }
);
```

### Working with Editor

```javascript
// Get editor and manipulate content
const noteId = 12345;
const editor = Zotero.BetterNotes.api.editor.getEditorInstance(noteId);

if (editor) {
  // Insert content at cursor
  Zotero.BetterNotes.api.editor.insert(editor, "<p>New paragraph</p>");
  
  // Get current line
  const currentLine = Zotero.BetterNotes.api.editor.getLineAtCursor(editor);
  
  // Scroll to a specific section
  await Zotero.BetterNotes.api.editor.scrollToSection(editor, "Conclusion");
  
  // Get text from a range
  const text = Zotero.BetterNotes.api.editor.getTextBetween(editor, 10, 50);
  console.log("Selected text:", text);
}
```

### Working with Note Structure

```javascript
// Analyze note structure
const noteItem = Zotero.Items.get(noteId);
const tree = await Zotero.BetterNotes.api.note.getNoteTree(noteItem);

// Get all headings
const headings = tree.all(node => node.model.level <= 6);
console.log("Headings found:", headings.length);

// Find specific section
const introduction = tree.first(node => 
  node.model.name && node.model.name.includes("Introduction")
);

if (introduction) {
  console.log("Introduction section at line:", introduction.model.lineIndex);
}

// Get flattened structure
const allNodes = await Zotero.BetterNotes.api.note.getNoteTreeFlattened(noteItem);
for (const node of allNodes) {
  console.log(`Level ${node.model.level}: ${node.model.name}`);
}
```

This manual provides comprehensive coverage of the ZBN utility, note, editor, and template APIs, with practical examples for common use cases.