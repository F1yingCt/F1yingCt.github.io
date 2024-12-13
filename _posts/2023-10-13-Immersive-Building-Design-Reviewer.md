---
layout: mypost
title: Immersive Building Design Reviewer
categories: [Research]
date: 2023-10-13
pinned: false
---

## Immersive Building Design Reviewer

Remote collaboration has drawn more and more attention of practitioners in AEC sectors. Especially in building design review, plenty of conceptions or techniques that derive from BIM(Building Information Modelling) have already meet the requirement of remote collaboration. 

However, there have not been a widely adopted platform for the collaboration of building design review. One of the causes is that even in a BCF(BIM Collaboration Format) platform, the efficiency of communication is limited. For example, cooperators need extra explanations like comments to understand the suggestions of each other. When these explanations become mass, cooperators may be confused and have to take effort to learn how to use the platform.

Virtual Reality provides us with powerful tools to handle these problems. In this project, we aimed to develop a building design reviewer that enables users to review the buildings with BIM in immersive environment, interact intuitively with the objects and leave their comments. 

In the following parts, several functions of our design reviewer will be introduced.  The VR hardware adopted in our reviewer is HTC Vive VR Bundles, including a HMD and two VR controllers.

![design_reviewer](https://s1.imagehub.cc/images/2024/12/12/eab94a963f9be20854e360a2e07426d5.md.jpg)

<br>

###  Navigation

This is a basic function that the implementation of architectural visualization system requires.  In our reviewer, Teleport is chosen as the navigation method for users to improve the efficiency and reduce the motion sickness.

**Teleport**

Users can target a destination through touchpad on their VR controllers and press to activate a teleportation. Immediately after they release the touchpad (after pressing), they will teleport to the destination.  

<br>

###  3-D Marks

Users are allowed to draw 3-D marks in our reviewer.  Press side button on right controller and users will activate the drawing mode, then they will see a little sphere indicating the pen. Users can make explanations through their marks.<br>

When users are satisfied with your marks, they can also press trigger on left controller to make a snapshot of their viewpoint.

![s3](https://s1.imagehub.cc/images/2024/12/12/869e450206e12bbf4cb7ef1c4503f497.md.png)

<br>

### BIM Support

As our reviewer is for practitioners sectors, all BIM data attached to objects will be reserved when importing to our reviewer. Users can select the objects by controllers and check their information.<br>

Also, users are able to leave their comments of each object through speech input. Comments will be attached to the objects and divided by usernames. 

![s2](https://s1.imagehub.cc/images/2024/12/12/fe3ac4d849848dc12af4a68613f22166.md.png)

<br>

Moreover, we allow users to store their comments and screenshot in BCF files, and they can transfer the review data to BCF platforms later. If you are interested in the implementation of this function, please read [BCF Storage in UE](https://f1yingct.github.io/posts/2023/05/30/Bcf-storage-in-UE-via-xml-format-file.html)

<br>

<br>

![ 2023 09 19 120214](https://s1.imagehub.cc/images/2024/12/12/ff2a6eac69f6419c927d29fedab537ce.png)

<br>

![s4](https://s1.imagehub.cc/images/2024/12/12/99647408e66716f91fd9018d86d4a441.png)

This is 3-D mark :)

<br>
