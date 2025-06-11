# Image Set Describer
Image Set Describer builds upon [VisDiff](https://arxiv.org/abs/2312.02974), an automatic procedure to describe the differences between two sets of images in natural language. Basically, given two sets of images, we want to receive a description in natural language that is more true for the first set than for the second set.

## 🚀 How to Use
The project is entirely self contained in the jupyter notebook. It can be uploaded to e.g. [Colab](https://colab.research.google.com/) and run.

The only requirements are:
- Having an [Hugging Face](https://huggingface.co) (HF) account
- Requesting access to the LLM on HF ([gemma 2](https://huggingface.co/google/gemma-2-9b-it) in our case)
- Having an HF user access token (see instruction in the notebook)

## 🔄 Pipeline
The pipeline has 4 main steps:

1. Panoptic segmentation: Use a segmentation model (Detectron2) to assign both a semantic label (like "person," "car," or "grass") and a unique instance ID (for individual objects like a specific person or car) to each pixel. It basically combines semantic and instance segmentation.

2. Image captioning: Use a captioning model (BLIP-2) to caption each image inside the the two sets of images, so that we end up with two sets of captions. Since panoptic segmentation was performed beforehand, the resulting captions will be more detailed than directly captioning the images.

3. Proposing differences: Prompt an LLM (GPT-4) to propose the differences in the two sets of captions, i.e. concepts that are more true for the first set than for the second set.

4. Ranking differences: Use a VLM (CLIP) to compare the similarity between the proposed differences and the two sets of images. Rank the proposed differences based on this similarity.

5. Evaluation: Prompt an LLM (GPT-4) to score the similarity between proposed differences and the ground truth. The scores can be: 0 (no match), 0.5 (partial match) or 1 (complete match). Measure Acc@k, which is the highest score of any of the top-k
proposals.
