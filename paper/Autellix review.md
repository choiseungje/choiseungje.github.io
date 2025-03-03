
# <font color="#31859b">Summary</font>

## 1-Line

```
Autellix는 LLM 기반 에이전트 프로그램의 성능을 최적화하는 서빙 엔진으로, 프로그램 수준의 스케줄링 및 로드 밸런싱을 통해 처리량을 최대 15배 향상시킨다. [Abstract, p.1]
```
## 3-Line

```
기존 LLM 서빙 시스템은 개별 요청 단위의 최적화에 집중했으나, Autellix는 프로그램 전체 실행 시간을 최소화하는 새로운 스케줄링 기법을 제안한다. [Section 1, p.2]
```

```
단일 및 다중 스레드 프로그램을 위한 스케줄링 알고리즘(PLAS, ATLAS)을 개발하여, 대기 시간과 실행 시간을 최소화하였다. [Section 2, p.4]
```

```
실험을 통해 Autellix는 vLLM 대비 프로그램 처리량을 4~15배 증가시키며, 다중 엔진 환경에서의 데이터 로컬리티를 고려한 로드 밸런싱 기법이 효과적임을 입증하였다. [Section 6, p.12]
```
## 5-Line

```
기존 LLM 서빙 시스템(vLLM 등)은 개별 LLM 호출을 최적화하지만, 프로그램 수준의 종합적인 최적화는 수행하지 못한다. [Section 1, p.2]
```

```
Autellix는 프로그램 내 호출 의존성을 고려하여, 대기 시간을 줄이고 실행 성능을 개선하는 새로운 스케줄링 기법(PLAS, ATLAS)을 도입한다. [Section 2, p.4]
```

```
MLFQ 기반의 다중 큐 시스템을 활용하여, 장기간 대기하는 프로그램이 발생하지 않도록 설계되었다. [Section 4, p.7]
```

```
데이터 로컬리티를 고려한 로드 밸런싱 기법을 통해, 다중 엔진 환경에서도 성능을 최적화한다. [Section 5, p.10]
```

```
실험 결과, Autellix는 기존 vLLM 대비 4~15배 높은 처리량을 기록하며, 복잡한 에이전트 프로그램의 실행 성능을 획기적으로 향상시킨다. [Section 6, p.12]
```


# <font color="#7030a0">Paper Review</font>

## Abstract / Motivation

> 기존 LLM 서빙 시스템은 개별 호출을 최적화하는 데 집중했지만, 프로그램 내 호출 간 의존성을 고려하지 않아 최적화 기회를 놓치고 있다. 이러한 한계를 극복하기 위해 Autellix는 프로그램 전체 실행 시간을 최소화하는 새로운 서빙 엔진을 제안한다. \[p.1, Abstract]
- 기존 시스템(vLLM 등)은 개별 요청 단위의 최적화에 초점을 맞추지만, 이는 긴 프로그램의 실행을 지연시키는 **Head-of-Line (HoL) Blocking** 문제를 발생시킴. \[Section 1, p.2]
- 프로그램 단위의 최적화를 통해 전체 대기 시간을 줄이는 방식이 필요함. \[Section 1, p.2]

## Background

- 기존 LLM 서빙 엔진(vLLM)은 개별 LLM 요청을 최적화하는 방식으로, 키-값(KV) 캐시 효율성 개선과 요청 스케줄링 알고리즘을 적용했으나, 프로그램 단위의 최적화는 고려되지 않음. \[Section 2, p.3]
- 프로그램 수준의 최적화는 LLM 호출 간 의존성을 고려해야 하며, 이를 통해 전체 실행 시간을 줄이는 것이 가능함. \[Section 2, p.4]
## Targeting Problems

- **Head-of-Line (HoL) Blocking**: 긴 LLM 호출이 짧은 호출을 지연시키는 문제. \[Section 3, p.6]
- **Program-Level Blocking**: 기존 스케줄러는 개별 요청의 우선순위를 고려하지만, 같은 프로그램 내에서 호출 간 의존성을 고려하지 않아 프로그램 실행이 지연됨. \[Section 3, p.7]
- **Load Balancing & Data Locality**: 다중 엔진 환경에서 요청을 무작위로 분배할 경우 KV 캐시 미스가 증가하여 성능 저하 발생. \[Section 5, p.10]

## Suggestions / Methods

#### 1. Program-Level Scheduling
- **PLAS (Program-Level Attained Service)**: 단일 스레드 프로그램을 위한 스케줄링 기법으로, 전체 실행 시간을 고려하여 우선순위를 조정함. \[Section 4, p.8]
- **ATLAS (Adaptive Thread-Level Attained Service)**: 다중 스레드 프로그램의 경우, 크리티컬 패스를 분석하여 최적의 우선순위를 결정함. \[Section 4, p.9]
#### 2. Preemptive Scheduling & Anti-Starvation
- 다단계 우선순위 큐(Multilevel Feedback Queue, MLFQ) 적용하여 대기 시간 최소화. \[Section 4, p.10]
	대기 시간 및 처리 시간을 고려하여 우선 순위를 매김
- 장기간 대기한 프로그램의 우선순위를 조정하여 **Starvation 문제 방지**. \[Section 4, p.11]
#### 3. Load Balancing with Data Locality
- 짧은 요청(≤2048 토큰)은 가장 부하가 적은 엔진으로 분산.
- 긴 요청은 같은 프로그램의 KV 캐시를 공유할 수 있도록 특정 엔진에 고정하여 배정. \[Section 5, p.12]


##### 장점
- Autellix는 vLLM 대비 **4~15배 높은 처리량**을 기록하며, **응답 속도를 단축**함. \[Section 6, p.13]
- 프로그램별 처리량 증가 및 대기 시간 단축 효과가 입증됨. \[Section 6, p.15]
- 다중 엔진 환경에서 **데이터 로컬리티 기반 로드 밸런싱**을 적용한 결과, KV 캐시 미스가 감소하여 성능이 향상됨. \[Section 6, p.17]


