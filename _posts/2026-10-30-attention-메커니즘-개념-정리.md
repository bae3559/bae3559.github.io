---
layout: post
title: "Attention 메커니즘, 개념부터 코드까지 완벽 정리!"
date: 2026-10-30
categories: [인공지능, 딥러닝, 자연어처리]
tags: [Attention, 어텐션, Transformer, 트랜스포머, 딥러닝기초, NLP, PyTorch]
---

# Attention 메커니즘, 개념부터 코드까지 완벽 정리!

안녕하세요, 개발 블로그 독자 여러분!

최근 인공지능 분야에서 가장 뜨거운 키워드 중 하나를 꼽으라면 단연 'Transformer'와 'Attention'일 것입니다. GPT-3, BERT와 같은 대규모 언어 모델들의 기반이 되는 기술이 바로 이 어텐션(Attention) 메커니즘인데요. 이름 그대로 '어디에 집중할 것인가?'를 학습하는 이 메커니즘은 NLP(자연어 처리) 분야에 혁명적인 변화를 가져왔습니다.

오늘은 초보자분들도 쉽게 이해할 수 있도록 어텐션 메커니즘의 기본 개념부터 작동 원리, 그리고 간단한 코드 예제까지 자세히 다뤄보려고 합니다. 자, 그럼 함께 어텐션의 세계로 떠나볼까요?

---

## 1. Attention은 왜 필요했을까? (RNN의 한계)

어텐션 메커니즘이 등장하기 전, 시퀀스(Sequence) 데이터를 처리하는 데 주로 사용되던 모델은 RNN(Recurrent Neural Network)과 그 변형인 LSTM(Long Short-Term Memory), GRU(Gated Recurrent Unit)였습니다. 이 모델들은 문장이나 음성처럼 순서가 중요한 데이터를 처리하는 데 강점을 보였죠.

하지만 RNN 계열 모델에는 치명적인 한계가 있었습니다.

1.  **장기 의존성 문제 (Long-term Dependency Problem):** 문장이 길어질수록 초반에 입력된 정보가 뒤로 갈수록 희미해지거나 손실되는 문제가 발생했습니다. 마치 "옛날 일은 잘 기억 못 하는 사람"과 같았죠.
2.  **정보 병목 현상 (Information Bottleneck):** Seq2Seq(Sequence-to-Sequence) 모델에서는 인코더가 입력 시퀀스 전체를 하나의 고정된 크기의 벡터(Context Vector)로 압축하여 디코더에 전달합니다. 문장이 짧을 때는 문제가 없지만, 문장이 길어질수록 모든 정보를 하나의 벡터에 담기 어려워져 정보 손실이 일어나는 병목 현상이 발생했습니다.

이러한 문제들을 해결하기 위해 등장한 것이 바로 'Attention'입니다.

---

## 2. Attention, "어디에 집중할까?"

어텐션 메커니즘의 핵심 아이디어는 우리 인간의 사고방식과 유사합니다. 우리가 신문을 읽을 때 모든 단어를 동일하게 집중해서 읽는 것이 아니라, 중요한 헤드라인이나 특정 키워드에 더 집중하듯이, 모델도 입력 시퀀스 전체를 보되 현재 시점에서 필요한 정보에 더 '집중'하여 가중치를 부여하는 방식입니다.

쉽게 말해, 어텐션은 **"주어진 시점의 출력값을 예측할 때, 입력 시퀀스 내의 어떤 부분에 더 많은 중요도(가중치)를 두어야 하는가?"**를 학습하는 메커니즘입니다. 이를 통해 모델은 장거리 의존성 문제를 해결하고, 고정된 크기의 컨텍스트 벡터의 한계를 뛰어넘을 수 있게 됩니다.

---

## 3. Attention은 어떻게 작동할까? (Query, Key, Value)

어텐션 메커니즘을 이해하는 데 가장 중요한 세 가지 개념은 바로 **Query (Q), Key (K), Value (V)** 입니다. 이 개념은 데이터베이스의 동작 방식이나, 도서관에서 책을 찾는 방식에 비유하면 이해하기 쉽습니다.

*   **Query (질의):** "지금 내가 찾고 있는 정보는 무엇인가?"
    *   (예: 도서관에서 '인공지능' 관련 책을 찾는 사람)
*   **Key (열쇠/색인):** "어떤 정보들이 존재하고, 그 정보들의 특징은 무엇인가?"
    *   (예: 도서관의 모든 책 표지에 붙은 제목, 저자, 분야 같은 라벨)
*   **Value (값):** "Key에 해당하는 실제 정보는 무엇인가?"
    *   (예: '인공지능' 라벨이 붙은 실제 책의 내용)

어텐션은 다음과 같은 3단계 과정을 거쳐 작동합니다.

1.  **유사도 계산 (Scoring): Query와 Key 비교**
    *   현재 시점의 `Query`와 입력 시퀀스의 모든 `Key`들이 얼마나 유사한지, 즉 얼마나 관련이 깊은지 점수(Score)를 계산합니다. 점수가 높을수록 현재 `Query`에 더 관련이 깊은 `Key`라는 뜻이죠.
    *   가장 일반적인 유사도 계산 방법은 내적(Dot Product)입니다.
        `Score(Query, Key_i) = Query ⋅ Key_i`

2.  **가중치 계산 (Softmax): 유사도 점수를 확률로 변환**
    *   계산된 유사도 점수들을 그대로 사용하는 것이 아니라, Softmax 함수를 사용하여 모든 점수의 합이 1이 되도록 정규화(Normalization)합니다. 이 정규화된 값이 바로 각 `Value`에 부여될 **가중치(Weight)** 입니다. 가중치가 높을수록 해당 `Value`에 더 많은 '집중'을 한다는 의미입니다.
        `Weight_i = Softmax(Score_i)`

3.  **가중 평균 (Weighted Sum): Value에 가중치 부여**
    *   계산된 가중치(Weight)를 해당 `Value`에 각각 곱한 뒤, 모두 더합니다. 이렇게 최종적으로 합쳐진 벡터가 바로 `Context Vector` (또는 Attention Output)가 됩니다. 이 벡터에는 `Query`와 가장 관련 깊은 `Value`들의 정보가 더 강조되어 담겨 있게 됩니다.
        `Context Vector = Σ (Weight_i * Value_i)`

이 과정을 통해 모델은 문장 내의 모든 단어를 고려하되, 번역이나 다음 단어 예측과 같은 특정 태스크 수행 시 필요한 단어들에만 선택적으로 집중할 수 있게 됩니다.

---

## 4. 간단한 Attention 코드 예제 (PyTorch)

이제 위에서 설명한 Q, K, V를 이용한 어텐션 메커니즘을 간단한 PyTorch 코드로 살펴보겠습니다. 이 예제는 이해를 돕기 위한 최소한의 형태로, 실제 Transformer의 Self-Attention과는 다소 다를 수 있지만, 핵심 개념은 동일합니다.

```python
import torch
import torch.nn.functional as F

# 예시 데이터 (임베딩 벡터라고 가정)
# Query: 현재 내가 찾고 있는 정보 (예: 디코더의 현재 상태)
#         차원의 크기는 Key, Value와 맞춰야 함
query = torch.randn(1, 64) # (batch_size, feature_dim)

# Keys: 입력 시퀀스에 있는 각 단어들의 "특징" (예: 인코더의 모든 출력)
# Values: 입력 시퀀스에 있는 각 단어들의 "정보"
#         여기서는 Key와 Value가 같은 벡터라고 가정하겠습니다.
#         실제로는 Key와 Value는 서로 다른 선형 변환을 거쳐 생성될 수 있습니다.
sequence_length = 10 # 입력 시퀀스의 길이
feature_dim = 64     # 임베딩 벡터의 차원

# 10개의 단어 각각에 대한 Key와 Value 벡터 (여기서는 동일하게 생성)
keys = torch.randn(sequence_length, feature_dim) # (seq_len, feature_dim)
values = keys # 간단한 예시를 위해 keys와 values를 동일하게 설정

print(f"Query shape: {query.shape}")
print(f"Keys shape: {keys.shape}")
print(f"Values shape: {values.shape}")
print("-" * 30)

# 1. 유사도 계산 (Scoring): Query와 Key의 내적 (Dot Product)
# Query: (1, 64)
# Keys: (10, 64) -> Transpose (64, 10)
# Result: (1, 10) -> 각 Key에 대한 Query의 유사도 점수
scores = torch.matmul(query, keys.transpose(0, 1))
print(f"Scores (유사도 점수) shape: {scores.shape}")
print(f"Scores:\n{scores}")
print("-" * 30)

# 2. 가중치 계산 (Softmax): 유사도 점수를 정규화하여 가중치로 변환
# Softmax를 적용하여 모든 가중치의 합이 1이 되도록 만듭니다.
# dim=-1은 마지막 차원에 대해 softmax를 적용하라는 의미입니다.
attention_weights = F.softmax(scores, dim=-1)
print(f"Attention Weights (가중치) shape: {attention_weights.shape}")
print(f"Attention Weights:\n{attention_weights}")
print(f"Sum of Weights: {attention_weights.sum()}") # 합이 1인지 확인
print("-" * 30)

# 3. 가중 평균 (Weighted Sum): Value에 가중치를 곱하고 합산
# Values: (10, 64)
# Attention Weights: (1, 10)
# Weighted sum: (1, 64) -> 최종 Context Vector
# torch.matmul(attention_weights, values)는 weights(1,10) * values(10,64)로 계산됩니다.
context_vector = torch.matmul(attention_weights, values)

print(f"Context Vector (어텐션 결과) shape: {context_vector.shape}")
print(f"Context Vector:\n{context_vector}")
```

**코드 설명:**

1.  **데이터 준비:** `query`, `keys`, `values` 텐서를 무작위로 생성합니다. 실제 모델에서는 이 벡터들이 임베딩 레이어나 RNN/Transformer 레이어를 거쳐 생성됩니다.
2.  **유사도 계산 (`scores`):** `query` 텐서와 `keys` 텐서의 전치(transpose)를 `torch.matmul`로 곱하여 각 `key`에 대한 `query`의 유사도 점수를 계산합니다. 이 점수는 `query`가 각 `key`에 얼마나 집중해야 하는지를 나타냅니다.
3.  **가중치 계산 (`attention_weights`):** `F.softmax` 함수를 사용하여 `scores`를 정규화합니다. 이렇게 얻은 `attention_weights`는 각 `key` (그리고 해당하는 `value`)가 최종 `context_vector`에 얼마나 기여할지 나타내는 확률 분포가 됩니다.
4.  **가중 평균 (`context_vector`):** `attention_weights`와 `values`를 다시 `torch.matmul`로 곱하여 가중 평균을 구합니다. 이 `context_vector`는 `query`가 '집중'한 입력 시퀀스의 정보를 담고 있는 최종 출력이 됩니다.

보시다시피, `attention_weights`를 통해 특정 `value`에 더 높은 가중치가 부여될수록 `context_vector`에는 해당 `value`의 정보가 더 많이 반영됩니다.

---

## 5. Attention의 힘, 그리고 Transformer

어텐션 메커니즘은 그 자체로도 강력하지만, 2017년에 발표된 "Attention Is All You Need" 논문에서 제시된 **Transformer** 모델을 통해 그 진가가 발휘되었습니다. 트랜스포머는 RNN 구조를 완전히 제거하고 오직 어텐션 메커니즘만으로 인코더-디코더 구조를 구성하여, 병렬 처리 능력과 장거리 의존성 처리 능력을 극대화했습니다.

이후 트랜스포머는 NLP 분야의 수많은 혁신적인 모델들의 기반이 되었고, 이제는 이미지 처리나 음성 처리 등 다양한 도메인으로 확장되고 있습니다.

---

## 결론하며

오늘 우리는 어텐션 메커니즘의 탄생 배경, 핵심 개념(Query, Key, Value), 작동 원리, 그리고 간단한 PyTorch 코드 예제까지 살펴보았습니다. 어텐션은 단순히 '집중'이라는 아이디어에서 시작했지만, 모델이 더 효율적으로 정보를 처리하고 장거리 의존성 문제를 해결할 수 있도록 돕는 매우 중요한 기술입니다.

이 글이 어텐션 메커니즘을 이해하는 데 좋은 출발점이 되었기를 바랍니다. 다음번에는 Self-Attention, Multi-Head Attention과 같은 더 심화된 주제로 찾아오겠습니다. 계속해서 인공지능의 흥미로운 세계를 함께 탐험해 봐요!

---