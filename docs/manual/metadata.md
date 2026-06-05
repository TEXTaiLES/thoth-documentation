# Metadata

The scene layer, as well as any created layer in a scene, is paired with a set of metadata. This metadata is meant to describe the scene, along with any models inside it or the selections of each layer respectively.

<p align="center">
    <img src="../../assets/icons/list.png" alt="Metadata" width="50"/>
</p>

You can edit the metadata of the scene layer or any other layer using the **edit metadata** buttons on the scene/layer controller from the **Layer Panel**. You can also open the metadata editor using the shortcuts **Shift + S** or **Shift + {layer id}** respectively.

## Schema selection

Any of this metadata is formed according to a selected metadata schema which the user can choose when filling the metadata for any layer. The schema must follow a set of specifications as to how it's structured. Current default schema is the ```puc_schema``` which comes with the default THOTH installation.

## Attributes

Once you have selected a valid schema, you can edit any of the attributes generated according to the schema. Once you have set the desired values, save the changes to the metadata by pressing the **save changes button**. 

## Inheritting from the scene layer

<p align="center">
    <img src="../../assets/icons/scene.png" alt="Scene metadata" width="50"/>
</p>

You can inherit the metadata schema and values assigned to the scene layers to any selected layer by pressing the respective **inherit metadata button** inside the metadata editor.