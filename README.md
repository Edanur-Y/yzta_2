![pipeline](https://raw.githubusercontent.com/Edanur-Y/yzta_2/refs/heads/main/pipeline.png)
PostgreSQL’e geçiş gerekli. `pgvector` extension   
User id partitioned user database

# **Kıyafet Segmentation ve Etiketleme:**

## OLLAMA:
* [Anyesh/wardrowbe](https://github.com/Anyesh/wardrowbe)

## Diğer seçenekler:

### Preprocessing:
* from PIL  
  * EXIF orientation  
  * Resize to (1024,1024)  
    * Görsel kalitesini korumak için Image.LANCZOS kullanılmalı.  
  * Normalize image: standard ImageNet normalization

### Segmentation:

Model; padded 1024x1024 görsele mask çıktısı verdikten sonra görseli, orjinal görsel koordinatlarına geri map etmek gerekiyor ki paddingler gözükmesin.

* [BiRefNet](https://huggingface.co/ZhengPeng7/BiRefNet)  
  * Kıyafet için örnek uygulama:  
    [PluckIt.Segmentation.Modal](http://github.com/AB-Law/Pluck-It/tree/35d85b1f9625236cf0ecb713b7409d7e93850919/PluckIt.Segmentation.Modal)  
* [SAM2](https://huggingface.co/docs/transformers/model_doc/sam2)  
  * Kıyafet için örnek uygulamalar:  
    * [Grounded-SAM-2-clothes-extraction](https://github.com/vladoverx/Grounded-SAM-2-clothes-extraction/blob/main/GroundingDINO_%26_SAM_2_for_Clothes_Extraction.ipynb) (+ [Grounding DINO](https://huggingface.co/docs/transformers/model_doc/grounding-dino))  
    * [Garment-Extraction-SAM-2](https://github.com/rakeshsahni/Garment-Extraction-SAM-2)

###  Etiketleme:

* [Fashion CLIP](https://huggingface.co/patrickjohncyh/fashion-clip) \- big community(az sorun), trained on studio product shots  
  * Örnek uygulama:  
    [outfit-transformer](https://github.com/bigohofone/outfit-transformer/blob/main/src/models/outfit_clip_transformer.py)  
  * embedding vector(N) \= 512  
* [Marqo-FashionCLIP](https://huggingface.co/Marqo/marqo-fashionCLIP) \- trained on broader/undisclosed data, reports outperforming fashion clip  
  * embedding vector(N) \= 512  
* [Marqo-FashionSigLIP](https://huggingface.co/Marqo/marqo-fashionSigLIP) \- Sigmoid loss model, reports outperforming marqo-fashion-clip  
  * Load via OpenCLIP to match the exact preprocessing the models were benchmarked with  
  * embedding vector(N) \= 768

* Optional add-on: [Florence-2-base](https://huggingface.co/microsoft/Florence-2-base)  
  * Describe/locate/read what's in this image through a prompt-based approach

➥Model prediction confidence ile kullanıcı geri dönüşü isteme ve model doğruluğu takibi.


# **Kombin Önerisi:**

## OLLAMA:
* [Anyesh/wardrowbe](https://github.com/Anyesh/wardrowbe)

## Deneysel seçenekler:

* [fashion-compatibility](https://github.com/mvasil/fashion-compatibility/tree/master) (2018)  
  \[ResNet/SiameseNet, Fisher-vec encoding of word2vec \]  
    
  * [fashion-compatibility \+](https://github.com/zuoxiang95/fashion-compatibility) (2020) (og \+ kıyafet masking ve margin ranking loss)  
      
* [Outfit-transformer](https://github.com/bigohofone/outfit-transformer/tree/main) (2026) (Fashion CLIP dahil)   
  \[ResNet-18 initialized with ImageNet pre-trained weights, pretrained SentenceBERT\]  
    
* [Outfit-compatibility-scoring](https://github.com/sakshamarora97/outfit-compatibility-scoring) (2023)  
  \[HGNN, NGNN\]

➥Kullanım sıklığı vs. değerlerinin dahil edilmesi gerekiyor.


# **Kombin Segmentation ve Etiketleme:**

## Kıyafet takibi için uygun seçimler:

* [segformer\_b2\_clothes](https://huggingface.co/mattmdjaga/segformer_b2_clothes) \- low efor, light model, big community(az sorun)


  * Preprocessing:  
    * Standard ImageNet normalization \- training kodun içinde (inputs)  
    * Upsampling before taking the argmax to get the final per-pixel labels. \- training kodun içinde (upsampled\_logits)  
    * [preprocessor\_config.json](https://huggingface.co/mattmdjaga/segformer_b2_clothes/blob/main/preprocessor_config.json)  
      * size: 512  
      * Output kalitesi: resample:2 \- Good general-purpose default, smooth

* [fashn-ai/fashn-human-parser](https://huggingface.co/fashn-ai/fashn-human-parser) \- low efor, light-medium model \[segformer-B4\]  
  * Demo: [spaces-fashn-human-parser](https://huggingface.co/spaces/fashn-ai/fashn-human-parser)  
      
  * Preprocessing:  
    * [preprocessor\_config.json](https://huggingface.co/fashn-ai/fashn-human-parser/blob/main/preprocessor_config.json)  
      * size: 384×576   
          
      * Output kalitesi:

        The boundary pixels: A garment's edge against skin or background is a hard, high-contrast transition.

        

        **pip package:** exact preprocessing used during training for maximum accuracy

        * BGR to RGB  
        * Resize algorithm:  cv2.INTER\_AREA  
        * can slightly soften and shift where that boundary visually sits.

        **The HuggingFace Hub:** broader compatibility, can result in slightly different outputs

        * Resize algorithm: resample:2 \- High-quality downscaling, sharper edges  
        * can introduce a thin band of slightly wrong-colored pixels right at the boundary.

## Diğer seçenekler:

* [SAM3](https://huggingface.co/facebook/sam3) (Prompt base çalışabiliyor.) \- gpu heavy  
* [Self-Correction-Human-Parsing](https://github.com/GoGoDuck912/Self-Correction-Human-Parsing) \- mid efor, light-medium model \[ResNet-based\]

➥Bu modellerin çıktılarının, kıyafet etiketleriyle uyuşması gerekiyor ki eşleşme yapılabilsin.


# **Kombin ve Kayıtlı Kıyafetleri Eşleştirme:**

## Cosine Similarity Score örneği:

* [Garment-similarity-search](https://huggingface.co/spaces/sedovsek/garment-similarity-search/tree/main?utm_source=galjot.si&utm_medium=blog) (2026) 

  * [Documentation](https://galjot.si/visual-similarity-search)  
  * [Demo](https://huggingface.co/spaces/sedovsek/garment-similarity-search) (sadece elbise)  


  **Örnek pipeline:**
    * Object detection (classification & localization):   
      * [Florence-2-base](https://huggingface.co/microsoft/Florence-2-base)  
    * Segmentation and background removal:  
      * [SAM2](https://huggingface.co/docs/transformers/model_doc/sam2)  
    * Embedding:  
      * [Fashion CLIP](https://huggingface.co/patrickjohncyh/fashion-clip)
    * Search:  
      * [NumPy's dot product](https://numpy.org/doc/stable/reference/generated/numpy.dot.html)

## Nearest Neighbor Search örneği:

* [Fashion-recommender-cv](https://github.com/eyereece/fashion-recommender-cv) (2023)  
  * [Documentation](https://www.joankusuma.com/post/smart-stylist-a-fashion-recommender-system-powered-by-computer-vision)

  **Pipeline:**
    * Object Detection Model:  
      * [YOLOv5](https://pytorch.org/hub/ultralytics_yolov5/)  
    * Feature Extraction:  
      * Convolutional AutoEncoder implemented with PyTorch  
    * Similarity Search Index


* [Fine-tuning ViT on clothing dataset](https://colab.research.google.com/drive/17ftAEdu7diEavKGl5DbYFPFvuGKWp_2X#scrollTo=bNg6P3M3AQLy)  
  * [Documentation](https://towardsdatascience.com/similarity-learning-for-clothing-building-a-web-service-from-scratch-350216830e21/)  
      
* [fashion-recommendation](https://github.com/khanhnamle1994/fashion-recommendation/tree/master) (2018)


## Approximate Nearest Neighbor örneği (Milvus \- HNSW index)

* [Vector Similarity Search and Fashion](https://zilliz.com/learn/vector-similarity-search-and-fashion) (Method) (2021)

## Öklid uzaklığı örneği:

* [VisualFashionAttributePrediction](https://github.com/malteprinzler/VisualFashionAttributePrediction/blob/master/notebooks/product_matching.ipynb) (2020)

## Contrastive Loss örneği:

* [fashion-similarity-model](https://github.com/ssabrut/fashion-similarity-model) (2023)

## HackUDC 2026:

Inditex modellerinin üstündeki kıyafetlerin hangi ürünler olduğunu bulma

* [product-matching-hackudc-inditex](https://github.com/usbt0p/product-matching-hackudc-inditex/blob/main/docs/README_EN.md)
   
  **Pipeline:**  
  * [YOLOv8](https://docs.ultralytics.com/models/yolov8#yolov8-usage-examples) & [Grounding DINO](https://huggingface.co/docs/transformers/model_doc/grounding-dino):
    * YOLOv8 for segmentation(cropping), improves embedding precision      
    * Grounding DINO with a text prompt identifies body regions and allows assigning a semantic zone to each YOLO crop by intersection (IoU).  
  * [GR-Lite](https://huggingface.co/srpone/gr-lite) (DINOv3 fine-tuned for fashion)  
    * This embedding was chosen over CLIP. Captures texture, cut, color, and style with much more precision.  
      ➥ Stüdyo fotoğraflarında performansı iyi olsa da StreetLook için performansı diğer seçenekler kadar iyi değil. [LookBench](https://serendipityoneinc.github.io/look-bench-page/)  
  * <ins>DomainMapper:</ins>  
    * Instead of fine tuning GR-Lite, trained a mapper. It is a two-layer network (\~4M parameters) that trains in minutes and projects bundle embeddings into the catalog embedding space.  
    * Contains the mapper architecture (residual \+ learnable temperature), the loss functions (InfoNCE with Hard Negative Mining, and the Cross-Batch Memory), and the full training loop with MixUp, SWA, and OneCycleLR.  
  * <ins>Temporal Proximity Weighting:</ins>  
    * Ürün koleksiyonun yüklenme tarihi yakınsa ödül, uzaksa ceza ile tahmin desteği  
      ➥Sık kullanılan ve yeni alınan kıyafetler için benzer bir yöntem kullanılabilir.  
  * <ins>Semantic Filtering:</ins>  
    * Soft filter (× 0.7): if the product and bundle belong to different sections  
    * Hard filter (× 0.0): if the body zone of the crop directly contradicts the body zone of the product

* [hackUDC26\_reto\_inditex](https://github.com/mtaibo/hackUDC26_reto_inditex)

  **Pipeline:**

  * Open CLIP (ViT-B-32) fine-tuned for embedding  
  * [FAISS](https://github.com/facebookresearch/faiss) for Vectoral search

## Deneysel örnekler:

(duruma daha uygun veri setleri)

* [Street-to-shop visual search](https://www.griddynamics.com/blog/street-to-shop-future-of-fashion-is-visual-search) (Method) (2021)  
  * Data: street2shop (noisy veri fakat duruma uygun)

* [street\_to\_shop\_experiments](https://github.com/movchan74/street_to_shop_experiments) (üsttekine yakın bir uygulama) (2017)  
  * Data: Aliexpress, user-seller data (noisy veri fakat duruma uygun)  
  * [Documentation](https://blog.mlreview.com/how-to-apply-distance-metric-learning-for-street-to-shop-problem-d21247723d2a)


# Diğer:

* [fashn-ai](https://huggingface.co/fashn-ai) (2026)  
  * human parsing models, virtual try-on systems  
* [mmfashion](https://github.com/open-mmlab/mmfashion) (2020)  
  * Attribute Prediction, Recognition and Retrieval, Parsing and Segmentation, Compatibility and Recommendation, Virtual Try-On
