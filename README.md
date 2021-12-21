<span align="center">
        <a href="https://huggingface.co/spaces/SajjadAyoubi/CLIPfa-Demo"><img src="https://img.shields.io/static/v1?label=%F0%9F%A4%97%20Hugging%20Face&message=HF Demo&color=blue"></a>
    <a href="https://huggingface.co/SajjadAyoubi/"><img src="https://img.shields.io/static/v1?label=%F0%9F%A4%97%20Hugging%20Face&message=Models&color=red"></a>
</span>

# CLIPfa: Connecting Farsi Text and Images
OpenAI recently released [the paper Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020) in which they present the CLIP (Contrastive Language–Image Pre-training) model. This model is trained to connect text and images, by matching their corresponding vector representations using a contrastive learning objective. CLIP consists of two separate models, a vision encoder and a text encoder. These were trained on a wooping 400 Million images and corresponding captions. In this work we've trained a **Tiny Farsi(Persian)** version of [OpenAI's CLIP](https://openai.com/blog/clip/) on a crawled dataset with 300,000 (image, text) pairs. We used RoBerta-fa for text encoder and Original CLIP's ViT as vision encoder.
![CLIPfa image](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/clipfa.png)
Keep it in mind that, this model was trained for 5 epochs only on 300K pairs whereas the Original CLIP was traind on 4m pairs and The training process took 30 days across 592 V100 GPUs.

## How to use?
You can use these models of the shelf. Both models create vectors with 768 dimention.
```python
from transformers import CLIPVisionModel, RobertaModel, AutoTokenizer, CLIPFeatureExtractor
# download pre-trained models
vision_encoder = CLIPVisionModel.from_pretrained('SajjadAyoubi/clip-fa-vision')
preprocessor = CLIPFeatureExtractor.from_pretrained('SajjadAyoubi/clip-fa-vision')
text_encoder = RobertaModel.from_pretrained('SajjadAyoubi/clip-fa-text')
tokenizer = AutoTokenizer.from_pretrained('SajjadAyoubi/clip-fa-text')
# define input image and input text
text = 'whatever you want'
image = PIL.Image.open(image_path)
# compute embeddings
text_embedding = text_encoder(**tokenizer(text, return_tensors='pt')).pooler_output
image_embedding = vision_encoder(**preprocessor(image, return_tensors='pt')).pooler_output
text_embedding.shape == image_embedding.shape
```

## Demo:
The followings are just some use cases of CLIPfa on 25K [unsplash images](https://github.com/unsplash/datasets)
- use `pip install -q git+https://github.com/sajjjadayobi/clipfa.git`
```python
from clipfa import CLIPDemo
demo = CLIPDemo(vision_encoder, text_encoder, tokenizer)
demo.compute_text_embeddings(['سیب','موز' ,'آلبالو'])
demo.compute_image_embeddings(test_df.image_path.to_list())
```
### Image Search:
```python
demo.image_search(query='غروب خورشید')
```
![](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/image_search.png)

```python
demo.image_search(query='جنگل در زمستان برفی')
```
![](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/forest%20in%20winter.png)

### Analogy: 
```python
demo.anology('sunset.jpg', additional_text='دریا')
```
![](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/analogy-sea.png)

```python
demo.anology('sunset.jpg', additional_text='برف')
```
![](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/analogy-snow.png)



### Zero Shot Image Classification:
```python
demo.zero_shot(image_path='apples.jpg')
```
- Provided labels with their probability for each image.

|                   گاو:36 , ماهی:22, اسب:42                   |                   گاو:41 , ماهی:23, اسب:36                   |                 گاو:26 , ماهی:**45**, اسب:27                 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![image](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/horse.jpg) | ![image](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/cow.jpg) | ![image](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/fish.jpg) |

### Online Demo: [CLIPfa at Huggingface🤗 spaces](https://huggingface.co/spaces/SajjadAyoubi/CLIPfa-Demo)
We used a small set of images to keep this app almost real-time, but it's obvious that the quality of image search depends heavily on the size of the image database. 

![](https://github.com/sajjjadayobi/CLIPfa/blob/main/assets/hf-spaces.png)


## Dataset: 
I was crouse about how much of CLIP's power comes from training on a huge dataset.
400K from filtered and translated (Flicker30K, MS-COCO and CCm3)
- Note: We used [image2ds](https://github.com/rom1504/img2dataset) a great tool to download large scale image datasets such as WiT. It can download, resize and package 100M urls in 20h on one machine. Also supports saving captions for url+caption datasets.


## Training: <a href="https://colab.research.google.com/github/sajjjadayobi/CLIPfa/blob/main/notebook/CLIPfa_Training.ipynb"><img src="https://img.shields.io/static/v1?label=%F0%9F%A4%97%20Hugging%20Face&message=CLIPfa Training&color=white"></a>
Any dataset can be used with little change by the [`training code`](https://github.com/sajjjadayobi/CLIPfa/tree/main/clipfa). CLIPfa can be trained with other encoders as long as they have the same hidden size at the last layer.  In [`this`](https://github.com/sajjjadayobi/CLIPfa/blob/main/notebook/CLIPfa_Training.ipynb) notebook I used [`training code`](https://github.com/sajjjadayobi/CLIPfa/tree/main/clipfa) to train a small CLIP on translated flicker30k dataset.


## Citation: ↩️
If you have a technical question regarding the model, code or publication, create an issue in the repository.
we didn't publish any papers on the work. However, if you did, please cite us properly with an entry like one below.
```bibtex
@misc{ParsBigBird,
  author          = {Ayoubi, Sajjad},
  title           = {CLIPfa: Connecting Farsi Text and Images},
  year            = 2022,
  publisher       = {GitHub},
  journal         = {GitHub repository},
  howpublished    = {\url{https://github.com/SajjjadAyobi/CLIPfa}},
}
```
> Made with ❤️ in my basement🤫
