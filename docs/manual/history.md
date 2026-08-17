# History

Undo and Redo move through operations performed by the current user during the current browser session.

- Select **Undo** or press `Ctrl+Z` to apply the inverse of the latest local operation.
- Select **Redo** or press `Ctrl+Y` to reapply the latest undone operation.

Creating, updating, and deleting models or annotations, changing model transforms, and editing model metadata are represented as reversible operations. Performing a new edit clears the redo stack.

History is held in memory: it is not included in scene JSON and is lost on reload. In a collaborative scene, remote operations do not enter your undo stack. Undoing or redoing your own operation broadcasts the resulting change so other connected users see it.
