# Architecture

## Overview

Let $\mathcal{X}$ and $\mathcal{Y}$ be the set of images and possible ages respectively. Given a face image $x\in\mathcal{X}$ and a target image $y'\in\mathcal{Y}$, our goal is to train a single Generator $G$ such that it can generate a face image $x'$ of a particular age $y'$ corresponding to the identity in $x$. In addition, we introduce an age modulator within G to reshape identity features by considering the target age and utilize it as a self-guiding information.

In comparison to prior works, the main objkect is to robustly transform face age as well as maximum retention of age-unrelated information in $x'$, such as background, hairstyle, expression, etc. This means this framework should preserve the age-unrelated information contained in the input image during the age transformation process. Therefore, we consider a simple strategey on encoder and decoder networtks to share some of the valuable information.

## Proposed Framework

The proposed GAN-based frame is divided into the generator consiting of an encoder, a modulator, and a decoder, and the discriminator. Since the discriminator follows exisiting approaches, it was not very broadly elaborated, only the generator part is described in depth. The generator makes use of encoder-decoder architecture for image generator and is made of an identity encoder $Enc$, an age modulator $AM$, and a decoder $Dec$. In a superficial view, the encoder and decoder networks perform the same procedyre as existing works, yet they have a few modifications.

One of the difference the proposed and the existing works is the indegration of addition sub-network at the bottleneck of the generator. By this networtk, we can obtain features providing information on how a particular person should look like at the age under consideration. Given that such age-ware features are learned based ona given input image, then it can be used for further generation process.

## Identity Encoder
Given an image $x$ for age transformation, our identity encoder $Enc$ extracts identity-related feature $f_{id}$ of the image, where $f_{id}=Enc(x)$. Particularly, the encoder provides such features that supply facial structures at the local level and general information on face shape. These features are necessary to generate the same-looking face and thus have high importance, as discussed eqarlier. In turn, this importance intuitively leads us to focus on the face region only. Therefore, they proposed to perform a masking operation after transferring $x$ into the feature domain. To this, we utilize a network trained on sophisticated mask-based dataset. We delibrately perform masking at this stage such that we can obtain face as well as background-related features to operate on the face region only while maintaining the background informaion simultaneously. At the architecture level, the idenetity encoder is designed to have an image-to-featur level convolutional layer followed by downsampling blocks.

## Age Modulator
Age modulator $AM$ is constructed in the form of CNN, which is widely used in learning a low-dimensional vector of an input. It takes identity vectors $f_{id}$ from the encoder and by, considering given age information $y'$, outputs its reshaped version $f_{aw} = AM(f_{id}, y')$, where $f_{aw}$ is an element age-aware vector. To embed target age into AM, we add conditional batch normalization (CBN) layers used as a way to incorporate label information into the network. By doing so, AM learns optimal age-aware features for input identity, which enables satisfying both identity and age properties. Given that the network is integrated into G, it can be trained alongside the generator in an end-to-end manner. We implement AM as a set of downsampling layers with CBN technique producing a compact feature-vector used to modulate decoder layers.

```{image} ../images/model.png
:alt: model
:class: bg-primary mb-1
:width: 900px
:align: center
```
