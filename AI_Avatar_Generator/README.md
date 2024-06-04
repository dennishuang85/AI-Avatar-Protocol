## Introduction
This is the AI tech for Voka Avatar Generater SDK. 

## Tech Structure

![Untitled Diagram](https://user-images.githubusercontent.com/20713531/206143677-086ba3a8-98ad-433b-9330-2ab4cc70307b.jpg)

https://user-images.githubusercontent.com/19359257/206367275-0687a023-691e-4ab9-90af-7f9d454fad4f.mp4

## Environment
1. Prefer python 3.7 or higher. 
2. The updated version of pytorch. 
3. the landmark detection requires mmdet, refer: https://github.com/open-mmlab/mmdetection
4. using cycleGAN for image segmentation requires to install pytorch-CycleGAN-and-pix2pix，refer: https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix
5. For importing fbx file, it needs offical fbx python sdk，refer: https://www.autodesk.com/developer-network/platform-technologies/fbx-sdk-2020-0 
6. pytorch3d installation can be referred offical installation guide. 


## 使用方法
 - 所有需要运行的流程应该都在pipeline里
 - 流程：
   1. 先对给定的这些目标模型进行预处理，主要是找到四个“环”，左眼，右眼，嘴，脖子，有的模型因为拓扑问题找不到嘴和脖子的封闭环没有关系。这个流程会生成一个json文件，里面包含了每个模型的四个环的顶点信息。这些信息都已经处理完成并且保存好了，不需要再次运行。如果加入了新的模型，就需要先预处理出这些信息。
   2. 用模板和这些模型进行对齐，对齐之后会得到一个映射模型，和模板中每个顶点对应的位置信息，已经保存好了，不需要再次运行。
   3. 因为给定的贴图不一定是和模板对齐的，所以还要重新把贴图贴到模板上，生成新的贴图，这个过程比较慢，已经处理好的贴图已经保存好了，不需要再次运行。
   4. 对得到的一套顶点坐标进行PCA降维到20或者25维，降维后的矩阵也保存好了。
   5. 根据图片进行模型生成，参考demo.ipynb。可以只给定一张图，如果之前在cyclegan里进行过分割，也可以加入分割的mask作为生成贴图的依据。stage1，程序会先调用detection检测landmark，然后对齐landmark，stage2，程序会继续微调landmark并同时开始生成贴图，使生成的模型和原图像尽量接近。

## 注意事项
 - 在对齐过程中对每个模型都放缩到单位大小，中心对齐到了原点，但是部分模型可能朝向有问题，是手动调整的。
 - 检测环的过程中有很多手动设置的参数，请小心调整
 - 之前都是自己一个人写，所以一些地方没有合理的设置api
 - 最后generate过程中，很多中间过程都是保存在tmp文件夹里的，没有重新建checkpoint的，可以在这里看中间过程，重新运行可能会造成覆盖
