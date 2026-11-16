---
layout: post
title: "딥러닝, 훈련 시간을 확 줄여주는 마법! 전이학습(Transfer Learning) 완전 정복"
date: 2026-11-16
categories: [Deep Learning, Machine Learning, AI]
tags: [전이학습, TransferLearning, 딥러닝, 머신러닝, 사전학습모델, Keras, TensorFlow, 인공지능, 개발블로그, 파이썬]
---

# 딥러닝, 훈련 시간을 확 줄여주는 마법! 전이학습(Transfer Learning) 완전 정복

안녕하세요! 딥러닝의 세계에 오신 여러분들을 환영합니다. 인공지능이 세상을 바꾸고 있는 지금, 딥러닝은 정말 매력적인 분야죠. 하지만 딥러닝 모델을 처음부터 학습시키는 것은 만만치 않은 일입니다. 엄청난 양의 데이터가 필요하고, 엄청난 컴퓨팅 자원과 시간이 소모되거든요. "데이터는 부족한데, 모델은 만들고 싶고..." 이런 고민을 해보신 적 있으신가요?

걱정 마세요! 오늘 소개해 드릴 **전이학습(Transfer Learning)**은 이런 고민을 해결해 줄 수 있는 아주 강력한 기술입니다. 마치 자전거 타는 법을 배워서 오토바이를 더 쉽게 배우는 것과 비슷하다고 할 수 있죠. 이미 다른 문제를 해결하기 위해 학습된 지식을 활용하여 새로운 문제를 더 빠르고 효율적으로 해결하는 방법, 바로 전이학습입니다.

## 전이학습(Transfer Learning)이란 무엇인가요?

전이학습은 이름 그대로 **'지식(학습된 내용)을 전이(옮겨)해서 학습한다'**는 의미입니다. 구체적으로는, 어떤 특정 작업(예: 고양이와 개 분류)을 위해 미리 학습된(Pre-trained) 딥러닝 모델의 지식과 구조를 가져와서, 우리가 풀고자 하는 새로운 작업(예: 암세포 분류)에 재활용하는 기법입니다.

왜 이런 방법이 효과적일까요? 이미지 분류를 예로 들어볼게요. 아주 복잡한 딥러닝 모델은 수많은 이미지들을 학습하면서 '선의 특징', '모서리 특징', '질감 특징' 같은 아주 기본적인 시각적 특징들을 먼저 배웁니다. 그리고 더 깊은 층으로 갈수록 '눈', '코', '바퀴'처럼 추상적이고 복합적인 특징들을 인식하게 되죠.

이때, 기본적인 특징들(선, 모서리, 질감 등)은 어떤 이미지 데이터셋이든 공통적으로 나타나는 경우가 많습니다. 전이학습은 이 **"일반적인 특징들을 추출하는 능력"**을 이미 갖추고 있는 사전 학습된 모델을 활용하는 겁니다. 이렇게 하면 모델이 아주 기초적인 것부터 다시 학습할 필요 없이, 우리가 풀고자 하는 문제에 특화된 부분만 빠르게 학습할 수 있게 됩니다.

### 전이학습의 주요 장점

1.  **데이터 부족 문제 해결:** 대규모 데이터셋이 없어도 강력한 모델을 만들 수 있습니다.
2.  **학습 시간 단축:** 처음부터 학습하는 것보다 훨씬 빠르게 수렴하고, 결과적으로 훈련 시간이 대폭 줄어듭니다.
3.  **성능 향상:** 적은 데이터로도 최신 딥러닝 모델에 필적하는, 때로는 그 이상의 성능을 낼 수 있습니다.

## 전이학습, 어떻게 적용할까요? (두 가지 주요 방법)

전이학습을 적용하는 방법은 크게 두 가지로 나눌 수 있습니다.

### 1. 특징 추출기(Feature Extractor)로 사용하기 (Freezing)

이 방법은 사전 학습된 모델의 대부분의 층은 **'얼어붙어(freeze)'** 있습니다. 즉, 가중치가 더 이상 업데이트되지 않도록 고정하는 것이죠. 그리고 사전 학습된 모델의 마지막 출력 층(일반적으로 분류 층)을 제거하고, 우리의 새로운 문제에 맞는 새로운 분류기 층(Dense Layer 등)을 추가하여 학습시킵니다.

-   **언제 사용할까요?** 우리의 데이터셋이 매우 작고, 사전 학습된 모델이 학습했던 데이터와 우리의 데이터가 시각적으로 매우 유사할 때 효과적입니다. (예: `ImageNet`으로 학습된 모델을 `새로운 품종의 강아지 분류`에 사용)
-   **비유:** 이미 그려진 아주 멋진 그림(사전 학습된 모델)이 있는데, 우리는 그 그림 위에 우리가 원하는 주제의 색깔(새로운 분류기)만 덧칠하는 것에 가깝습니다.

### 2. 미세 조정(Fine-tuning)

미세 조정은 특징 추출 방식보다 조금 더 적극적으로 모델을 변경하는 방법입니다. 사전 학습된 모델의 일부 층(주로 출력 층과 가까운 층) 또는 전체 층을 얼리지 않고, 아주 **작은 학습률(learning rate)**로 우리의 데이터에 맞게 다시 학습시키는 방법입니다. 일반적으로 처음 몇 층은 얼려두고, 중간 층부터 마지막 층까지를 미세 조정하는 경우가 많습니다.

-   **언제 사용할까요?** 우리의 데이터셋이 어느 정도 크기가 있고, 사전 학습된 모델이 학습했던 데이터와 우리의 데이터가 시각적으로 비슷하지만 약간의 차이가 있을 때 좋은 성능을 보입니다.
-   **비유:** 이미 잘 만들어진 그림에 우리가 원하는 디테일을 추가하거나 전체적인 색감을 조절(가중치 미세 조정)하여, 우리만의 그림으로 만드는 과정과 비슷합니다.

## 코드 예제: 케라스를 이용한 전이학습 (특징 추출 방식)

이제 실제 코드를 통해 전이학습이 어떻게 이루어지는지 살펴볼까요? 여기서는 케라스(Keras)와 텐서플로우(TensorFlow)를 사용하여 `MobileNetV2`라는 사전 학습 모델을 가져와 우리의 이미지 분류 문제에 적용하는 예시를 보여드리겠습니다. `MobileNetV2`는 작고 효율적이라 모바일 환경이나 전이학습 예제에 많이 사용됩니다.

이 예제에서는 '특징 추출기' 방식으로 전이학습을 진행합니다.

```python
import tensorflow as tf
from tensorflow.keras.applications import MobileNetV2
from tensorflow.keras.layers import Dense, GlobalAveragePooling2D
from tensorflow.keras.models import Model
from tensorflow.keras.optimizers import Adam
import numpy as np # 가상의 데이터를 만들기 위해 사용

print(f"TensorFlow Version: {tf.__version__}")

# 1. 사전 학습된 모델 불러오기
# MobileNetV2는 ImageNet 데이터셋으로 학습된 모델입니다.
# include_top=False: 모델의 상단(분류) 층을 포함하지 않습니다. 우리의 새로운 분류기를 추가할 것이기 때문입니다.
# weights='imagenet': ImageNet 데이터셋으로 학습된 가중치를 사용합니다.
# input_shape: 이미지의 크기와 채널 수 (예: 224x224 픽셀, RGB 3채널)
base_model = MobileNetV2(input_shape=(224, 224, 3),
                         include_top=False,
                         weights='imagenet')

# 2. 사전 학습된 모델의 층들을 '얼리기' (Freezing)
# base_model의 가중치들이 훈련 중에 업데이트되지 않도록 설정합니다.
base_model.trainable = False
print(f"Base model layers are trainable: {base_model.trainable}")

# 3. 새로운 분류기(Classification Head) 추가
# base_model의 출력 위에 우리의 새로운 분류 층들을 쌓습니다.
# GlobalAveragePooling2D: 특징 맵의 공간적 차원을 줄여 하나의 벡터로 만듭니다.
# Dense: 완전 연결 층. 우리의 문제에 맞는 최종 분류 층입니다. (여기서는 2개의 클래스 예시)
x = base_model.output
x = GlobalAveragePooling2D()(x)
x = Dense(128, activation='relu')(x) # 중간 층 추가
predictions = Dense(2, activation='softmax')(x) # 우리의 목표 클래스 수 (예: 2개)

# 4. 최종 모델 생성
# base_model의 입력과 새로 추가한 분류기 층을 연결하여 최종 모델을 만듭니다.
model = Model(inputs=base_model.input, outputs=predictions)

# 5. 모델 컴파일
# 모델을 학습시키기 전에 손실 함수(loss function), 옵티마이저(optimizer), 평가 지표(metrics)를 설정합니다.
model.compile(optimizer=Adam(learning_rate=0.0001), # 작은 학습률을 사용하면 좋습니다.
              loss='categorical_crossentropy', # 다중 클래스 분류에 적합
              metrics=['accuracy'])

# 모델 구조 요약 확인
model.summary()

# (옵션) 모델 학습 예시 (실제 데이터 대신 가상의 데이터로 진행)
# 실제 프로젝트에서는 ImageDataGenerator 등을 사용해서 실제 이미지를 로드해야 합니다.
print("\n--- 가상의 데이터로 모델 학습 예시 ---")
num_samples = 100
dummy_images = np.random.rand(num_samples, 224, 224, 3) # (샘플 수, 높이, 너비, 채널)
dummy_labels = tf.keras.utils.to_categorical(np.random.randint(0, 2, num_samples), num_classes=2) # 2개 클래스

print(f"가상 이미지 데이터 형태: {dummy_images.shape}")
print(f"가상 레이블 데이터 형태: {dummy_labels.shape}")

model.fit(dummy_images, dummy_labels, epochs=3, batch_size=32, verbose=1)

print("\n전이학습 모델 구성 및 학습 예시 완료!")
print("이제 실제 데이터를 준비하여 이 모델을 훈련시키면 됩니다.")
```

### 코드 설명

1.  **`MobileNetV2` 불러오기**: `include_top=False`는 `ImageNet` 데이터셋에 맞는 최종 1000개 클래스 분류기를 제외하고 특징 추출 부분만 가져오겠다는 의미입니다.
2.  **`base_model.trainable = False`**: 이 부분이 가장 중요합니다. `MobileNetV2`의 모든 층들이 훈련 중에 가중치를 업데이트하지 않도록 고정(freeze)합니다.
3.  **새로운 분류기 추가**: `GlobalAveragePooling2D`를 통해 특징 맵을 단일 벡터로 만들고, `Dense` 층을 추가하여 우리의 문제에 맞는 새로운 분류기를 만듭니다. `predictions` 층의 `Dense(2, ...)`는 우리가 2개의 클래스를 분류한다고 가정했기 때문입니다.
4.  **모델 컴파일**: `Adam` 옵티마이저와 `categorical_crossentropy` 손실 함수를 사용합니다. `learning_rate`를 아주 작게 설정하는 것이 좋습니다.
5.  **`model.summary()`**: 모델의 층별 정보를 보여줍니다. `Non-trainable params`가 `base_model`의 파라미터 수와 일치하는 것을 확인할 수 있습니다.
6.  **가상 데이터 학습**: 실제 데이터 대신 무작위로 생성된 이미지와 레이블로 `fit` 함수를 호출하여, 모델이 정상적으로 학습을 시작할 수 있음을 보여줍니다.

## 마무리하며

전이학습은 딥러닝 모델 개발에 있어 "시간과 자원을 절약하면서도 높은 성능을 달성"할 수 있게 해주는 정말 강력한 기술입니다. 여러분이 딥러닝 프로젝트를 시작할 때 데이터셋이 부족하거나, 학습 시간을 단축하고 싶다면 주저 없이 전이학습을 고려해보세요!

오늘 설명드린 '특징 추출' 방식과 '미세 조정' 방식을 잘 이해하고 여러분의 프로젝트 상황에 맞춰 적용한다면, 분명 좋은 결과를 얻을 수 있을 겁니다.

다음 시간에는 실제 데이터셋을 가지고 전이학습을 적용하는 방법에 대해 더 자세히 다뤄볼게요. 궁금한 점이 있다면 언제든지 댓글로 남겨주세요!