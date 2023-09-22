# Overview

This package contains the shader I create in my series of YouTue videos. 


# Package Contents

This package contains shaders graphs, textures and a material. All of these assets are located in **`com.simonvs.realtimeblackhole/Assets`**, in separate folders **`Materials`**, **`Shaders`** and **`Textures`**.


# Installation Instructions

The shader was developed using URP. Therefore, it is recommended to use this package with a URP project. It is not tested for HDRP. For instructions on installing unity packages, see the offical [Package Manager installation instructions](https://docs.unity3d.com/Manual/upm-ui-install.html).


# Requirements

This shader is made to be used as a full-screen pass renderer feature. Full-screen pass renderer features were introduced in URP 14, therefore Unity version 2022.2 or newer is required.


# Limitations

There are some limitations with the implementation of the shader. Most importantly, no other objects can be drawn when using the shader as a full-screen effect. This limitation can be circumvented if the black hole is relatively far away compared to everything else in the scene. In that case, you can simply use the shader as a skybox, and ignore the bending of light when rendering the objects in your scene. Secondly, for each light ray, only 3 intersections with the disk plane can be drawn. For most use cases, this is more than sufficient.

# Workflows

To use the shader as a full-screen pass renderer feature, follow these steps:
1. Select the **`URP-[QualitySetting]-Renderer.asset`** file. If you started from the URP preset project, there will be 3 such files in the **`Assets/Settings`** folder: 
	- URP-Balanced-Renderer.asset
	- URP-HighFidelity-Renderer.asset
	- URP-Performant-Renderer.asset
	
	These are the 3 default quality presets that come with URP. You can see which of those presets are in use, depending on the target platform, in the Quality settings (*Edit* **>** *Project Settings* **>** *Quality*).

2. In the Inspector panel, at the bottom, should be a section **Renderer Features**. On the *Balanced* and *HighFidelity* presets, there will be a *Screen Space Ambient Occlusion* renderer feature enabled by default. Since no objects can be drawn when using the shader as a full-screen effect (see Limitations), you can remove this (click the 3 dots in the corner, and then *Remove*).

3. Click **`Add Renderer Feature`** and choose **`Full Screen Pass Renderer Feature`**.

4. Set the Pass Material to the black hole material. **Note:** The black hole shader will also show up in the list of materials. Be sure to choose the material and **not** the shader, otherwise you will be unable to adjust the shader parameters from the default values!


# Advanced Topics

## Anti-Aliasing

The shader benefits a lot from anti-aliasing. There are 2 different places where you can enable anti-aliasing, and they don't work together. The first place is in the **`Universal Render Pipeline Asset`**. If you started from the URP project preset, this should also be in the **`Settings`** folder. Select the **`URP-[QualitySetting].asset`** file (again, if you started from the URP preset, there should be one for Balanced, HighFidelity and Performant). In the instpector panel, under **Quality**, there is *Anti Aliasing*. This should be disabled. If you enable this, unity will use MSAA Anti Aliasing, which doesn't work for shader aliasing. The second place to find anti-aliasing settings is on the Main Camera, under **Rendering**. There are 3 different anti-aliasing algorithms available, any will work. For more information on anti-aliasing, see [this manual page on anti-aliasing](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@14.0/manual/anti-aliasing.html).

## Physics included and missing

This shader implements the following physics accurately:
- Bending of light rays, as described by general relativity, using the Schwarzschild metric.
- Blackbody radiation from the disk based on the temperature distribution in the disk.
- Keplerian rotation of clumps in the disk, although close to the disk this is not a good approximation.
- Doppler shift of emission by the disk, due to the velocity of the gas.
- Gravitational Doppler shift of the emission from the disk, due to the proximity of the gas to the black hole.

The following physics is missing from the shader:
- The Schwartzschild metric describes spacetime around a non-rotating black hole. In reality, however, black holes do rotate. Usually quite rapidly as well. Spacetime around a rotating black hole is described by the Kerr metric, which is not implemented in this shader.
- The light from the environment should be blueshifted due to the proximity of the camera to the black hole. To implement this effect, the skybox would be needed in 3 near-infrared filters, besides the RGB components. Therefore I chose to not implement this physical effect.


# Reference

The shader has the following parameters:

| **Parameter** | **Range** | **Description** |
|---------------|:---------:|-----------------|
**Black Hole**
| Mass                             | >0         | The mass of the black hole. Can be thought of as the size of the black hole. |
**Accretion Disk**
| Temperature Falloff              | 0.5 - 1    | How fast the temperature of the disk falls off with the distance to the black hole. The temperature falls off with `T ~ r^(- Temperature Falloff)`. |
| Mass Accretion                   | >0         | The mass accretion rate controls the temperature of the disk, with `T ~ Mdot^(1/4)`. A higher accretion rate results in a bluer and brighter accretion disk. |
| ISCO Cutoff Smoothness           | 0.01 - 10  | At the inner edge of the accretion disk the temperature drops. This parameter controls the smoothness of this cutoff. |
| Brightness                       | -inf - inf | Controls the brightness of the disk. The emission from the disk is multiplied by 10^(Brightness), so this parameter is in log scale. |
| Temperature Brightness Influence | 0 - 1      | This parameter controls how much the temperature controls the brightness. This is not realistic, but gives more artistic control. For realism, leave this parameter at 1. |
| Outer Radius                     | >0         | The outer radius of the disk. Past this radius, the disk becomes transparent. |
| Outer Radius Falloff             | 0.01 - 1   | The sharpness of the outer edge of the disk. A higher value will result in a sharper falloff, while a smaller value gives a smoother gradient. |
| Beaming Strength                 | 0 - 1      | The strength of the beaming/Doppler effect due to the rotation of the disk. With a value of 1, the inner edge of the disk orbits at the speed of light. |
**Disk Clumps**
| Ring Width                       | >0         | Controls the radial scale of clumps in the disk. |
| Texture Scale                    | >0         | Controls the lateral scale of clumps in the disk. |
| Rotation Speed                   | -inf - inf | Orbital speed of the clumps in the disk. Negative values make the gas orbit in the opposite direction |
| Density Gradient Low             | 0 - 1      | Controls the contrast of the clumps, together with `Density Gradient High`. Should be lower than `Density Gradient High`. |
| Density Gradient High            | 0 - 1      | Controls the contrast of the clumps, together with `Density Gradient Low`. Should be higher than `Density Gradient Low`. |
| Density Range Low                | 0 - 1      | The minimum density of the gas. Should be lower than `Density Range High`. |
| Density Range High               | 0 - 1      | The maximum density of the gas. Should be higher than `Density Range Low`. |
**Environment**
| Cubemap                          |            | Image to use as the environment. The image should have a texture shape of `Cube` (set in the texture settings). |
| Environment Brightness           | >=0         | Brightness of the environment. The environment cubemap is multiplied by this parameter. |