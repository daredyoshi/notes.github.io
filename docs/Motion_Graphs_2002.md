# Motion Graphs

## Introduction
*Motion Graph* is a structure that encodes how the captured clips may be re-assembled in different ways. It is a directed graph wherein edges contain either pieces of original motion data or automatically generated transitions. Each *node* is a choice point where small bits of motion join seamlessly. A motion graph is a direct graph where all edges correspond to clips of motion. 


*Graph Walks* are the issue of selecting sequences of nodes. Algorithms in graph theory create graph walks that satisfy certain criteria. 

## Motion Graph Construction 
Because it's unlikely that two pieces of data are similar (houdini transition graph), we need to create cilps for that purpose. *Transitions* are clips desigened such that they can seamlessly connect two segments of original data. This is hard, so we look for similar portions of the clips where linear blending can provide a good result. (lol just like sidefx after all)

### Detecting Candidate transitions
Why to not simply locate transition points by computing some vector norm to measure the difference between poses at each pair of frames:

1. Meaning of Parameters. (A finger joint dose not has as much influence as the hips do)
2. Neet to compare in the space of the agent 
3. You need to analyze multiple frames. Transitions must account not only for pose but also for velocity, accelleration, and maybe more.

They use a point cloud similarity driven by the skeleton to measure similarity. This will be very similar to how the skinned geometry will deform, as that's the final ground truth of wether the transition will be effective. *Ideally this is a downsampling of the skinned mesh of the agent*

# To be cont! 
