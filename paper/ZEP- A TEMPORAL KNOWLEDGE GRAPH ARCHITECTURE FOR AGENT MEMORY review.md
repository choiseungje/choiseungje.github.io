
# <font color="#31859b">Summary</font>

## 1-Line

```
Zep은 동적 지식 그래프를 활용하여 AI 에이전트의 장기 기억을 개선하는 새로운 메모리 계층 서비스로, 기존 MemGPT 대비 우수한 성능을 보인다 [Abstract, p.1].
```
## 3-Line

```
Zep은 정적인 문서 검색에 의존하는 기존 RAG(Retrieval-Augmented Generation) 시스템의 한계를 극복하고, Graphiti라는 시간 인식 지식 그래프 엔진을 활용하여 대화 데이터 및 구조화된 데이터를 동적으로 통합한다 [Abstract, p.1].
```

```
DMR 벤치마크에서 MemGPT보다 높은 94.8%의 성능을 기록했으며 [Abstract, p.2]
```

```
LongMemEval 벤치마크에서는 18.5% 향상된 정확도를 보였다 [Abstract, p.2].
```
## 5-Line

```
기존 RAG 시스템은 문맥 유지가 어렵고 실시간 데이터 업데이트가 불가능한 한계를 지닌다 [Abstract, p.1-2]
```

```
Zep은 이러한 문제를 해결하기 위해 Graphiti 엔진을 기반으로 한 동적 지식 그래프를 활용하여 장기 기억을 효과적으로 관리한다 [Abstract, p.2]
```

```
이를 통해 기업 환경에서의 세션 간 정보 통합 및 장기 문맥 유지가 가능해진다 [Abstract, p.2]
```

```
실험 결과, Zep은 MemGPT 대비 성능이 향상되었으며 [Introduction, p.2]
```

```
검색 속도를 90% 이상 단축하여 실시간 응답성을 대폭 개선했다 [Introduction, p.2].
```


# <font color="#7030a0">Paper Review</font>

## Abstract / Motivation

>기존 RAG 프레임워크는 정적인 문서 검색에 의존하여 실시간 데이터 통합이 어려운 문제가 있다 \[Abstract, p.1].
- 기존 AI 에이전트는 문맥 창 크기가 제한적이므로 장기적인 정보 보유가 어렵다.
- 기업 환경에서는 지속적으로 변화하는 대화 데이터 및 비즈니스 데이터를 활용한 동적 메모리가 필요하다.
- Zep은 Graphiti 엔진을 활용하여 동적 지식 그래프를 구축함으로써 이러한 문제를 해결한다.
>"While existing retrieval-augmented generation (RAG) frameworks for large language model (LLM)-based agents are limited to static document retrieval, enterprise applications demand dynamic knowledge integration from diverse sources including ongoing conversations and business data."\[Abstract, p.1]

## Background

- 기존 RAG 시스템은 정적인 문서 검색에 의존하며, 실시간 데이터 업데이트가 어렵다.
- Knowledge Graphs(KGs)는 RAG의 한계를 보완하기 위해 도입되었으며, 다양한 형태의 데이터를 통합하는 데 효과적이다.
- Graphiti 엔진은 기존 KG의 한계를 개선하여 시간 정보를 유지하는 동적 지식 그래프를 제공한다.
>"Current approaches using RAG have focused on broad domain knowledge and largely static corpora—that is, document contents added to a corpus seldom change."\[Introduction, p.2]

## Targeting Problems

- **정적 문서 검색의 한계**: 기존 RAG 시스템은 변화하는 데이터를 실시간으로 반영하지 못한다 \[Introduction, p.2].
- **LLM 문맥 창의 제약**: LLM이 다룰 수 있는 컨텍스트 길이가 제한적이므로 장기 문맥 유지가 어렵다 \[Introduction, p.2].
- **장기 문맥 유지의 어려움**: 기존 시스템은 세션 간 정보 연결이 어렵다 \[Introduction, p.3].

## Suggestions / Methods

### 1. Graphiti 기반 지식 그래프 구축
- **Graphiti**는 시간 인식이 가능한 지식 그래프 엔진으로, 대화 데이터 및 비즈니스 데이터를 실시간으로 업데이트함.
- 기존 정보와의 관계를 유지하며, 새로운 데이터가 추가될 경우 자동으로 업데이트.
- 세 개의 서브그래프 구조로 구성됨
	1. **Episode Subgraph** (Ge): 원시 대화 데이터를 저장하는 계층.
	2. **Semantic Entity Subgraph** (Gs): 대화에서 추출된 개체 간 관계를 구성하는 계층.
	3. **Community Subgraph** (Gc): 개체의 군집을 형성하고, 의미론적 연관성을 추출하는 계층.
> _"Graphiti KG engine dynamically updates the knowledge graph with new information in a non-lossy manner, maintaining a timeline of facts and relationships, including their periods of validity."\[Knowledge Graph Construction, p.3]

### 2. 시간 인식 모델 적용
-  대화 내 날짜 및 시간 정보를 활용하여 정보의 유효 기간을 추적합니다
- 새로운 정보가 추가될 경우, 기존 정보를 자동으로 무효화하여 최신 정보를 유지합니다.

### 3. 고성능 검색 및 검색 재정렬 (Retrieval & Reranking)
**검색 함수**

- 검색 기능은 다음과 같은 함수를 사용하여 표현됩니다 \[Memory Retrieval, p.5].
- 입력 쿼리 qqq 에 대해 관련성이 높은 엔티티 노드(NsN_sNs​), 커뮤니티 노드(NcN_cNc​), 그리고 의미적 엣지(EsE_sEs​)를 반환합니다 \[Memory Retrieval, p.5].

**재정렬**

- 검색 결과를 정렬하는 함수 ρ\rhoρ 를 사용합니다 \[Memory Retrieval, p.5].
- 여기서 ρ\rhoρ 는 검색 결과를 정렬하여 가장 관련성이 높은 정보를 우선 제공하는 역할을 합니다 \[Memory Retrieval, p.5].


##### 장점
1. #### **Deep Memory Retrieval (DMR) 평가**
	- **MemGPT 대비 94.8% 성능 향상 (gpt-4-turbo 기준)**
	- **세션 간 정보 연결 및 장기 문맥 유지에서 높은 정확도 기록**
2. #### **LongMemEval (LME) 평가**
	- **gpt-4o-mini 기준, 기존 방법 대비 15.2% 정확도 향상**
	- **gpt-4o 기준, 18.5% 성능 향상**


