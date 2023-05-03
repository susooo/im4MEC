###  이해하기
#### A. WSI pre-processing

#### B. SSL pre-training
```
cat <svs 절대경로 리스트> | xargs -I WSIFILE echo python sample_tiles.py --input_slide=WSIFILE --output_dir=<저장경로> --tile_size=360 --n=2048 --out_size=224 | bash
```
output
* <저장경로>/train : 각 WSI(Whole Slide Image)의 crop된 타일 이미지 저장
* <저장경로>/QC : 각 WSI에 대해 하나의 품질 관리(QC) 이미지 저장
<br>
[sample_tiles.py] <br>
<br>
from preprocess import ( <br>
    create_tissue_mask, #  <br>
    create_tissue_tiles, #  <br>
    crop_rect_from_slide, #  <br>
    make_tile_QC_fig, #  <br>
    tile_is_not_empty, #  <br>
) <br>



<br>

#### C. Tile feature extraction
MOCO-V2
* 기본 인코더 : 이미지의 고차원적 특성을 추출하는 역할, resnet 50
* 프로젝션 헤드 : 인코더에서 추출한 고차원적 특성벡터를 입력받아 저차원 특성공간(모델간의 유사성 측정하는데 사용)으로 매핑하는 역할

인코더
* 쿼리 인코더(Q) ; 주요 네트워크
* 키 인코더(K) : 쿼리 인코더의 가중치를 모멘텀으로 업데이트하는데 사용 <br>


```
python3 moco/main_moco.py \
<저장경로> \
--lr 0.06 \
--warmup 10 \
--epochs 300 \
--cos \
--batch-size <32*GPU 개수> \
--arch resnet50 \
--moco-k <batch-size*256> \
--moco-t 0.07 \
--mlp \
--dist-url 'tcp://localhost:10001' \
--multiprocessing-distributed \
--world-size 1 \
--rank 0 \
--gpu <사용할 GPU 개수>
```

[main_moco.py]

* def main() : 
* def main_worker() :
* def train() :
* def save_checkpoint() :
* 

<br>

#### D. Attention based slide classification
① converting each WSI into a bag of feature vectors
각 타일의 특징 추출 모델(checkpoint)를 실행하여 특징벡터를 얻는다.
② prepare dataset
③ training
[preprocess.py]

* def segment_tissue() :
* def detect_foreground() :
* def construct_polygon() :
* def generate_tiles() :
* def make_tile_QC_fig() :
* def create_tissue_mask() :
* def create_tissue_tiles() :
* def tile_is_not_empty() :
* def crop_rect_from_slide() :

[train.py]


<br>

#### E. Interpretability

[attention.py]
