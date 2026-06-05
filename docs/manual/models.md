# Models

<p align="center">
    <img src="../../assets/icons/scene.png" alt="Models" width="50"/>
</p>

## Model overview

The models panel is the main control point for importing, deleting and managing models. On a technical level, this corresponds the **nodes** attached to the root scene.

## Adding models to the scene

You can add new models in the scene by selecting the **Add model button** in the models panel, or by pressing **Shift + A**. 

<p align="center">
    <img src="../../assets/icons/add.png" alt="Add Model" width="50"/>
</p>

Models are, by default retrieved from the local ATON directory and must follow the [specifications defined by ATON](https://osiris.itabc.cnr.it/aton/index.php/tutorials/creating-3d-content-for-aton/).

## Managing imported models

Once a model is properly imported to your scene, you can manage the following attributes.

* **Visibility**: You can toggle the visibility of a model using the **visibility icon** on the left of the model controller.

* **Transforms**: You can modify the position and rotation of a selected object with the respective controls on any 3D axis.

Additionally, you can **focus** on a specific model by using the **focus button** for a selected layer. You can also view the meshes attached to each model.

## Deleting models

The user can delete a model by pressing the **Delete button** on the models's controller. This action is reversable. 

## Viewpoints

<p align="center">
    <img src="../../assets/icons/pov.png" alt="viewpoints" width="50"/>
</p>

THOTH allows you to view the source images from which the 3D object was reconstructed. 

To view a source image, click on one of the viewpoint spheres in the scene. This will open up a card containing information about the viewpoint (position, target, image). You can then view the image for higher-resolution viewing and downloading.

You can hide the viewpoint spheres from the Viewpoint tab in the settings menu.

*The displayed viewpoints are selected from the object's COLMAP*