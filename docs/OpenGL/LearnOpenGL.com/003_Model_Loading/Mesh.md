# Mesh

Before loading our models it's helpful to create a mesh class. This will help to transform the data into a format that OpenGl understands so that we can render the objects. A mesh should at minimum have a vertex array, where each vertex contains a position vector, normal vector, and texture coordinate vector. It should also contain indices for drawing, and material data in the form of textures. 

Instead of making a vertex class, we have made a vertex struct. The advantage of this is that the data is sequenctial, and we can use offsetof to get the position of each variable in the struct. 

```cpp
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, Normal));  
```

# Model

Assimp gives you a pointer to a scene object. 

```cpp
Assimp::Importer importer;
const aiScene *scene = importer.ReadFile(path, aiProcess_Triangulate | aiProcess_FlipUVs); 
```

Here we are first declaring an **importer** object and then calling it's ReadFile function. To that function we are adding some post-processing flags to specify how we want to load it. Triangulate tells Assimp to convert the model to triangles, and FlipUvs tells it to flip the texture coordinates. There are some other interesting ones

* aiProcess_GenNormals : creates normal vectors for each vertex if the model doesn't have any
* aiProcess_SplitLargeMeshes: auto-splits large meshes into smaller sub-meshes which is great if your rendering has a limited amount of vertices
* aiProcess_OptimizeMeshes: does the reverse by joining meshes together

We now load all the data from Assimp

```cpp
void Model::Draw(ShaderProgram &shader) const{
    for (Mesh mesh : m_meshes){
        mesh.draw(shader);
    }
}

void Model::loadModel(std::string_view path){
    Assimp::Importer importer;
    const aiScene *scene = importer.ReadFile(
            path.data(), aiProcess_Triangulate | aiProcess_FlipUVs); 
    
    // check to see if scene data might be incomplete
    if(!scene || scene->mFlags & AI_SCENE_FLAGS_INCOMPLETE || !scene->mRootNode){
        std::cout << "ERROR::ASSIMP::" << importer.GetErrorString() << '\n';
        return;
    };
    m_directory = path.substr(0, path.find_last_of('/'));
    // this will recursively build a mesh out of all the scene node's meshes
    processNode(scene->mRootNode, scene);
}

void Model::processNode(aiNode *node, const aiScene *scene){
    // process all of the nodes meshes
    for(unsigned int i{0}; i < node->mNumMeshes; ++i){
        aiMesh *mesh = scene->mMeshes[node->mMeshes[i]];
        m_meshes.push_back(processMesh(mesh, scene));
    }
    // recursvly iterate over all child nodes
    for(unsigned int i{0}; i<node->mNumChildren; ++i){
        processNode(node->mChildren[i], scene);
    }
}
```

You could also loop over only the scene's meshes, but that would forgo parent-child relationships. This will make it easier to keep the transformations correct. We aren't doing that right now, but it's a good habit to loop over things this way. 

Now we just have to convert the aiMesh to our own mesh. Processing a mesh is a three part process:

1. Retrieve vertex data
2. Retrieve mesh indices
3. Retrieve material data




