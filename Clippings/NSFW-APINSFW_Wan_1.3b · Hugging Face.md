---
Title: "NSFW-API/NSFW_Wan_1.3b · Hugging Face"
Source: "https://huggingface.co/NSFW-API/NSFW_Wan_1.3b?not-for-all-audiences=true"
ClippedDate: 2025-06-09
Tags:
  - "clippings"
---
## NSFW Wan 1.3B T2V - Uncensored Text-to-Video Model

## Model Description

**NSFW Wan 1.3b T2V** is a powerful text-to-video generation model, with 1.3 billion parameters, specifically fine-tuned for generating Not Safe For Work (NSFW) content. This model has been trained on an extensive and diverse dataset curated from the top 1,000 posts across approximately 1,250 NSFW-focused subreddits.

The primary goal of Wan 2.1 is to provide a research and creative tool capable of generating coherent and thematically relevant short video clips based on text prompts within the adult content domain. It aims to understand and render a wide array array of NSFW scenarios, aesthetics, and actions described in natural language.

## Model Details

- **Architecture:** Wan 2.1 (Text-to-Video Transformer Architecture)
- **Parameters:** 1.3 Billion
- **Type:** Text-to-Video (T2V)
- **Specialization:** NSFW Content Generation

### Important Note on Video Quality & Motion Helper LoRA

The base checkpoint was fine-tuned primarily on images, which means you might observe some deterioration in video quality or inconsistencies in motion in the generated videos. This is an expected characteristic.

**To significantly enhance video quality, motion, and overall style, we strongly recommend using the dedicated `NSFW_Wan_1.3b_motion_helper` LoRA.** This LoRA has been specifically trained to address these aspects and provides excellent results, particularly when used with the `wan_1.3B_e10.safetensors` checkpoint (as it was trained on this epoch).

You can download the motion helper LoRA from:[**NSFW\_Wan\_1.3b\_motion\_helper on Hugging Face**](https://huggingface.co/NSFW-API/NSFW_Wan_1.3b_motion_helper)

This LoRA serves as an effective way to achieve high-quality outputs while further fine-tuning of the main checkpoint on a video dataset is in progress.

## Training Data

The model was trained on a dataset comprising the top 1,000 posts from approximately 1,250 distinct NSFW subreddits. This dataset was carefully curated to capture a broad spectrum of adult themes, visual styles, character archetypes, specific kinks, and actions prevalent in these online communities.

The captions associated with the training data leveraged the language and tagging conventions found within these subreddits. For insights into effective prompting strategies for specific styles or content, please refer to the `prompting-guide.json` file included in this repository.

**Note:** Due to the nature of the source material, the training dataset inherently contains explicit adult content.

## Training Procedure

- **Hardware:** Trained on a cluster of 8x A100 GPUs.
- **Epochs:** 10 epochs.
- **Duration:** Approximately 3 days.
- **Checkpoints:** Model weights are provided for each epoch (`wan_1.3B_e1.safetensors` through `wan_1.3B_e10.safetensors`). This allows users to select the checkpoint that best balances fidelity, generalization, and specific stylistic nuances for their needs. Early epochs might be more creative or varied, while later epochs may show higher fidelity to the training data.

## Files Included

- `wan_1.3B_e1.safetensors`
- `wan_1.3B_e2.safetensors`
- `wan_1.3B_e3.safetensors`
- `wan_1.3B_e4.safetensors`
- `wan_1.3B_e5.safetensors`
- `wan_1.3B_e6.safetensors`
- `wan_1.3B_e7.safetensors`
- `wan_1.3B_e8.safetensors`
- `wan_1.3B_e9.safetensors`
- `wan_1.3B_e10.safetensors`
- `prompting-guide.json`: This crucial JSON file contains an analysis of common keywords, phrases, and descriptive language associated with the content from various source subreddits. It is designed to help users craft more effective prompts by understanding the vocabulary the model was trained on for different niches.

## How to Use

This model is intended for generating short video clips (typically a few seconds) from descriptive text prompts.

1. **Select an Epoch Checkpoint:** Experiment with different `wan_1.3B_e{i}.safetensors` files. While later epochs generally offer more refined results for common themes, `wan_1.3B_e10.safetensors` is specifically recommended if you plan to use the motion helper LoRA.
2. **(Highly Recommended) Apply the Motion Helper LoRA:** For significantly improved video quality, motion, and style, apply the `NSFW_Wan_1.3b_motion_helper` LoRA. This LoRA was trained using the epoch 10 checkpoint.
	- Download it from: [NSFW\_Wan\_1.3b\_motion\_helper on Hugging Face](https://huggingface.co/NSFW-API/NSFW_Wan_1.3b_motion_helper)
	- Ensure it is correctly loaded and applied in your generation pipeline along with the main model checkpoint.
3. **Craft Your Prompt:** Utilize natural language to describe the desired scene, subjects, actions, and style.
4. **Consult `prompting-guide.json`:** For best results, especially when targeting specific sub-community styles or niche fetishes, refer to the `prompting-guide.json`. This guide will provide insights into the terminology and phrasing most likely to elicit the desired output based on the training data's captioning patterns.
5. **Generate:** Use your preferred inference pipeline compatible with this model architecture.

## Ideal Base for LoRA Fine-Tuning

While Wan 2.1 1.3B T2V is a capable NSFW model on its own, its true strength for many users lies in its efficacy as a **foundational base for training specialized LoRAs (Low-Rank Adaptations)**.

The extensive NSFW training provides a robust understanding of:

- **Core NSFW Anatomy:** It already has a strong grasp of how to depict features like a penis, vagina, breasts, etc.
- **Common Sexual Acts:** Concepts like blowjobs, masturbation, various sexual positions, and basic interactions are part of its foundational knowledge.
- **General NSFW Aesthetics:** It understands common lighting, settings, and visual cues within adult content.

This means you don't need to teach your LoRA these fundamental NSFW building blocks from scratch. Instead, you can **focus your LoRA training dataset exclusively on the specific niche concept, character, artistic style, unique action, specific motion, or specialized terminology** you want to master. NSFW Wan will effectively "fill in the rest," leveraging its broad NSFW foundation to complement your targeted LoRA training. This can lead to more efficient LoRA training and better results for highly specific NSFW content generation.

## Community & Support

**Join our Discord server!**

Connect with other users, share your creations, get help with prompting, discuss model updates, and contribute to the community:

[**https://discord.gg/mjnStFuCYh**](https://discord.gg/mjnStFuCYh)

We encourage active participation and feedback to help improve future iterations and resources!

## Limitations and Bias

- **NSFW Focus:** The model's knowledge is heavily biased towards the content prevalent in the NSFW subreddits it was trained on. It will likely perform poorly on SFW (Safe For Work) prompts or concepts far removed from its training data.
- **Specificity & Artifacts:** While trained for detail, the model may still produce visual artifacts, anatomical inaccuracies, or fail to perfectly capture highly complex or nuanced prompts, especially without the recommended motion helper LoRA. Video generation is an evolving field.
- **Bias:** The training data reflects the content, biases, preferences, and potentially problematic depictions present in the source NSFW communities. The model may generate content that perpetuates these biases.
- **Safety:** This model does not have built-in safety filters to prevent the generation of potentially harmful or offensive interpretations of NSFW content, beyond the scope of its training data. Users are responsible for the ethical application of the model.
- **Temporal Coherence:** While a T2V model, very long or complex actions might still exhibit some temporal inconsistencies. Using the `NSFW_Wan_1.3b_motion_helper` LoRA can improve this.

## Ethical Considerations & Responsible AI

**This model is intended for adult users (18+/21+ depending on local regulations) only.**

- **Consent and Harm:** This model generates fictional, synthetic media. It **must not** be used to create non-consensual depictions of real individuals, to impersonate, defame, harass, or generate content that could cause harm.
- **Legal Use:** Users are solely responsible for ensuring that their use of this model and the content they generate complies with all applicable local, national, and international laws and regulations.
- **Distribution:** Exercise extreme caution and responsibility if distributing content generated by this model. Be mindful of platform terms of service and legal restrictions regarding adult content.
- **No Endorsement:** The creators of this model do not endorse or condone the creation or distribution of illegal, unethical, or harmful content.

We strongly recommend users familiarize themselves with responsible AI practices and the potential societal impacts of generative NSFW media.

## License

Steal this model!

## Disclaimer

The outputs of this model are entirely synthetic and computer-generated. They do not depict real people or events unless explicitly prompted to do so with user-provided data (which is not the intended use of this pre-trained model). The developers of this model are not responsible for the outputs created by users.

Downloads last month

\-

Downloads are not tracked for this model.[How to track](https://huggingface.co/docs/hub/models-download-stats)

Inference Providers [NEW](https://huggingface.co/docs/inference-providers)

## Model tree for NSFW-API/NSFW\_Wan\_1.3b

Adapters

[1 model](https://huggingface.co/models?other=base_model:adapter:NSFW-API/NSFW_Wan_1.3b)

Finetunes

[1 model](https://huggingface.co/models?other=base_model:finetune:NSFW-API/NSFW_Wan_1.3b)