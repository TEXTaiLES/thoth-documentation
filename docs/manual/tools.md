## Selecting a tool
To select a tool, do one of the following: 
    
- Click a tool in the Tool panel.
    
- Press the tools's keyboard shortcut. The keyboard shortcut is displayed in its tool tip. More information on tooltips can be found under the [keybinds](../keybinds/keybinds.md) section.

Each tool also has additional adjustable options that can alter its usage, such as size and normal thresholds.

## Tools

### <img src="../../assets/icons/measure.png" alt="measure" width="20" style="vertical-align:middle; margin-right:6px;"> **Measure (M)**

Creates a measurement on the object, measured in meters.

**Options**

* **Mode**: Selects between euclidean (straight line) and geodesic (around the geometry) measure.

### <img src="../../assets/icons/brush.png" alt="brush" width="20" style="vertical-align:middle; margin-right:6px;"> **Brush (B)**

Allows the user to paint directly onto 3D surfaces within the scene by holding down.

**Options**

* **Size**: Adjusts the size of the selection.

### <img src="../../assets/icons/eraser.png" alt="eraser" width="20" style="vertical-align:middle; margin-right:6px;"> **Eraser (E)**

Allows the user to erase directly from 3D surfaces within the scene.

**Options**

* **Size**: Adjusts the size of the selection.

### <img src="../../assets/icons/lasso.png" alt="lasso" width="20" style="vertical-align:middle; margin-right:6px;"> **Lasso (L)**

Allows the user to form a free-hand selection onto the screen, from which a selection will be formed on the object.

**Options**

* **Resolution**: Adjust the level of detail of the lasso selection.
* **Select occluded faces**: Enables selection of obstructed faces
* **Normal threshold**: Adjusts the tolerance of the lasso tool. The higher the threshold the lower the tolerance.

### <img src="../../assets/icons/none.png" alt="no-tool" width="20" style="vertical-align:middle; margin-right:6px;"> **No Tool (N)**

This option unselects all tools, allowing for easier scene navigation.

## Additive/Subtractive mode

Each tool has a default functionality to either add or remove faces from a selection (e.g, the brush adds faces to a selection). This functionallity is used when the tool is used with the left mouse button.

When a tool is used with the right mouse button, any faces that would be added to the selection would instead be removed.

## Navigation

While any tool (except the no tool option) is selected, normal navigation is disabled. However, **you can navigate the scene with a tool selected if you hold down the spacebar button**. Selecting the **no tool** tool will unselect a tool and enable navigation.