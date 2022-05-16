
## I. **RESPIRENET: A DEEP NEURAL NETWORK FOR ACCURATELY DETECTING ABNORMAL LUNG SOUNDS IN LIMITED DATA SETTING**

## 초록

- 원문

    Auscultation(청진법) of respiratory sounds is the primary tool for screening and diagnosing lung diseases. Automated analysis, coupled with digital stethoscopes(청진기), can play a crucial
    role in enabling tele-screening(원격 선별) of fatal lung diseases. Deep neural networks (DNNs) have shown a lot of promise(가능성) for such problems, and are an obvious choice. However, DNNs
    are extremely data hungry, and the largest respiratory dataset ICBHI has only 6898 breathing cycles, which is still small for training a satisfactory DNN model. In this work,
    RespireNet, we propose a simple CNN-based model, along with a suite of(한 벌의) novel(새로운) techniques—device specific fine-tuning, concatenation(연속)-based augmentation, blank region clipping,
    and smart padding—enabling us to efficiently use the small-sized dataset. We perform extensive(폭넓은) evaluation on the ICBHI dataset, and improve upon the state-of-the-art results for 4-class classification by 2.2%.

    Index Terms(색인 용어) — Abnormality detection(이상 탐지), lung sounds, crackle(수포음) and wheeze(천명음), ICBHI dataset, deep learning

- 데이터 증강 방법: device specific fine-tuning, concatenation(연속)-based augmentation, blank region clipping, smart padding

- 요약

    - 의료 데이터는 구하기 어렵다. 
    - DNN도 성능이 좋을 것 같지만, 훈련시키려면 데이터가 부족하다. 
      - 왜? Deep Neural Netwark는 훈련시키는 데에 많은 데이터가 필요하기 때문

    ⇒ 본 논문에서는 심플한 CNN을 베이스로 한 모델인 `RespireNet`를 제안


### RespireNet Framework

![image](https://user-images.githubusercontent.com/67695343/166189313-a54288ff-39d8-407c-8937-d7da21e7e4b8.png)

> 요약<br>
사운드 신호 전처리(bandpass filtering, downsampling, normalization, etc., ...) → concatenation-based 증강 → smart padding → mel-spectrogram 생성 → blank region clipping → 처리된 파형이미지를 모델에 넣음 → 모델 훈련 1단계: 훈련 세트 전체를 이용 → 2단계: 파인튜닝, 데이터중 각각의 장비에 맞는 부분만 사용하여 훈련!

## CONCLUSION AND FUTURE WORK

- 원문

    The paper proposes RespireNet a simple CNN-based model, along with a set of novel techniques—device specific fine-tuning, concatenation-based augmentation, blank region clipping, and smart padding—enabling us to effectively utilize a small-sized dataset for accurate abnormality detection in lung sounds. Our proposed method achieved a new SOTA for the ICBHI dataset, on both the 2-class and 4-class classification tasks. Further, our proposed techniques are orthogonal(직각, 직교의) to the choice of network architecture and should be easy to incorporate(포함하다) within other frameworks. The current performance limit of the 4-class classification task can be mainly attributed to(~의 덕분이다) the small size of the ICBHI dataset, and the variation among the recording devices. Furthermore, there is lack of standardization in the 80-20 split and we found variance(변화(량)) in the results based on the particular split. In future, **we would recommend that the community should focus on capturing a larger dataset, while taking care of the issues raised in this paper.**

- 요약

    ### 성과 요인

![image](https://user-images.githubusercontent.com/67695343/166189777-b866bdb8-fda4-43cb-a3dd-f88ee07eda27.png)


1. 작은 양의 ICBHI 데이터셋, 그리고 녹음기들 사이의 다양화
2. 특정한 split 비율에 근거하여 결과값에 variance(변화(량))를 찾음
not 80-20 split! → 80-20은 표준화(standardization)하기엔 부족

concatenation-based augmentation (CBA), blank region clipping (BRC) and device specific fine-tuning (FT)

3. **이 논문에서 제기된 이슈 주의, 데이터셋의 양을 늘리는 거 자체에 집중하기를 추천**

## (1) 데이터 증강방법에 관한 부분

- 원문

    **2. METHOD**
    *Dataset*: We perform all evaluations on the ICBHI scientific challenge respiratory sound dataset. It is one of the largest publicly available respiratory datasets. The dataset comprises(구성되다) of 920 recordings from 126 patients with a combined total duration of 5.5 hours. Each breathing cycle in a recording is annotated by an expert as one of the four classes: normal, crackle, wheeze, or both (crackle and wheeze). The dataset comprises of recordings from four different devices from hospitals in Portugal and Greece. For every patient, data was recorded at seven different body locations.

    *Pre-processing*: The sampling rate of recordings in the dataset varies from 4 kHz to 44.1 kHz. To standardize, we down-sample the recordings to 4 kHz and apply a 5-th order Butterworth band-pass filter to remove noise (heartbeat, background speech, etc.). We also apply standard normalization on the input signal to map the values within the range (-1, 1). The audio signal is then converted into a Mel-spectrogram, which is fed into our DNN.

    *Network architecture*: We use a CNN-based network, ResNet34, followed by two 128-d fully connected linear layers with ReLU activations. The last layer applies softmax activation to model classwise(클래스범위) probabilities. Dropout is added to the fully connected layers to prevent overfitting. The network is trained
    via a standard categorical cross-entropy loss to minimize the loss for multi-class classification. The overall framework and architecture is illustrated in Figure 1.

    **2.1. Efficient Dataset Utilization**
    Even though ICBHI is the largest publicly available dataset with 6898 samples, it is still relatively small for training DNNs effectively. Thus, a major focus of our work has been to develop techniques to efficiently use the available samples.
    We extensively analyzed the dataset to identify dataset characteristics that inhibit training DNNs effectively, and propose solutions to overcome the same.

    The first commonly used technique we apply is transfer learning, where we initialize our network with weights of a pre-trained ResNet-34 network on ImageNet [23]. This is followed by our training where we train the entire network end to-end. Interestingly, even though ImageNet dataset is very different from the spectrograms which our network sees, we still found this initialization to help significantly. Most likely, low level features such as edge-detection are still similar and thus “transfer” well.

    **Concatenation-based Augmentation**: Like most medical datasets, ICBHI dataset has a huge class imbalance, with the normal class accounting for(~의 비율을 차지하다) 53% of the samples. To prevent the model from overfitting on abnormal classes, we experimented with several data augmentation techniques. We first apply standard audio augmentation techniques, such as noise addition, speed variation, random shifting, pitch shift, etc., and also use a weighted random sampler to sample mini-batches uniformly from each class. These standard techniques help a little, but to further improve generalization of the underrepresented(불충분하게 대표된) classes (wheeze, crackle, both), we developed a concatenation based augmentation technique where we generate a new sample of a class by randomly sampling two samples of the same class and concatenating them (see Figure 2). This scheme(책략) led to a non-trivial(하찮지 않은) improvement in the classification accuracy of abnormal classes.


![image](https://user-images.githubusercontent.com/67695343/166191431-7f9be84c-c8f3-4e42-a656-0a92a40e115f.png)
Fig. 2. Proposed concatenation-based augmentation.

    **Smart Padding**: The breathing cycle length varies across patients as well as within a patient depending on various factors (e.g., breathing rate can increase moderately(적당히) during fever). In the ICBHI dataset, the length of breathing cycles ranges from 0.2s to 16.2s with a mean cycle length of 2.7s. This poses a problem while training our network as it expects a fixed size input. The standard way to handle this is to pad the audio signal to a fixed size via zero-padding or reflection based padding. We propose a novel smart padding scheme, which uses a variant of the augmentation scheme described above. For each data sample, smart padding first looks at the breathing cycle sample for the same patient taken just before and after the current one. If this neighbouring cycle is of the same class or of the normal class, we concatenate the current sample with it. If not, we pad by copying the same cycle again. We continue this process until we reach our desired size. This smart padding scheme also augments the data and helps prevent overfitting. We experimented with different input lengths, and found a 7s window to perform best. A small window led to clipping of samples, thus loosing valuable information in an already scarce dataset, while a very large window caused repetition leading to degraded performance.

![image](https://user-images.githubusercontent.com/67695343/166191602-c1f5f9ee-d7fe-4c72-a44e-befe07a56170.png)
Fig. 3. Blank region clipping: The network attention starts focusing more on the bottom half of the spectrogram, instead of blank spaces after clipping.

    **Blank Region Clipping**: On analyzing samples using GradCam++ which our base model mis-classified(잘못 분류된), we found notable black regions at higher frequency regions of their spectrograms (Figure 3). On further analysis, we found that many samples, and in particular 100% of the Litt3200 device samples, had blank region in the 1500-2000Hz frequency range. Since this was adversely affecting our network performance, we selectively clip off the blank rows from the high frequency regions of such spectrograms. This ensures that the network focuses on the region of interest leading to improved
    performance. Figure 3 shows this in action.

    **Device Specific Fine-tuning**: The ICBHI dataset has samples from 4 different devices. We found that the distribution of samples across devices is heavily skewed, e.g. the
    AKGC417L Microphone alone contributes to 63% of the samples. Since each device has different audio characteristics, the DNN may fail to generalize across devices, especially for the underrepresented(대표자가 불충분한) devices in the already small dataset. To verify(확인하다) this, we divided the test set into 4 subsets depending on their device type, and compute the accuracy of abnormal class samples in each subset. As expected, we found the classification accuracy to be strongly correlated with the training set size of the corresponding device. To address this, we first train a common model with the full training data (stage-1,
    Figure 1). We then make 4 copies of this model and fine-tune (stage-2) them for each device separately by using only the subset of training data for that device. We found this approach to significantly improve the performance, especially for the underrepresented devices.

- 요약

### 데이터셋 :

① 920개 (126명의 환자에게서 녹음)

② 총 5.5시간

③ 클래스 4개
i) normal

ii)  crackle

iii) wheeze

iv) crackle과 wheeze 둘 다
④ 녹음기 종류 - 서로 다른 4개
⑤ 녹음 장소

i) 포르투갈

ii) 그리스

⑥ 녹음 부위 - 서로 다른 7가지 신체 부위 (모든 환자 당)

**Chest location
a. Trachea (Tc)    # 기도** (wheezing(천명음), crackle(수포음) 사운드 x but 협찹음(stridor) good)
**b. Anterior left (Al)    #** 앞면청진 
**c. Anterior right (Ar)
d. Posterior left (Pl)   #** 후면청진
**e. Posterior right (Pr)
f. Lateral left (Ll)   #** 측면청진 - 협찹음을 제외한 소리를 얻을 수 있음
**g. Lateral right (Lr)**

### 전처리 :

- 녹음 파일들의 샘플링이 4kHz부터 44kHz까지 달라서 모조리 4kHz로 다운샘플링
- 노이즈(심장박동, background 말소리 등)를 없애고, 5차(5-n) Butterworth 대역 필터를 적용
- 데이터값을 -1, 1 범위안으로 맵핑하기 위해 인풋 신호에 standard normalization 적용
- 그리고 나서 멜-스펙트로그램('음성 데이터'를 '특징벡터'(Feature)화 해주는 알고리즘)으로 변환되어 DNN에 투입된다.

### 네트워크 구조 :

- CNN 베이스의 네트워크
- 2개의 128-d 완전연결된 linear layers (ReLU activations)
- 클래스 범위 확률을 모델링할 때는 마지막 레이어에 softmax activation 적용
- 오버피팅을 막기 위해 fully connected layers에 드랍아웃추가
- 다중분류를 위한 loss를 최소화하기 위해 standard categorical cross-entropy loss를 통해 훈련
- 전체적인 framework와 구조는 맨 위의 Figure 1 참고

---

### 효율적인 데이터셋 이용

충분한 데이터양을 모으고자 사용할 수 있는 샘플들을 효과적으로 사용하게 하는 기법 

### 전이 학습(transfer learning)

- 사전학습된 <`**ImageNet**`에서의 `**ResNet-34**` 네트워크>의 웨이트로 우리의 네트워크를 초기화(initialize)
- 이것은 우리가 전체 네트워크를 끝과 끝을 이어붙여(end-to-end) 훈련시킨 뒤에 수행
- 이미지넷 데이터셋이 우리 네트워크가 보는 스펙트로그램과는 아주 다르지만, 이 초기화작업은 상당히 도움이 됨
- edge-detection(윤곽선 검출)같은 저레벨 특성들은 여전히 비슷한 상태이므로 전이가 잘됨.

### Concatenation-based Augmentation

- ICBHI dataset도 대부분의 의학 데이터처럼 class imbalace가 엄청 남
- normal 클래스가 53%의 비율을 비율을 차지
- 모델이 abnormal 클래스들에게 오버피팅되는 것을 막기 위해 실험된 여러가지 데이터 증강 기법들
- standard audio augmentation techniques
    - noise addition
    - speed variation
    - random shifting
    - pitch shift
    - etc.
    - weighted random sampler to sample mini-batches uniformly from each class
- => 불충분하게 대표된 클래스들(wheeze, crackle, both)의 일반화를 작게나마 향상
- developed Concatenation-based Augmentation 기법
- 새로운 클래스 생성
    - 같은 클래스의 두가지 샘플들을 랜덤하게 샘플링하고, 그것들을 연결

![image](https://user-images.githubusercontent.com/67695343/166191697-9e2f1418-2ca8-4a51-a861-59e380683a5f.png)
Fig. 2. Proposed concatenation-based augmentation.

- => abnormal 클래스들의 분류 정확도 꽤(non-trivially) 향상
- 스마트 패딩
- 호흡 주기의 길이는 환자마다 다르고, 환자마다도 다양한 요소들(열났을 때 호흡율이 적당히 오르는 경우 등)에 따라 다르다
- ICBHI 데이터셋
    - 호흡 사이클 길이 - 0.2초~16.2초
    - 평균 2.7초
- 우리의 네트워크는 고정된 인풋 사이즈를 기대
    - 이 문제를 처리하기 위해 일반적으로 쓰는 방식 - 오디오 신호에 패딩을 주는 것
        - 제로 패딩
        - reflection based 패딩
    - 논문이 제안하는 새로운 방식
        - 스마트 패딩
            - 현재 샘플을 보기 바로 전 후에, 같은 환자에게서 얻어진 호흡 주기 샘플 관찰 → 이웃하는 주기가 같은 클래스거나 normal 클래스면 현재 샘플과 연결
            - 그렇지 않으면 같은 주기를 다시 카피해서 패딩
            - 이 처리는 원하는 사이즈에 이를 때 까지 반복
            - best 인풋길이 - 7s window
            - 오버피팅 방지 효과


- Black Region Clipping
- 스펙트로그램에서 높은 주파수 부분 쪽 검은 공간
- 덜 중요한 정보 -> 성능에 역효과
- 선택적으로 잘라내기
    - 특히 Litt3200 장비로 얻은 샘플들은 100%
- Device Specific Fine-tuning
- 장비마다 다른 오디오 특성O → 일반화하기에는 심하게 왜곡됨
    - AKGC417L Microphone - 샘플의 63%
- 4개 장비별로 데이터셋을 나눔 
-> 각각의 장비별로  abnormal 클래스의 정확도를 계산 
-> 분류 정확도는 훈련세트의 크기와 강한 연관이 있음을 확인
- 보통 쓰는(common) 모델을 훈련 데이터 전체로 학습 
-> 이 모델을 4개 카피 
-> 각각에 4개 장비에서 수집된 데이터를 따로 훈련
- => 상당한 성능 개선
    - 특히 샘플을 적게 수집한 장치

## (2) 주의해야할 이슈

① 데이터셋의 몇 가지 특성

- 원문

In order to efficiently use the available data, we did extensive analysis of the ICBHI dataset. We found several characteristics of the data that might inhibit(억제하다) training DNNs effectively. For example, the dataset contains audio recordings from four different devices, with skewed(왜곡된, 편향된) distribution(분포) of samples across the devices, which makes it difficult for DNNs to generalize well across devices. Similarly, the dataset has a skewed distribution across normal and abnormal classes, and varying lengths of audio samples. We propose multiple novel techniques to address these problems—device specific fine-tuning, concatenation-based augmentation, blank region clipping, and smart padding. We perform extensive evaluation and ablation(삭마, 풍화·침식 작용에 의해 얼음·눈·암석이 깎이는 현상) analysis of these techniques.

- 요약

이 데이터셋의 아래 특성은 DNN을 효과적으로 돌리기 어렵게 함 

- 소리 샘플에 녹음기마다 서로 다른 왜곡된 분포 O  → 모델이 일반화된 학습을 하기에는 어렵다
- normal / abnormal 클래스에 서로 다른 왜곡된 분포 + 서로 다른 샘플 길이

⇒ 이 데이터셋을 효과적으로 사용하기 위해 고안된 기법

- 녹음 장치별로 파인튜닝
- concatenation-based-argumentation
- blank region clipping
- smart padding


② etc.

- 원문

The main contributions of our work are:

1. demonstration that a simple network architecture is sufficient for respiratory sound classification, and more focus is needed on making efficient use of available data.
2. a detailed analysis of the ICBHI dataset pointing out its
characteristics impacting(영향을 주다) DNN training significantly.
3. a suite of techniques—device specific fine-tuning,
concatenation-based augmentation, blank region clipping and smart padding—enabling efficient dataset usage. These techniques are orthogonal to the choice of network architecture and should be easy to incorporate in other networks.
- 요약
1. 데이터셋을 효율적으로 사용하고자 만든 간단한 호흡분류기 네트워크 구조와 기법들
2. 이 논문에서 소개되는 기법들은 여기서 사용된 네트워크 구조 뿐만아니라 다른 네트워크에도 쉽게 포함될 수 있도록 고안됨

## II. An improved adventitious(우발적인, 우연한) lung sound classification using non-local block
resnet neural network with mixup data augmentation

### [참고] I. RespireNet (PPT논문) 내용 중 4. RELATED WORK

Recently, there has been a lot of interest in using deep learning models for respiratory sounds classification [1, 9, 12]. It has outperformed(더 나은 결과를 내다, 능가하다) statistical methods (HMM-GMM) [8] and traditional machine learning methods (boosted decision trees, SVM) [4, 24]. In these deep learning based approaches, a time-frequency representation of the audio signal is provided as input to the model. Kochetov et al. [9] propose a deep recurrent network with a noise masking intermediate(중간의) step for the four class classification task, obtaining a score of 65.7% on the 80-20 split. However the paper omits the details regarding noise label generation [1], thus making it hard to reproduce. Deep residual networks and optimized S-transform based features are used by Chen et al. [6] for three-class classification of anomalies in lung sounds. The model is trained and tested on a smaller subset of the ICBHI dataset on a 70-30 split and achieve a score of 98%.

---

호흡음 분류에 딥러닝을 사용한 최근 연구

- Jyotibdha Acharya and Arindam Basu. Deep neural network
for respiratory sound classification in wearable devices enabled by patient specific model tuning. IEEE Transactions on
Biomedical Circuits and Systems, PP:1–1, 03 2020.
- Kirill Kochetov, Evgeny Putin, Maksim Balashov, Andrey
Filchenkov, and Anatoly Shalyto. Noise Masking Recurrent
Neural Network for Respiratory Sound Classification: 27th International Conference on Artificial Neural Networks, Rhodes,
Greece, October 4–7, 2018, Proceedings, Part III, pages 208–
217. 10 2018. ISBN 978-3-030-01423-0.
- Yi Ma, Xinzi Xu, and Yongfu Li. Lungrn+nl: An improved
adventitious lung sound classification using non-local block
resnet neural network with mixup data augmentation. 08 2020.

딥러닝 베이스 모델은 이전에 사용되던 통계적 기법이나 전통적인 머신 러닝 기법(부스트 결정 트리, SVM)보다 나은 결과를 냄. 딥러닝 모델들은 소리 신호를 파형이미지로 바꾸어 모델에게 인풋데이터로 투입함. (중략) 4개 분류에 사용되는 deep recurrent nework는 65.7%(80-20split). 그러나, Deep residual networks와 특성들을 베이스로 최적화된(optimized) S-transform은 클래스 3개 분류에 사용된다. 이 모델은 70-30 분할로 ICBHI 데이터셋 중 더 작은 부분만 사용했고, 98%의 점수를 성취한다. (→ 혜선님이 추가적으로 찾아주신 부분)

깃헙 링크 추가

## 초록

- 원문

Performing an automated adventitious lung sound detection is a challenging task since the sound is susceptible(민감한) to noises (heart-beat, motion artifacts(움직임 아티팩트 - 영상 촬영시 환자가 움직였을 때 발생되는 것), and audio sound) and there is subtle discrimination((식별되는) 차이) among different categories. An adventitious lung sound classification model, LungRN+NL, is proposed in this work, which has demonstrated a drastic improvement compared to our previous work and the state-of-the-art models. This new model has incorporated(포함하다) the non-local block in the ResNet architecture. To address the imbalance problem and to improve the robustness of the model, we have also incorporated the mixup method to augment the training dataset. Our model has been implemented and compared with the state-of-the-art works using the official ICBHI 2017 challenge dataset and their evaluation method. As a result, `**LungRN+NL**` has achieved a performance
score of 52.26%, which is improved by 2.1-12.7% compared to
the state-of-the-art models.

**Index Terms**: adventitious lung sounds classification, mixup, data augmentation, convolutional neural network, non-local block

- 요약

LungRN+LN 이라는 모델에 ppt논문과 같은 데이터셋, 평가 방법을 사용한 사례 
성능이 52.26%에서 2.1~12.7% 범위로 개선되어 시도해볼 법한 모델로 보입니다!


## III. **A Window Width Optimized S-Transform (예정)**

### **RESPIRENET**

A DEEP NEURAL NETWORK FOR ACCURATELY DETECTING ABNORMAL LUNG SOUNDS IN LIMITED DATA SETTING

[](https://arxiv.org/pdf/2011.00196.pdf)

---

- Vocabs

auscultation: 청진 / 환자의 몸안에서 나는 소리를 청취하여 질병의 여부 판단

stethoscope: 청진기

spirometry: 폐활량측정법

Mel-spectogram: 음성 데이터를 raw data 그대로 사용하면 파라미터가 너무 많아지고 데이터 용량이 너무 커져 mel-spetogram으로 바꿔서 사용한다

- Notes

RespireNet - DNN을 사용하는 것이 당연히 효과가 좋지만 데이터가 많이 필요하다. 그러나 lung diseases의 데이터가 충분치 않기 때문에 작은 사이즈의 데이터로도 사용할 수 있는 CNN 베이스의 모델인 RespireNet을 소개한다

Dataset:

[ICBHI 2017 Challenge](https://bhichallenge.med.auth.gr/ICBHI_2017_Challenge)

- 920 recordings from 126 patients
- 4 classes: normal, crackle, wheeze, or both(crackle and wheeze)
- for every patient, data was recorded at seven different body locations

Pre-processing:

- down-sample the recordings to 4kHz and apply a 5-th order Butterworth band-pass filter to remove noise(heartbeat, back-ground speech, etc.)
- Butterworth filter

[[파이썬 python] Butterworth filter / low pass filter / signal data filtering](https://arumyworld.tistory.com/20)

- apply standard normalization on the input signal to map the values within the range(-1, 1)
- then converted into a Mel-spectogram, which is fed into the DNN

Network architecture:

- CNN-based network, ResNet34
- two 128-d FC linear layers with ReLU activation
- last layer applies softmax activation to model classwise probabliities
- Dropout is added to the FC layers to prevent overfitting
- the network is trained via a standard categorical cross-entropy loss to minimize the loss for multi-class classification

**2.1. Efficient Dataset Utilization**

Transfer learning

- initialize the network with weights of a pre-trained ResNet-34 network on ImageNet. This is followed by its training where trained the entire network end-to-end.
- even though ImageNet dataset is very different from the spectograms, still found this initialization to help significantly. Most likely, low level features such as edge-detection are still similar and thus ‘transfer’ well

Concatenation-based Augmentation

- ICBHI dataset has a huge class imbalance, with the normal class accounting for 53% of the samples
- to prevent model from overfitting on abnormal classes, experimented with several data augmentation techniques
1. standard audio augmentation techiniques : noise addition, speed variation, random shifting, pitch shift,  etc. and also used a weighted random sampler to sample mini-batches uniformly from each class.
2. to furthure improve generalization of the underrepresneted classes (wheeze, crackle, both), developed a concatenation based augmentation technique where we generate a new sample of a class by randomly sampling two samples of the same class and concatenating them → led to non-trivial improvement in the classification accuracy of abnormal classes

Smart Padding

The standard way to handle is to pad the audio signal to a fixed size via zero-padding or reflection based padding

BUT came up with the Smart Padding scheme, which uses a variant of the augmentation scheme (same as above)

For each data sample, smart padding first looks at the breathing cycle sample for the same patient taken just before and after the current one. If  this neighbouring cycle is of the same class or of the normal class, concatenate the current sample with it. If not, pad by copying the same cycle again

→ continue this process until reaching the desired size

→ this smart padding scheme also augments the data and helps prevent overfitting

experimented different input lengths, and  found that 7s window performs the best

- small window led to clipping of samples, thus loosing valuable information in an already scarce dataset
- large window cuased repetition leading to degraded performance

Blank Region Clipping

Black region in a spectogram means that the audio signal has zero energy in the corresponding audio frequency range

Device Specific Fine-tunning

found that the classification accuracy to be strongly correlated with the training set size of the corresponding device

- to address this, first train a common model with the full training data. Then make 4 copies of this model and fine-tune them for each device separately by using only the subset of training data for that device

→ this approach significantly improve the performance, especially for the underrepresented devices


**3.0. Experiments**

final score is computed as the mean of Sensitivity and Specificity

**5. Conclusion**

> The current performance limit of the 4-class classification
task can be mainly attributed to the small size of the ICBHI
dataset, and the variation among the recording devices. Furthermore, there is lack of standardization in the 80-20 split
and we found variance in the results based on the particular
split. In future, we would recommend that the community
should focus on capturing a larger dataset, while taking care
of the issues raised in this paper.
> 


### Triple-Classification of Respiratory Sounds Using Optimized S-Transform and Deep Residual Networks

RESPIRENET - related works에서 좋은 성과를 내어 선택 

> Deep residual networks and optimized S-transform
based features are used by Chen et al. [6] for three-class classification of anomalies in lung sounds. The model is trained
and tested on a smaller subset of the ICBHI dataset on a 70-30
split and achieve a score of 98%
> 

문제정의

> However, due to the contained artifacts and constrained feature extraction methods, the reliability and accuracy of the classification of wheeze, crackle, and normal sounds need significant improvement
> 

이 논문에서는 optimized S-transform(OST) 와 deep residual networks(ResNets)을 사용하여 wheeze, crackle and normal 을 분류

순서

먼저 raw respiratory sound 가 OST를 사용하여 processed → spectogram of OST 가 Resnet을 위해 rescaled → ResNet을 통해 feature learning 과 classification 을 하고 respiratory sound 의 클래스를 recognize