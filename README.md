# OpenNets-SegmentNet

### Segmentation vs. Detection

Although classifying a satellite image as containing one or more structures is an important first step, real values would be added by an algorithm that can also segment these structures out from the background, so that they are easily identified. 

Convolutional neural networks to do segmentation are still in their infancy, but the relative simplicity of satellite imagery (low variance) allows us to test their potential. 

The precise algorithm and settings used were taken from here:
https://devblogs.nvidia.com/parallelforall/exploring-spacenet-dataset-using-digits/

### Image Generation

First, we download Open Street Map data for a specific country from here:
http://data.trimble.com/market/provider/OpenStreetMap.html

Selecting a random polygon at a time, we use the Google Map API to download satellite imagery, centered on the polygon. 

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-1.png]]

We then find all other polygons that are completely within the image. 

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-2.png]]

Rasterizing the polygons provides us with a binary image mask: 0 for no building, 1 for building. 

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-3.png]]

Finally, we transform these binary masks by computing the signed distance of each point to the boundary of the polygon. These distances are centered at 128, such that higher values mean points closer to the center of the polygon, while values lower than 128 indicate points outside of the polygon (i.e. no building). 

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-4.png]]

### Neural Network

The neural network close resembles the architecture of a auto-encoder, with the image first being fed forward through a series of convolutional layers, downsampling the image and therefore forcing the network to represent the images in a lower dimensional space (i.e. encode features). Then, rather than using a traditional fully connected layer, we use a series of deconvolution steps to up-sample the image to a black and white mask. The loss function is thus defined as the difference between the signed distance mask image and the output of the network. 

### Example In/Outputs

The left column represents the input to the neural net: the actual satellite images. The middle column represents the output of the neural net: a black/white gradient image indicating the confidence of the neural net of having detected a building/structure at that location. Finally, the right column overlays both images. 

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-5.png]]

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-6.png]]

[[https://github.com/gbrouwer/OpenNets-SegmentNet/blob/master/figures/3-7.png]]


### Generalization

Crucial to the usefulness of our methodologies is that they generalize well. To test this, we trained our network on satellite data from Uganda, and tested it on satellite data from both Nigeria and Bhutan. Initial findings suggest that the generalization does happen, although some unique building/structure types might not be detected if not trained on. Therefore, we propose to train the network on data taken from all areas of interest. 

