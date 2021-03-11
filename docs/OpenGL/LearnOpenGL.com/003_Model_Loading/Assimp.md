# Assimp

Usually geometry is created by 3d artists in DCCs. The render programmer's job is to parse these exported model files and extract all the relevant information to store in a format that OpenGL understands. A common issue is that there are many file formats and each exports the model data in a different way. Wavefront objs are one of the most common but also limited 3d interchange formats. Of course we are also going to be long term interested in things like USD and Alembic. 

A very popular model import library is [Assimp](http://assimp.org) aka *Open Asset Import Library*. It has support for a ton of different model file formats. Once Assimp has loaded the model, we can retrieve the data from it in a common format. 

Assimp loads the entire model into a *scene* object that contains all the data of the imported model/scene. 

![assimp_structure](./assimp_structure.png)

* All the data is in a central root scene node
* This node may contain children nodes, and could have a set of indices that point to mesh data. All meshes are stored in the scene(root) mMeshes array. 
* Mesh objects themselves contain all the relevant data for rendering: vertex positions, normal vectors, texture coordinates, faces, and materials.
* Each mesh contains one or more faces. A **Face** is a render primitive of the object (triangles, squares, points). The face holds references to the indices of the vertices for that primitive. This makes it easier for us to render via an index buffer. 
* The material on a mesh hosts severl function to retrieve the properties of an object's material. Colors, texture maps, etc. 

Even though artists format data in a variety of different meshes, the rendering pipeline doesn't care about this. We take all the meshes and combine them into a single **Model** object.

I'm on arch so I just built assimp using the AUR. :P
