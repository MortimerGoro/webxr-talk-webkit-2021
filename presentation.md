# WebXR and Browsers
##### Imanol Fernandez
###### Igalia, September 2021

----
<!-- .slide: class="igalia-align-left" -->
### Today, we are going to talk about
+ Main challenges behind a WebXR Browser
	+ Rendering
	+ UI
	+ Privacy
+ Solution to address each challenge

----

#### Stereo Rendering

<img style="max-width: 65%;" src="public/img_stereo1.png">
<img style="max-width: 45%;" src="public/img_stereo2.png">

----

#### Problem: Distortion

Lens Distortion
<img style="max-width: 75%;" src="public/img_fov.png">

Pincushion Distortion.
<img style="max-width: 75%;" src="public/img_pincushion.png">

----

#### Solution: Distortion pass

Compensate with Barrel Distortion
<img style="max-width: 85%;" src="public/img_barrel.png">

<img style="max-width: 50%;" src="public/img_barrel2.jpg">

----

#### Problem: Chromatic Aberration

<img style="max-width: 70%;" src="public/img_chromatic_light.png">

Lens cause different wavelengths of light to have differing focal lengths <!-- .element: class="igalia-text-small" -->

----

#### Solution: Chromatic Aberration pass

##### Compensate Chromatic Aberration

<img style="max-width: 90%;" src="public/img_chromatic.png">

----

#### Problem: Motion to Photon Latency

<img style="max-width: 70%; border: solid 1px" src="public/img_motion.png">

* MTP <=20ms
* Motion sickness & nausea
* 120 Hz, 90 Hz, 72 Hz, 60Hz

----

#### Solution: Optimize Browser + XR Engine Pipeline

Classic render pipeline

<img style="max-width: 95%;" src="public/img_pipeline1.png">

How to optimize it?

----

#### XR Engine Pipeline

Multi-camera

<img style="max-width: 90%;" src="public/img_pipeline2.png">

Easiest option, but the slowest.

----

#### XR Engine Pipeline

Multi-pass

<img style="max-width: 90%;" src="public/img_pipeline3.png">

* Optimized VR render loop
* Extract eye-independent steps

----

#### Multi-pass combined frustrum

<img style="max-width: 65%;" src="public/img_culling.jpg">
<img style="max-width: 60%;" src="public/img_frustrum.png">

----

#### VR Engine Pipeline

Single-pass

<img style="max-width: 95%;" src="public/img_pipeline4.png">

* Multiview: 40% improvements in CPU bound apps
* Submit scene commands ONCE

----

#### WebGL OVR_multiview2 

<img style="max-width: 95%;" src="public/img_multiview.png">

----

#### XR Engine Pipeline: GPU Bound 

Foveated Rendering
<img style="max-width: 95%;" src="public/img_foveated.jpg">

----

### WebXR Foveated Rendering
You can request Foveated Rendering as an optional feature.<!-- .element: class="igalia-text-small" -->

``` js
navigator.xr.requestSession('immersive-vr', {
    requiredFeatures: ['local-floor'],
    optionalFeatures: ['high-fixed-foveation-level']
})
```

----

#### XR Engine Pipeline: Stability 

All this is not enough
<img style="max-width: 95%;" src="public/img_hud.png">

----

#### Asynchronous TimeWarp

<video controls data-autoplay loop style="max-width: 95%;">
	<source src="videos/tw1.mp4" type="video/mp4">                                                                       
</video>

----

<h5>Without frame prediction</h5>
<img style="max-width: 85%;" src="public/img_prediction1.png">
<h5>After implementing frame prediction</h5>
<img style="max-width: 60%; margin-top: 0px" src="public/img_prediction2.png">

----
<!-- .slide: class="igalia-align-left" -->
### Today, we are going to talk about
+ Main challenges behind a WebXR Browser
	+ Rendering
	+ **UI**
	+ Privacy
+ Solution to address each challenge

----

### Redesigned UI for Standalone Browser
<figure class="video_container">
	<video controls data-autoplay loop style="max-width: 70%;">
		<source src="videos/wpe_vr.mp4" type="video/mp4">                                                                      
	</video>
</figure>

----

#### Standalone browser pipeline

<img style="max-width: 90%;" src="public/img_pipeline5.png">

Performance + External ok, but still a quality problem

----

#### Quality problems due to texture sampling

<img style="max-width: 60%;" src="public/img_netflix.jpg">


1. Original quality: 2K or 4K
2. Eye framebuffer size: 1K on Oculus Go (1024 x 1024)
3. Placement on the scene: e.g 512 px
4. Distortion pass: only 512px available to toy with

----

#### Solution: XR Compositor Layers

<img style="max-width: 80%;" src="public/img_pipeline6.png">

* They bypass eye buffers completely
* Unique Texture sample (directly from distortion mesh)
* Resolution: 1024x1024 (eye) vs max 6000x3000 (layers)
* Subpixel precision, oclussion, etc
* No need to scale main eye buffer (e.g. 1.5x with 4x MSAA)


----

#### XR Compositor Layers

<img style="max-width: 35%;" src="public/img_layer_types.png">


* 3D Projection
* Quad
* Cubemap
* Equirectangular
* Cylindrical

----
<!-- .slide: class="igalia-align-left" -->
### Today, we are going to talk about
+ Main challenges behind a WebXR Browser
	+ Rendering
	+ UI
	+ **Privacy**
+ Solution to address each challenge

----
#### Hand Tracking Input

<img style="max-width: 75%;" src="public/img_hand_tracking.jpg">

* Exposes articulated hand poses.
* Risks: fingerprinting, track gender, age, medical problems, etc.
* Solution: Explicit consent and Rounding/noising.

----
#### Eye Tracking

<img style="max-width: 55%;" src="public/img_eye_tracking.png" />


Solutions
* Explicit consent
* Internal use by UA and VR Compositor.

----
#### WebXR: AR & World Understanding

<img style="max-width: 75%;" src="public/img_world_understanding.jpg" />

- New privacy risks: users' physical environment

----

## The End

Any questions?

Ask ifernandez@igalia.com for help!
