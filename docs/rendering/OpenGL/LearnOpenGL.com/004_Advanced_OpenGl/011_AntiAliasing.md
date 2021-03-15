# Anti Aliasing

The jagged edges around objects are known as **aliasing**. There are several **anti-aliasing** techniques to fight this. The first technique used to be **super sample anti-aliasing** (SSAA) that temporarily rendered to twice the resolution and then downsampled. This also created a major performance drawback, since it renders a *lot* more fragments. 

A more modern technique that grew out of this is called **multisample anti-aliasing** (MSAA) that brrows from the concepts of SSAA while implementing a much more efficient approach. 


## Multisampling

The rasterizer is the part of the pipeline that converts the vertices into fragments for the fragment shader to work on. For more detail on that check out the [pipeline map](../../Pipeline_Map.md). The rasterzer is what sets the pixels red in the following two images. 

