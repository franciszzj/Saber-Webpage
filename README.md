# Saber: Scaling Zero-Shot Reference-to-Video Generation

Zijian Zhou<sup>1,2</sup>, Shikun Liu<sup>1</sup>, Haozhe Liu<sup>1</sup>, Haonan Qiu<sup>1</sup>, Zhaochong An<sup>1</sup>, Weiming Ren<sup>1</sup>, Zhiheng Liu<sup>1</sup>, Xiaoke Huang<sup>1</sup>, Kam Woh Ng<sup>1</sup>, Tian Xie<sup>1</sup>, Xiao Han<sup>1</sup>, Yuren Cong<sup>1</sup>, Hang Li<sup>1</sup>, Chuyan Zhu<sup>1</sup>, Aditya Patel<sup>1</sup>, Tao Xiang<sup>1</sup>, Sen He<sup>1</sup>

<sup>1</sup> Meta AI &nbsp;&nbsp;&nbsp; <sup>2</sup> King's College London

[![Paper](https://img.shields.io/badge/Paper-arXiv-red)](https://arxiv.org)
[![Code](https://img.shields.io/badge/Code-GitHub-blue)](https://github.com/franciszzj/Saber)

## Project Overview

**Saber** is a scalable zero-shot framework for reference-to-video (R2V) generation. By introducing a masked training strategy, Saber bypasses the bottleneck of explicit reference image-video-text triplet datasets, training exclusively on video-text pairs to achieve zero-shot generation capabilities without explicit R2V data.

### Teaser Video

<video width="100%" controls>
  <source src="assets/videos/Saber.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

*Click the video above or [watch the full teaser video](assets/videos/Saber.mp4) to see Saber in action.*

## Key Features

- **Zero-Shot Learning**: No explicit reference image-video-text triplet datasets required, training only on video-text pairs
- **Masked Training Strategy**: Uses randomly sampled and partially masked video frames as references, compelling the model to learn identity-consistent representations
- **Attention Mechanism**: A tailored attention mechanism guided by attention masks directs the model to focus on reference-aware features while suppressing background noise
- **Spatial Mask Augmentations**: Further mitigates copy-paste artifacts common in reference-to-video generation
- **Multi-Reference Image Support**: Naturally supports a varying number of reference images and multiple views of the same subject, enabling richer multi-subject customization

## Method Overview

The core idea of Saber is to simulate the R2V task by replacing explicitly collected reference images with **randomly masked frames** during training. This approach consists of two key components:

1. **Mask Augmentations**: Designed to mitigate copy-paste artifacts
2. **Tailored Attention Mechanism**: Guides the model to focus on relevant reference features

### Masked Frames as Reference

Instead of relying on pre-collected reference images with limited subject diversity, we use randomly masked frames as dynamic substitutes during training. This strategy naturally introduces highly diverse reference samples, allowing the model to learn more effective subject integration and achieve stronger generalization.

![Masked Frames as Reference](assets/figures/mask_frames.png)

*Masked reference generation. Given a video, the mask generator produces diverse random masks (ellipse, Fourier blob, polygon, etc.), which are then applied to randomly sampled video frames with spatial augmentations (rotation, scaling, shear, translation, flip).*

### Model Design

We adopt a simple yet effective architecture that concatenates reference images along the temporal dimension at the end of target video frames in latent space. The model manages interaction between target video latents and reference latents through a tailored attention mechanism in each transformer block.

![Model Design](assets/figures/model_design.png)

*Model design overview. Masked frames serve as reference images and are concatenated to the video tokens in latent space. Self-attention enables interaction between video and reference tokens under the attention mask, while cross-attention incorporates text guidance for semantic alignment.*

## Experimental Results

On the **OpenS2V-Eval** benchmark, Saber consistently outperforms models explicitly trained on R2V data, demonstrating strong zero-shot generalization and scalability.

### Qualitative Comparisons

We conduct qualitative comparisons between Saber and other methods (Kling1.6, Phantom, VACE) across various visual scenarios, demonstrating superior subject preservation and video quality.

#### Example 1: Single Human Image

| Reference Image | Text Prompt | Kling1.6 | Phantom | VACE | Saber (Ours) |
|----------------|-------------|----------|---------|------|--------------|
| <img src="assets/videos/4_Qualitative_Comparison_Videos/1_reference_image_1.png" width="200"> | The video features a man with a rugged beard, wearing a leather jacket, riding a vintage motorcycle along a desert highway. His expression is focused, eyes narrowed slightly against the wind, as the setting sun casts a warm glow over the landscape. | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/1_generated_video_kling.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/1_generated_video_phantom.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/1_generated_video_vace.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/1_generated_video_saber.mp4" type="video/mp4"></video> |

#### Example 2: Multiple Human Images

| Reference Images | Text Prompt | Kling1.6 | Phantom | VACE | Saber (Ours) |
|-----------------|-------------|----------|---------|------|--------------|
| <img src="assets/videos/4_Qualitative_Comparison_Videos/2_reference_image_1.jpg" width="100"> <img src="assets/videos/4_Qualitative_Comparison_Videos/2_reference_image_2.jpg" width="100"> <img src="assets/videos/4_Qualitative_Comparison_Videos/2_reference_image_3.jpg" width="100"> | A woman with long, flowing black hair pulled back into a ponytail dances gracefully in a sunlit meadow. She is wearing a flowing dress that billows gently around her as she moves. The scene captures her mid-twirl, with the fabric of her dress swirling elegantly. | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/2_generated_video_kling.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/2_generated_video_phantom.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/2_generated_video_vace.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/2_generated_video_saber.mp4" type="video/mp4"></video> |

#### Example 3: Single Object Image

| Reference Image | Text Prompt | Kling1.6 | Phantom | VACE | Saber (Ours) |
|----------------|-------------|----------|---------|------|--------------|
| <img src="assets/videos/4_Qualitative_Comparison_Videos/3_reference_image_1.jpg" width="200"> | This video shows a peaceful morning scene at a cozy farmhouse. The camera slowly pans across the exterior, revealing the rustic charm of the wooden structure. As the camera moves, a dog is seen lying near the entrance, its tail wagging gently. The soft morning light creates long shadows, adding to the tranquil atmosphere. | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/3_generated_video_kling.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/3_generated_video_phantom.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/3_generated_video_vace.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/3_generated_video_saber.mp4" type="video/mp4"></video> |

#### Example 4: Multiple Object Images

| Reference Images | Text Prompt | Kling1.6 | Phantom | VACE | Saber (Ours) |
|-----------------|-------------|----------|---------|------|--------------|
| <img src="assets/videos/4_Qualitative_Comparison_Videos/4_reference_image_1.jpg" width="100"> <img src="assets/videos/4_Qualitative_Comparison_Videos/4_reference_image_2.jpg" width="100"> <img src="assets/videos/4_Qualitative_Comparison_Videos/4_reference_image_3.jpg" width="100"> | The video shows a table setting with a floral cup, an ashtray and an open book, as a stream of tea is slowly poured into the cup. | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/4_generated_video_kling.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/4_generated_video_phantom.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/4_generated_video_vace.mp4" type="video/mp4"></video> | <video width="200" controls><source src="assets/videos/4_Qualitative_Comparison_Videos/4_generated_video_saber.mp4" type="video/mp4"></video> |

### More Visualization Examples

For more visualization examples, including:
- **Teaser Videos**: [View Teaser Videos](assets/videos/1_Teaser_Videos/)
- **More Mask Examples**: [View Mask Examples](assets/videos/2_More_Mask_Examples/)
- **Effect of Mask Augmentation**: [View Videos](assets/videos/5_Effect_of_the_Mask_Augmentation_Videos/)
- **Effect of Attention Mask**: [View Videos](assets/videos/6_Effect_of_the_Attention_Mask_Videos/)
- **Same Subject Multi-View**: [View Videos](assets/videos/7_Same_Subject_Multi_View_Videos/)
- **Cross-Modal Alignment**: [View Videos](assets/videos/8_Cross_Modal_Alignment_Videos/)
- **More Visualization Examples**: [View All Examples](assets/videos/X_More_Vis_Examples/)

Please visit our [project website](index.html) for the complete interactive visualization experience.

### Emergent Abilities

Beyond the standard R2V task, Saber demonstrates several interesting capabilities that emerged from its training strategy, showcasing remarkable robustness and flexibility.

#### Single Subject Multiple Views

Saber can handle multiple reference images corresponding to different views of the same subject.

| Reference Images | Text Prompt | Generated Video |
|-----------------|-------------|-----------------|
| <img src="assets/videos/7_Same_Subject_Multi_View_Videos/1_reference_image_1.png" width="150"> <img src="assets/videos/7_Same_Subject_Multi_View_Videos/1_reference_image_2.png" width="150"> <img src="assets/videos/7_Same_Subject_Multi_View_Videos/1_reference_image_3.png" width="150"> | The video opens with a slow, cinematic rotation around a robot, its body gleaming with a fusion of transparent panels and brushed metal, circuits pulsing faintly beneath the surface like living veins of light. As the rotation completes, the scene flows into motion, following the robot as it strides along lush green country lanes. | <video width="400" controls><source src="assets/videos/7_Same_Subject_Multi_View_Videos/1_generated_video.mp4" type="video/mp4"></video> |

#### Cross-Modal Alignment

Saber demonstrates strong cross-modal alignment capabilities, allowing fine-grained control over generated content through text prompts.

| Reference Images | Text Prompt | Type 1: Blue Shirt | Type 2: Black Vest |
|-----------------|-------------|-------------------|-------------------|
| <img src="assets/videos/8_Cross_Modal_Alignment_Videos/1_reference_image_1.jpg" width="150"> <img src="assets/videos/8_Cross_Modal_Alignment_Videos/1_reference_image_2.jpg" width="150"> | The video depicts two men in an office environment. The focus is on a man wearing a [Type 1: blue shirt] / [Type 2: black vest] seated at a desk, working intently on a laptop. | <video width="300" controls><source src="assets/videos/8_Cross_Modal_Alignment_Videos/1_generated_video_type_1.mp4" type="video/mp4"></video> | <video width="300" controls><source src="assets/videos/8_Cross_Modal_Alignment_Videos/1_generated_video_type_2.mp4" type="video/mp4"></video> |

## Citation

If you find Saber useful for your research, please cite our paper:

```bibtex
@article{zhou2025scaling,
    title={Scaling Zero-Shot Reference-to-Video Generation},
    author={Zhou, Zijian and Liu, Shikun and Liu, Haozhe and Qiu, Haonan and An, Zhaochong and Ren, Weiming and Liu, Zhiheng and Huang, Xiaoke and Ng, Kam Woh and Xie, Tian and Han, Xiao and Cong, Yuren and Li, Hang and Zhu, Chuyan and Patel, Aditya and Xiang, Tao and He, Sen},
    journal={arXiv preprint},
    year={2025}
}
```
