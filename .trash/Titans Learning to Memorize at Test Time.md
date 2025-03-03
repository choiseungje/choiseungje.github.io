---
author:
  - A
  - B
submission:
  - PMIR
  - B
year: "2024"
file: 
related: 
tags:
  - Test-Time-Learning
review date: {{date}}
---
# <font color="#31859b">Summary</font>

## 1-Line

```
Titans은 Transformer의 단기 기억과 신경망 기반 장기 기억을 결합하여, 2M 이상의 문맥 창을 효율적으로 처리하는 새로운 아키텍처를 제안[12:Abstract]
```
## 3-Line

```
Titans은 Transformer의 단기 기억(attention)과 신경망 기반 장기 기억(neural memory)을 결합하여, 긴 문맥에서 학습 및 추론 성능을 향상시키는 모델이다[12:Abstract, §1].
```

```
기존 Transformer는 높은 계산량 문제를 해결하지 못했으며, 선형 순환 모델은 정보 손실이 심각했다[12:§1].
```

```
Titans은 이러한 한계를 극복하고, 언어 모델링, 상식 추론, 유전자 분석, 시계열 예측 등 다양한 작업에서 우수한 성능을 보인다[12:§1, §3].
```
## 5-Line

```
Transformer는 제한된 문맥 창 내에서만 작동하여 장기 의존성을 학습하는 데 한계가 있다[12:§1].
```

```
기존 선형 순환 모델은 정보를 압축하여 저장하지만, 표현력이 부족하고 장기 기억을 효과적으로 활용하지 못한다[12:§2].
```

```
Titans은 신경망 기반의 장기 기억 모듈을 도입하여 Transformer와 결합함으로써 이러한 문제를 해결한다[12:§3].
```

```
효율적인 학습과 확장 가능한 구조를 통해 2M 이상의 문맥 창을 처리할 수 있으며, 기존 모델보다 높은 정확도를 달성한다[12:§5].
```

```
자연어 처리, 시계열 분석, 유전자 분석 등 다양한 분야에서 활용 가능성을 제시한다[12:§6].
```


# <font color="#7030a0">Paper Review</font>

## Abstract / Motivation

> 기존 순환 신경망(RNN)은 선형 복잡도(linear complexity)를 가지지만, 메모리 업데이트 방식의 한계로 인해 긴 컨텍스트에서 정보 손실이 발생하며 성능이 제한적이다..\["While both linear Transformers and linear RNNs (including state space models) compress the information in memory update step, the critical difference lies in the structure of the memory."]\[12:Section 2, p.4]
- Self-attention은 긴 컨텍스트에서 강력한 성능을 보이지만, 이차 복잡도(quadratic complexity, O(n²))로 인해 확장성(scalability)이 제한된다.\["This accurate modeling of dependencies, however, comes with a quadratic cost, limiting the model to a fixed-length context."]\[12:Abstract]
- 장기 문맥(long-context)을 효과적으로 활용하면서도 효율적인 연산이 가능한 새로운 시퀀스 모델링 기법이 필요하다.\["To remain both efficient and expressive in long context, we need a better compression heuristic."]\[12:Section 2, p.5]]

## Background

- 선형 Transformer가 제안되었지만, 정보 압축 과정에서 중요한 내용을 손실하는 문제가 있다
- Transformer는 attention 기반으로 작동하며, 고정된 길이의 문맥 창 내에서 단기 연관성을 학습하는 구조를 가지고 있지만 문맥 창이 길어질수록 계산량이 증가하여 확장성이 제한된다.\[12:§1].

## Targeting Problems

- 장기 기억을 포함하는 새로운 구조가 필요하다.\[12:§1].
- RNN 및 Linear Transformer 모델은 장기 문맥을 저장하지만, 효과적으로 활용하는 메커니즘이 부족하다\[12:§2].
- 기존 Transformer는 문맥 창 크기에 따라 계산 비용이 기하급수적으로 증가한다\[12:§2, §3].

## Suggestions / Methods

##### 1.Titans 아키텍처 설계
- Titans은 신경망 기반 장기 기억을 효과적으로 통합하기 위해 세 가지 주요 변형(variants)을 제안
	
	1. Memory as Context (MAC) – 기억을 문맥의 일부로 활용
		
		- 기존 Transformer의 문맥(context)에 장기 기억을 추가하여, 과거 데이터를 직접 활용할 수 있도록 함.
		
		- 각 시퀀스가 처리될 때, 장기 기억에서 가장 관련성이 높은 정보를 찾아 문맥으로 삽입하는 방식.\["Attention by having both historical and current context, has the ability to decide whether given the current data, the long-term memory information is needed."]\[12:§4.1, p.10]
	
	2. Memory as Gating (MAG) – 기억을 게이트(gate)로 조정
		
		- 기존 Transformer의 Self-attention과 장기 기억을 병렬적으로 학습한 후, 게이트(gate) 메커니즘을 적용하여 두 정보를 결합하는 방식\[12:§4.2].
		
		- 어떤 정보가 더 중요한지 자동으로 조정하는 능력을 갖추며, 주어진 입력에 따라 장기 기억을 다르게 활용함\["Sliding window attention is acting as a precise short-term memory, while the neural memory module is acting as a fading memory for the model."_]\[12:§4.2].
		- g(Yshort​,Ylong​)=σ(Wg​*Yshort​+bg​)⊙Ylong​+(1−σ(Wg*​Yshort​+bg​))⊙Yshort​
	
	4. Memory as a Layer (MAL) – 기억을 독립적인 계층(layer)으로 추가
		
		- Transformer 구조에 별도의 장기 기억 레이어를 추가하여, 기존 레이어와 독립적으로 학습하는 방식\["We then use this historical information along with our persistent memory parameters as the input sequence to the attention module."]\[12:§4.3]
##### 2.신경망 기반 장기 기억 (Neural Long-term Memory)
- Titans은 신경망 기반 장기 기억 모듈(Neural Memory Module)을 추가하여, 과거 데이터를 저장하고 적절한 시점에 이를 참조할 수 있도록 한다\[12:§3].
	1. 서프라이즈 메트릭(Surprise Metric) 활용
		- 이 메트릭은 새로운 입력 데이터가 기존 메모리와 얼마나 다른지를 평가하여, 예상 밖의 정보(Unexpected Information)를 더 잘 기억하도록 설계\[To this end, we measure the surprise of an input with the gradient of the neural network with respect to the input in associative memory loss."]\[12:§3.1, p.7]
		- 서프라이즈 메트릭(Surprise Metric) = |∇ℓ(𝑀ₜ₋₁, 𝑥ₜ)| (입력 데이터 𝑥ₜ, 기존 기억 상태 𝑀ₜ₋₁)
	2. 적응형 망각 메커니즘 (Adaptive Forgetting Mechanism)
		- 이 기법은 기억 크기(memory size)와 데이터의 중요도(surprise score)를 기반으로 어떤 정보를 유지하고 어떤 정보를 제거할지 결정\["To better handle the limited memory, we present a decaying mechanism that considers the proportion of memory size and the amount of data surprise, resulting in better memory management."_]\[12:§3.1, p.8]

###### 장점
1. 장기 기억(Neural Memory) 활용
	- 더 긴 문맥을 처리
	- 장기적인 의존성을 학습
2. 효율적인 계산 자원 사용
	-  Neural Memory를 활용함으로써, 불필요한 정보의 반복 처리를 줄임
	-  필요한 정보만을 선택적으로 활용하여 계산 자원을 효율적임

