---
layout: post
title: "전이학습(Transfer Learning): 머신러닝, 굳이 처음부터 시작할 필요 있나요?"
date: 2026-10-20
categories: [딥러닝, 머신러닝, 인공지능]
tags: [전이학습, TransferLearning, 딥러닝기법, Keras, TensorFlow, MobileNetV2, 특징추출, 미세조정]
---

# 전이학습(Transfer Learning): 머신러닝, 굳이 처음부터 시작할 필요 있나요?

안녕하세요, 개발 블로그 독자 여러분! 🚀

오늘은 머신러닝, 특히 딥러닝 분야에서 효율성과 성능 향상을 동시에 잡을 수 있는 마법 같은 기법, 바로 **전이학습(Transfer Learning)**에 대해 이야기해보려 합니다. "전이학습? 뭔가 복잡해 보이는데..." 라고 생각하셨나요? 전혀 그렇지 않습니다! 초보자분들도 쉽게 이해하실 수 있도록 친절하고 자세하게 설명해 드릴게요.

## 🤯 딥러닝, 왜 늘 처음부터 시작해야 할까요?

여러분은 혹시 딥러닝 모델을 처음부터 학습시키는 과정이 얼마나 많은 시간과 데이터, 그리고 컴퓨팅 자원을 필요로 하는지 알고 계신가요? 수백만, 수천만 장의 이미지 데이터와 수십 대의 GPU를 동원해야 겨우 만족할 만한 성능을 얻을 수 있는 경우가 많습니다. 특히 우리가 다루는 문제가 특정 분야(예: 희귀 질병 진단, 특정 식물 분류)에 국한되어 있다면, 이렇게 방대한 양의 데이터를 처음부터 모으는 것은 거의 불가능에 가깝죠.

이러한 어려움 때문에 많은 연구자와 개발자들은 고민에 빠졌습니다. "이미 다른 문제에서 학습된 똑똑한 모델의 지식을 우리 문제에 활용할 수 없을까?" 이 질문에 대한 해답이 바로 **전이학습**입니다.

## 💡 전이학습(Transfer Learning)이란 무엇인가요?

전이학습은 말 그대로 **'지식의 전이(Transfer)'**를 의미합니다. 즉, **이미 방대한 양의 데이터셋(예: ImageNet)으로 학습되어 특정 문제(예: 1000가지 사물 분류)를 해결하는 데 뛰어난 성능을 보이는 모델의 가중치(weights)를 가져와, 우리가 해결하고자 하는 새로운 문제에 맞게 모델을 재조정하여 활용하는 기법**입니다.

좀 더 쉽게 비유해볼까요?
여러분은 피아노를 꽤 잘 치는 사람이라고 가정해봅시다. 그런데 갑자기 기타를 배워야 하는 상황이 생겼어요. 피아노를 전혀 쳐본 적 없는 사람보다 피아노 경험이 있는 여러분이 기타를 더 빨리 배울 수 있을까요? 당연히 그렇겠죠! 손가락의 독립적인 움직임, 악보를 읽는 능력, 음악적 감각 등 피아노를 통해 얻은 '기본 지식'들이 기타 학습에도 긍정적으로 '전이'되기 때문입니다.

딥러닝 모델도 마찬가지입니다. 예를 들어, ImageNet이라는 방대한 이미지 데이터셋으로 학습된 모델은 이미지 내의 선, 모서리, 질감, 패턴 등과 같은 **'일반적인 시각적 특징(General Visual Features)'**을 추출하는 능력이 뛰어납니다. 이 모델의 초반 레이어들은 마치 시각 정보를 분석하는 '눈'처럼 작동하는 것이죠. 우리가 고양이와 강아지를 분류하는 문제를 풀고 싶을 때, 굳이 이 '눈'을 처음부터 학습시킬 필요 없이, 이미 학습된 '눈'을 빌려와 그 위에 고양이와 강아지를 구분하는 '판단 레이어'만 추가로 학습시키는 것이 전이학습의 핵심입니다.

## 👍 전이학습, 왜 사용해야 할까요? (장점)

전이학습을 활용하면 다음과 같은 다양한 이점을 얻을 수 있습니다.

1.  **데이터 부족 문제 해결**: 적은 양의 데이터만으로도 좋은 성능을 얻을 수 있습니다. 학습된 모델이 이미 '세상의 이미지'에 대한 많은 지식을 가지고 있기 때문이죠.
2.  **빠른 학습 시간**: 이미 대부분의 학습이 완료된 모델의 일부만 재학습하므로, 학습 시간이 현저히 단축됩니다.
3.  **성능 향상**: 처음부터 학습하는 것보다 일반적으로 더 높은 성능을 달성할 수 있습니다. 특히 데이터셋이 작을 때 그 효과가 두드러집니다.
4.  **계산 자원 절약**: GPU와 같은 고가의 컴퓨팅 자원 없이도 효과적인 딥러닝 모델을 구축할 수 있습니다.

## ⚙️ 전이학습은 어떻게 작동할까요? (주요 전략)

전이학습은 주로 두 가지 주요 전략으로 나눌 수 있습니다.

### 1. 특징 추출 (Feature Extraction)

가장 간단하고 일반적인 전이학습 방법입니다.

*   **방법**: 사전 학습된 모델의 합성곱(Convolutional) 층들은 그대로 유지하고 (가중치를 고정시키고), 모델의 마지막 분류(Classification) 층만 우리의 새로운 문제에 맞게 새로 추가하여 학습시킵니다.
*   **언제 사용하면 좋을까요?**: 우리의 새로운 데이터셋이 원본 학습 데이터셋과 **유사성이 높고, 데이터의 양이 충분하지 않을 때** 효과적입니다. 사전 학습된 모델이 추출하는 일반적인 특징들이 우리의 문제에도 유용하게 사용될 것이기 때문입니다.
*   **예시**: ImageNet으로 학습된 모델을 사용하여 고양이와 강아지를 분류하는 문제. 고양이와 강아지도 일반적인 사물 이미지와 크게 다르지 않으므로, 기존 모델의 특징 추출 능력을 그대로 활용할 수 있습니다.

### 2. 미세 조정 (Fine-tuning)

좀 더 적극적으로 사전 학습된 모델을 활용하는 방법입니다.

*   **방법**: 사전 학습된 모델의 가중치를 고정하지 않고, 모델의 일부 또는 전체 층을 우리의 새로운 데이터셋으로 다시 학습시킵니다. 이때 일반적으로 학습률(Learning Rate)은 처음부터 학습할 때보다 훨씬 작게 설정하여 기존 가중치들이 급격히 변하는 것을 방지합니다.
*   **언제 사용하면 좋을까요?**: 우리의 새로운 데이터셋이 원본 학습 데이터셋과 **유사성이 낮거나, 데이터의 양이 어느 정도 충분할 때** 사용합니다. 기존 모델이 가진 일반적인 지식을 기반으로, 우리의 특수한 문제에 더 잘 맞는 특징들을 학습하도록 모델을 '미세 조정'하는 것이죠.
*   **예시**: ImageNet으로 학습된 모델을 사용하여 의료 영상(X-ray, MRI 등)에서 질병을 진단하는 문제. 의료 영상은 일반적인 사물 이미지와는 매우 다른 특징을 가질 수 있으므로, 모델의 하위 층 일부를 미세 조정하여 의료 영상에 특화된 특징을 학습시키는 것이 좋습니다.

## 🧑‍💻 코드로 알아보는 전이학습 예제 (Keras/TensorFlow)

이제 실제 코드를 통해 전이학습이 어떻게 구현되는지 살펴보겠습니다. 여기서는 **특징 추출(Feature Extraction)** 전략을 사용하여, `MobileNetV2`라는 사전 학습된 모델을 기반으로 `CIFAR-10` 데이터셋의 이미지를 분류하는 간단한 모델을 만들어볼 것입니다.

`MobileNetV2`는 `ImageNet`으로 학습된 경량화된 모델로, 모바일 및 임베디드 환경에 최적화되어 있으면서도 훌륭한 성능을 보여줍니다. `CIFAR-10`은 10가지 클래스의 작은 컬러 이미지 데이터셋입니다.

```python
import tensorflow as tf
from tensorflow.keras.applications import MobileNetV2
from tensorflow.keras.layers import Dense, GlobalAveragePooling2D
from tensorflow.keras.models import Model
from tensorflow.keras.utils import to_categorical
import numpy as np

print(f"TensorFlow Version: {tf.__version__}")

# 1. 데이터 로드 및 전처리
# CIFAR-10 데이터셋을 사용합니다. (32x32 크기 이미지, 10개 클래스)
# MobileNetV2는 224x224 이미지를 기대하므로, 리사이징이 필요합니다.
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.cifar10.load_data()

# 예제 실행 속도를 위해 데이터셋의 작은 부분만 사용합니다.
x_train = x_train[:1000]
y_train = y_train[:1000]
x_test = x_test[:200]
y_test = y_test[:200]

num_classes = 10 # CIFAR-10의 클래스 개수

# 레이블을 원-핫 인코딩으로 변환
y_train = to_categorical(y_train, num_classes)
y_test = to_categorical(y_test, num_classes)

print(f"원본 학습 데이터셋 크기: {x_train.shape}")
print(f"원본 테스트 데이터셋 크기: {x_test.shape}")

# 이미지를 MobileNetV2 입력 크기(224x224)로 리사이징하고 정규화합니다.
def preprocess_image_for_mobilenet(image):
    image = tf.image.resize(image, (224, 224))
    image = tf.cast(image, tf.float32) / 255.0  # 0-1 범위로 정규화
    # MobileNetV2는 -1 ~ 1 범위의 입력을 선호하지만, 0-1도 잘 작동합니다.
    # tf.keras.applications.mobilenet_v2.preprocess_input(image) 사용도 가능
    return image

x_train_processed = np.array([preprocess_image_for_mobilenet(img) for img in x_train])
x_test_processed = np.array([preprocess_image_for_mobilenet(img) for img in x_test])

print(f"전처리 후 학습 데이터셋 크기: {x_train_processed.shape}")
print(f"전처리 후 테스트 데이터셋 크기: {x_test_processed.shape}")

# 2. 사전 학습된 MobileNetV2 모델 로드
# weights='imagenet': ImageNet 데이터셋으로 학습된 가중치를 사용합니다.
# include_top=False: 모델의 최상위 분류 레이어는 포함하지 않습니다. (우리가 새로 정의할 것이기 때문)
# input_shape=(224, 224, 3): 모델의 입력 이미지 크기를 지정합니다.
base_model = MobileNetV2(weights='imagenet', include_top=False, input_shape=(224, 224, 3))

# 3. 사전 학습된 모델의 가중치 동결 (Feature Extraction 전략)
# base_model의 모든 레이어가 학습되지 않도록 설정합니다.
base_model.trainable = False

# 4. 새로운 분류 레이어 추가
# base_model의 출력을 가져와서 우리의 새로운 분류 문제를 위한 레이어를 추가합니다.
x = base_model.output
x = GlobalAveragePooling2D()(x) # 특징 맵의 공간 차원을 줄여 하나의 벡터로 만듭니다.
x = Dense(128, activation='relu')(x) # 새로운 은닉층 추가 (원하는 대로 구성 가능)
predictions = Dense(num_classes, activation='softmax')(x) # CIFAR-10의 10개 클래스를 위한 최종 출력 레이어

# 5. 최종 모델 정의
model = Model(inputs=base_model.input, outputs=predictions)

# 6. 모델 컴파일
# 'adam' 옵티마이저와 'categorical_crossentropy' 손실 함수를 사용합니다.
# 이진 분류의 경우 'binary_crossentropy'를 사용합니다.
model.compile(optimizer='adam',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# 모델 구조 요약 확인 (새로 추가된 레이어와 동결된 base_model을 볼 수 있습니다)
print("\n--- 모델 요약 ---")
model.summary()

# 7. 모델 학습 (특징 추출 부분만 학습)
# 적은 데이터셋과 에포크 수로 빠르게 학습합니다.
print("\n--- 모델 학습 시작 (짧은 예시를 위해 5 에포크만 진행) ---")
history = model.fit(x_train_processed, y_train,
                    epochs=5,
                    validation_data=(x_test_processed, y_test),
                    batch_size=32)

print("\n--- 학습 완료 ---")

# 8. 간단한 예측 예시
print("\n--- 예측 예시 ---")
sample_image = x_test_processed[0:1] # 테스트 데이터셋의 첫 번째 이미지 가져오기
predicted_probabilities = model.predict(sample_image)
predicted_class = np.argmax(predicted_probabilities[0])
true_class = np.argmax(y_test[0])

print(f"예측 결과 (확률): {predicted_probabilities[0]}")
print(f"가장 높은 확률의 예측 클래스: {predicted_class}")
print(f"실제 클래스: {true_class}")

# 9. (선택 사항) 미세 조정을 위한 모델 변경
# 만약 미세 조정을 하고 싶다면, base_model의 일부 레이어를 다시 학습 가능하게 설정하고
# 더 작은 학습률로 모델을 재컴파일 후 학습을 이어갈 수 있습니다.
# print("\n--- 미세 조정을 위한 모델 설정 예시 ---")
# base_model.trainable = True # base_model의 가중치를 학습 가능하게 설정
# # for layer in base_model.layers[:-30]: # 예를 들어, 마지막 30개 레이어만 제외하고 동결
# #     layer.trainable = False
#
# # 미세 조정을 위해 더 낮은 학습률을 가진 옵티마이저를 사용합니다.
# model.compile(optimizer=tf.keras.optimizers.RMSprop(learning_rate=1e-5),
#               loss='categorical_crossentropy',
#               metrics=['accuracy'])
# model.summary()
# print("\n이제 이 모델을 더 낮은 학습률로 추가 학습할 수 있습니다 (미세 조정).")
```

**코드 설명:**

1.  **데이터 로드 및 전처리**: `CIFAR-10` 데이터를 불러와 `MobileNetV2`가 기대하는 224x224 크기로 이미지 크기를 조절하고 픽셀 값을 정규화합니다. 레이블은 원-핫 인코딩으로 변환합니다. 예시의 빠른 실행을 위해 데이터셋의 크기를 줄였습니다.
2.  **사전 학습 모델 로드**: `MobileNetV2` 모델을 `imagenet` 가중치와 함께 로드합니다. `include_top=False`를 통해 최상위 분류 레이어는 제거합니다.
3.  **가중치 동결**: `base_model.trainable = False`를 설정하여 `MobileNetV2`의 모든 레이어 가중치가 학습 중에 업데이트되지 않도록 고정합니다. 이 부분이 '특징 추출' 전략의 핵심입니다.
4.  **새로운 분류 레이어 추가**: `base_model`의 출력 위에 `GlobalAveragePooling2D`와 `Dense` 레이어를 추가하여 우리의 `CIFAR-10` 분류 문제에 맞는 새로운 헤드(Head)를 만듭니다.
5.  **최종 모델 정의**: `base_model`의 입력과 새로 추가된 레이어들을 연결하여 최종 모델을 생성합니다.
6.  **모델 컴파일**: `adam` 옵티마이저와 `categorical_crossentropy` 손실 함수를 사용하여 모델을 컴파일합니다.
7.  **모델 학습**: `fit()` 메서드를 사용하여 모델을 학습합니다. 이때 학습되는 부분은 새로 추가된 분류 레이어뿐이며, `base_model`의 가중치는 변하지 않습니다.
8.  **예측 예시**: 학습된 모델을 사용하여 새로운 이미지에 대한 예측을 수행합니다.

이 코드를 실행해보시면, 적은 학습 데이터와 짧은 에포크 만으로도 어느 정도의 분류 정확도를 달성하는 것을 확인하실 수 있습니다. 처음부터 모델을 학습시키는 것과 비교했을 때 얼마나 효율적인지 체감할 수 있을 것입니다.

## ⚠️ 전이학습 시 주의할 점

*   **원본 문제와 새 문제의 유사성**: 원본 모델이 학습된 데이터와 새로운 데이터의 도메인이 유사할수록 전이학습의 효과는 극대화됩니다. 만약 전혀 다른 도메인이라면 미세 조정을 통해 더 많은 층을 학습시키거나, 전이학습의 효과가 적을 수도 있습니다.
*   **데이터셋 크기**: 새로운 데이터셋의 크기가 매우 작다면 특징 추출 전략이 안전합니다. 데이터셋이 커질수록 미세 조정을 통해 더 좋은 성능을 기대할 수 있습니다.
*   **사전 학습 모델 선택**: 문제의 특성과 컴퓨팅 자원을 고려하여 적절한 사전 학습 모델(VGG, ResNet, EfficientNet, MobileNet 등)을 선택해야 합니다.

## 맺음말

전이학습은 딥러닝 모델 개발에 있어 가장 강력하고 실용적인 기법 중 하나입니다. 데이터 부족, 학습 시간, 계산 자원 등의 문제에 부딪혔을 때, 이미 세상의 많은 지식을 학습한 '똑똑한 선생님'의 도움을 받는 것이죠.

오늘 정리해 드린 개념과 코드 예제를 바탕으로 여러분도 각자의 프로젝트에 전이학습을 적용해보세요! 분명 여러분의 딥러닝 여정을 더욱 쉽고 효과적으로 만들어 줄 것입니다. 궁금한 점이 있다면 언제든지 댓글로 질문해주세요! 다음에는 더 유익한 주제로 찾아오겠습니다. 감사합니다!

---