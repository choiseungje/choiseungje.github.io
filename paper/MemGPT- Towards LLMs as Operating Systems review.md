
# <font color="#31859b">Summary</font>

## 1-Line

```
MemGPT는 LLM의 제한된 컨텍스트 윈도우를 운영 체제(OS)의 가상 메모리 관리 기법을 활용하여 확장하는 새로운 접근 방식을 제안 [Abstract, p.1].

```
## 3-Line

```
기존 LLM은 컨텍스트 윈도우 크기가 제한적이며, 이를 확장하는 것은 연산 비용 증가 문제를 초래함 [Introduction, p.2].
```

```
MemGPT는 OS의 가상 메모리 페이징 개념을 차용하여 계층적 메모리 시스템을 구축하고, LLM이 자동으로 메모리를 관리하도록 설계됨 [Section 2, p.4].
```

```
MemGPT는 다중 문서 분석과 다중 세션 챗봇에서 기존 LLM보다 높은 성능을 보이며, 장기적인 문맥 유지 및 효율적인 정보 검색을 가능하게 함 [Experiments, p.12].

```
## 5-Line

```
LLM은 제한된 컨텍스트 윈도우로 인해 긴 대화나 문서 분석에서 성능이 저하되는 문제가 있음 [Introduction, p.2].
```

```
기존 방식은 컨텍스트 길이를 단순히 확장하거나 요약하는데, 이는 연산 비용 증가 및 정보 손실 문제를 초래함 [Background, p.3].
```

```
MemGPT는 가상 메모리 페이징 개념을 활용하여 LLM이 자동으로 컨텍스트를 유지하고, 필요할 때만 관련 데이터를 불러올 수 있도록 함 [Section 2.1, p.5].
```

```
다중 세션 대화 및 장기적인 문서 분석에서 MemGPT가 기존 방법보다 높은 정확도를 보이며, 정보 유지 및 검색 능력이 향상됨 [Experiments, p.13].
```

```
실험 결과, MemGPT는 GPT-4 기반 모델과 결합하여 기존 GPT-4 대비 성능을 크게 향상시킴 [Experiments, p.17].
```


# <font color="#7030a0">Paper Review</font>

## Abstract / Motivation

> 기존 LLM은 컨텍스트 윈도우가 제한적이어서 장기적인 문서 분석과 다중 세션 대화에서 성능이 저하됨 \["Large language models (LLMs) have revolutionized AI, but are constrained by limited context windows, hindering their utility in tasks like extended conversations and document analysis.", Abstract, p.1].
- Transformer 기반 모델은 자기 주의(Self-Attention) 메커니즘으로 인해 컨텍스트 길이가 길어질수록 연산량이 기하급수적으로 증가함 \["Directly extending the context length of transformers incurs a quadratic increase in computational time and memory cost due to the transformer architecture’s self-attention mechanism.", Section 1, p.2].
- 기존 연구들은 컨텍스트 윈도우를 확장하는 방향으로 문제를 해결하려 했으나, 연산 비용 문제와 컨텍스트 활용의 한계로 인해 실용적이지 않음 \["Recent research shows that long-context models struggle to utilize additional context effectively.", Section 1, p.2].
- 이에 따라 OS의 가상 메모리 페이징 개념을 차용하여, LLM이 효과적으로 컨텍스트를 관리할 수 있는 새로운 접근 방식이 필요함 \["Our approach borrows from the idea of virtual memory paging that was developed to enable applications to work on datasets that far exceed the available memory.", Section 1, p.3].
## Background

- 기존 LLM은 컨텍스트 윈도우 내에서만 정보를 유지할 수 있으며, 이를 넘어서면 이전 데이터를 잃어버리게 됨 \["Despite these advances, the limited fixed-length context windows used by LLMs significantly hinders their applicability to long conversations or reasoning about long documents.", Section 1, p.2].
- Transformer 모델에서 컨텍스트 윈도우를 확장하면 연산 비용이 기하급수적으로 증가하여 실용성이 떨어짐 \["Scaling up context length results in quadratic memory and computational costs, making it impractical for real-world applications.", Section 1, p.3].
- OS의 가상 메모리 관리 기법(페이징, 계층적 메모리 시스템)은 제한된 물리적 메모리 내에서 큰 데이터 세트를 다룰 수 있도록 설계되었으며, 이를 LLM에 적용할 수 있음 \["We treat context windows as a constrained memory resource, and design a memory hierarchy for LLMs analogous to memory tiers used in traditional OSes.", Section 2, p.4].

## Targeting Problems

- **제한된 컨텍스트 윈도우 문제**: 기존 LLM은 긴 문서나 대화를 처리할 때 과거 정보를 잃어버리는 한계가 있음 \[Section 1, p.2].
- **연산 비용 증가 문제**: 컨텍스트 길이를 직접 늘리면 연산량이 급증하여 실용성이 떨어짐 \[Section 1, p.3].
- **장기적인 정보 유지 문제**: 다중 세션 대화에서 일관성을 유지하는 것이 어렵고, 문서 분석에서 관련 정보를 연결하는 것이 어려움 \[Section 3.1, p.13]

## Suggestions / Methods

### 1. 가상 컨텍스트 관리 (Virtual Context Management)

- OS의 가상 메모리 페이징 개념을 활용하여, LLM이 자동으로 컨텍스트를 유지하고 필요한 정보를 불러오도록 설계 \["Using function calls, LLM agents can read and write to external data sources, modify their own context, and choose when to return responses to the user.", Section 2.1, p.5].

- 메모리 계층 구조를 활용한 효율적인 컨텍스트 관리
	1. **Main Context** (단기 메모리): LLM의 컨텍스트 윈도우 내에서 직접 접근 가능한 데이터
	2. **Recall Storage** (중기 메모리): 최근 사용한 데이터를 저장하는 계층, 자주 참조되는 정보 보관
	3. **Archival Storage**(장기 메모리): 장기간 유지해야 하는 데이터 저장소
		\["MemGPT enables the LLM to retrieve relevant historical data missing from what is placed in-context, and also evict less relevant data from context into external storage systems.", Section 2.2, p.6]
	


### 2. 기능 호출을 통한 컨텍스트 관리 (Function Calls for Context Management)

- LLM이 자체적으로 컨텍스트를 관리할 수 있도록 함수 호출 기능을 도입하여, 컨텍스트 내부 데이터를 업데이트하고, 필요할 때 외부 저장소에서 데이터를 검색할 수 있도록 설계 \["Memory edits and retrieval are entirely self-directed: MemGPT autonomously updates and searches through its own memory based on the current context.", Section 2.3, p.7].

### 3. 다중 문서 분석 및 다중 세션 대화 지원

- 긴 문서를 분석할 때, 관련 정보를 연결하여 효율적으로 검색할 수 있도록 설계 \["MemGPT actively retrieves documents from its archival storage, allowing it to scale to larger effective context lengths.", Section 3.2, p.16].
    
- 다중 세션 챗봇에서 사용자와의 대화를 장기적으로 유지하고, 이전 대화를 기억할 수 있도록 구현 \["MemGPT enables maintaining long-term memory, consistency, and evolvability over extended dialogues.", Section 3.1, p.14].

###### 장점
1. Mini-Batch 기반 처리 최적화
	- 긴 문장을 처리할 때 **Batch Processing 기법을 활용하여** 연산량을 줄이고 속도를 향상.
	- LLM이 처리할 데이터 블록을 미리 분할하여 병렬로 연산을 수행하도록 구성.
2.장기 기억 활용
