# Elsa Core 전체 아키텍처 및 코드 분석 리뷰

이 문서는 Elsa Core (`elsa-core`) 프로젝트의 전체 구조와 핵심 모듈, 그리고 주요 파일들에 대한 심층 리뷰를 제공합니다.

---

## 🏗️ 1. 핵심 워크플로 엔진 (Core Workflow Engine)
Elsa의 심장부에 해당하는 레이어로, 워크플로의 정의, 실행, 상태 관리를 담당합니다.

### 📂 Elsa.Workflows.Core
워크플로의 기본 모델과 추상화 레이어를 제공합니다.
*   **WorkflowRunner.cs**: 워크플로 실행의 최상위 진입점입니다. (Strategy & Facade 패턴)
*   **ActivityInvoker.cs**: 개별 활동(Activity)의 실행 단위를 격리하여 처리합니다. (Middleware 패턴)
*   **WorkflowExecutionContext.cs**: 런타임 데이터의 'Single Source of Truth' 역할을 수행합니다.
*   **IActivity.cs**: 모든 활동의 기본 계약을 정의하는 핵심 인터페이스입니다.
*   **Variable.cs**: 타입 세이프한 데이터 저장 및 전달을 위한 추상화 객체입니다.
*   **IActivityScheduler.cs**: 실행 순서 및 우선순위를 결정하는 큐 관리자입니다.
*   **WorkflowGraphBuilder.cs**: 정의를 그래프 구조(DAG)로 변환하는 빌더입니다.

### 📂 Elsa.Workflows.Runtime
워크플로의 실제 실행 환경과 북마크 관리, 트리거 시스템을 담당합니다.
*   **WorkflowRuntime.cs**: 인스턴스 생명주기 관리 및 실행 트리거링의 중심입니다.
*   **BookmarkManager.cs**: 비동기 대기 지점(Bookmark)의 생성, 저장 및 재개를 관리합니다.
*   **TriggerManager.cs**: 특정 이벤트 발생 시 워크플로를 시작시키는 트리거 로직을 처리합니다.
*   **StimulusHandler.cs**: 외부 자극(Stimulus)을 받아 적절한 워크플로를 재개시키는 매커니즘입니다.

### 📂 Elsa.Workflows.Management
워크플로 정의의 저장, 버전 관리 및 UI를 위한 메타데이터를 제공합니다.
*   **WorkflowDefinitionManager.cs**: 워크플로 정의의 CRUD 및 버전 관리를 담당합니다.
*   **ActivityDescriptor.cs**: 활동의 메타데이터를 추출하여 Studio UI에 정보를 제공합니다.
*   **WorkflowPublisher.cs**: 워크플로의 게시(Publish) 및 회수(Retract) 프로세스를 관리합니다.

---

## 🧬 2. 표현식 및 데이터 바인딩 (Expressions & Data Binding)
워크플로 내부에서 동적인 값 계산을 가능하게 하는 유연한 엔진 레이어입니다.

### 📂 Elsa.Expressions
표현식 평가의 기본 프레임워크를 제공합니다.
*   **ExpressionEvaluator.cs**: 다양한 구문(Syntax)의 표현식을 평가하는 통합 서비스입니다.
*   **LiteralEvaluator / VariableEvaluator**: 상수 값 및 변수 값을 처리하는 기본 평가기입니다.

### 📂 언어별 확장 모듈
*   **Elsa.Expressions.CSharp**: C# 스크립트 기반의 표현식 평가를 지원합니다. (Roslyn 활용)
*   **Elsa.Expressions.JavaScript**: Jint 라이브러리를 통해 JavaScript 표현식을 지원합니다.
*   **Elsa.Expressions.Liquid**: Fluid 라이브러리를 활용한 Liquid 템플릿 처리를 지원합니다.

---

## 💾 3. 영속성 레이어 (Persistence Layer)
워크플로의 상태와 정의를 안정적으로 저장하기 위한 추상화 및 구현체입니다.

### 📂 Elsa.Persistence.EFCore
Entity Framework Core를 사용한 기본 영속성 구현입니다.
*   **ElsaDbContext.cs**: 워크플로 정의, 인스턴스, 북마크 등에 대한 DB 스키마를 정의합니다.
*   **EFCoreWorkflowDefinitionStore.cs**: DB를 통한 정의 저장소 구현체입니다.
*   **Migrations**: Sqlite, SqlServer, PostgreSql, MySql 등 각 DB별 마이그레이션 스크립트를 포함합니다.

---

## 🌐 4. 부가 기능 및 통합 모듈 (Extended Modules)
엔진의 기능을 확장하는 강력한 부가 기능들입니다.

### 📂 주요 통합 모듈
*   **Elsa.Http**: HTTP 요청 수신(Trigger) 및 발신(Activity) 기능을 제공합니다. ASP.NET Core 미들웨어와 통합됩니다.
*   **Elsa.Identity**: 워크플로 API 및 Studio 접근 제어를 위한 인증/인가 시스템입니다.
*   **Elsa.Scheduling**: Quartz.NET 또는 Hangfire를 연동하여 시간 기반 워크플로 실행을 관리합니다.
*   **Elsa.Tenants**: 멀티테넌시 환경을 지원하여 테넌트별 리소스 격리를 보장합니다.
*   **Elsa.Caching**: 성능 최적화를 위한 메모리 및 분산 캐싱 전략을 제공합니다.

---

## 🛠️ 5. 유틸리티 및 공통 (Common & Utilities)
프로젝트 전반에서 사용되는 공통 라이브러리입니다.
*   **Elsa.Common**: ID 생성기, 날짜/시간 추상화, 직렬화 유틸리티 등을 포함합니다.
*   **Elsa.Alterations**: 실행 중인 워크플로 인스턴스의 구조를 변경(Hot-fix 등)할 수 있는 고급 기능을 지원합니다.

---

---

*이 문서는 Elsa Core의 전체 코드베이스를 모듈별로 분석한 결과이며, 각 모듈은 독립적인 확장성과 결합도를 유지하도록 설계되어 있습니다.*

## 🏆 6. 핵심 코드 상세 리뷰 (Top 10)

### 1. WorkflowRunner.cs
- **기능**: 워크플로 실행의 최상위 진입점으로, 특정 워크플로 정의나 인스턴스를 구동하는 역할을 수행합니다.
- **디자인 패턴**: **Strategy Pattern** (동기/비동기 실행 전략 분리), **Facade Pattern** (복잡한 엔진 내부 로직을 단순화된 인터페이스로 제공).
- **중요성**: 시스템의 런타임 엔진 그 자체이며, 모든 워크플로 요청이 통과하는 병목 지점이자 핵심 실행기입니다.
- **상세 설명**: `WorkflowRunner`는 외부로부터 워크플로 정의(`WorkflowDefinition`)를 전달받아 `WorkflowExecutionContext`를 초기화합니다. 이후 `IActivityScheduler`와 협력하여 활동(Activity) 큐를 관리하며, 비동기 작업 시 중단점(Bookmark)을 생성하여 실행을 일시 중단하거나 재개하는 정교한 상태 제어 로직을 포함합니다.

### 2. ActivityInvoker.cs
- **기능**: 개별 활동(Activity)의 실행 단위를 격리하여 처리하고 관리하는 실행 핸들러입니다.
- **디자인 패턴**: **Middleware Pattern** (실행 전후 파이프라인 처리), **Decorator Pattern** (활동 실행에 부가적인 기능을 동적으로 추가).
- **중요성**: 활동의 실제 `ExecuteAsync` 메서드를 안전하게 호출하고, 예외 처리 및 결과 출력을 표준화하는 역할을 합니다.
- **상세 설명**: 활동 실행의 원자성을 보장하기 위해 `ActivityInvoker`는 활동 실행 전후에 'Activity Execution Pipeline'을 통과시킵니다. 이를 통해 로깅, 메트릭 수집, 입력 값 바인딩 및 출력 값 캡처가 통일된 방식으로 처리됩니다.

### 3. WorkflowExecutionContext.cs
- **기능**: 워크플로 실행 인스턴스의 현재 상태, 변수, 북마크, 활동 스택 등 모든 런타임 데이터를 보유하는 컨텍스트 객체입니다.
- **디자인 패턴**: **State Pattern** (워크플로의 현재 상태를 객체화), **Context Object Pattern** (실행 정보를 전파).
- **중요성**: 엔진 내부의 거의 모든 컴포넌트가 참조하는 'Single Source of Truth'이며, 실행 일관성을 유지하는 핵심입니다.
- **상세 설명**: 이 컨텍스트는 활동 간의 데이터 전달을 담당하는 `Variable` 저장소를 관리하며, 워크플로가 일시 중단될 때 `WorkflowState`로 직렬화될 기초 자료를 제공합니다. 또한 부모-자식 워크플로 간의 계층 구조 정보를 유지합니다.

### 4. IActivity.cs
- **기능**: 모든 워크플로 활동이 반드시 구현해야 하는 기본 인터페이스로, 활동의 행위와 메타데이터를 규정합니다.
- **디자인 패턴**: **Interface Segregation Principle** (활동의 핵심 계약 정의), **Command Pattern** (실행 가능한 명령 단위).
- **중요성**: Elsa 엔진의 확장성을 보장하는 핵심 인터페이스로, 누구나 이를 구현하여 커스텀 활동을 추가할 수 있습니다.
- **상세 설명**: 활동의 생명주기 메서드인 `ExecuteAsync`를 정의하며, 선언적 속성(Property)을 통해 입력/출력 핀을 명시합니다. 이 인터페이스를 통해 엔진은 구체적인 클래스 타입에 의존하지 않고 다형적으로 활동을 실행할 수 있습니다.

### 5. Variable.cs
- **기능**: 워크플로 내에서 데이터를 저장하고 전달하기 위한 추상화된 데이터 홀더입니다.
- **디자인 패턴**: **Proxy Pattern** (실제 값에 대한 접근 제어), **Type Safe Container**.
- **중요성**: 강력한 타입 시스템을 지원하여 워크플로 설계 시 데이터 흐름의 안정성을 보장합니다.
- **상세 설명**: `Variable`은 단순한 값 저장을 넘어, 표현식(Expression) 엔진과의 연동을 통해 동적인 값 계산을 지원합니다. 또한 워크플로의 다양한 스코프(Scope)에 따라 변수의 가시성과 생명주기를 관리합니다.

### 6. Bookmark.cs
- **기능**: 비동기 대기 지점을 나타내는 고유 식별자로, 외부 이벤트가 발생했을 때 중단된 워크플로를 다시 찾는 키 역할을 합니다.
- **디자인 패턴**: **Observer Pattern** (특정 이벤트 대기), **Memento Pattern** (복귀 지점 저장).
- **중요성**: 장기 실행 워크플로(Long-running workflows)의 핵심 메커니즘으로, 메모리 효율성을 극대화합니다.
- **상세 설명**: 활동이 실행 도중 `Wait` 상태가 되면 엔진은 `Bookmark`를 생성하여 데이터베이스에 저장합니다. 이후 HTTP 요청이나 타이머 신호가 들어오면 이 북마크를 매칭하여 정확한 워크플로 인스턴스의 실행 지점으로 복귀합니다.

### 7. WorkflowState.cs
- **기능**: 워크플로의 실행 상태를 영속화하기 위해 최적화된 직렬화 데이터 구조입니다.
- **디자인 패턴**: **Data Transfer Object (DTO)** (영속성 레이어 전송용), **Snapshot Pattern**.
- **중요성**: 데이터베이스 저장 및 복원(Hydration)의 효율성을 결정하며, 시스템 가용성을 보장합니다.
- **상세 설명**: `WorkflowExecutionContext`에서 런타임 전용 정보를 제외하고 상태 복구에 필요한 최소한의 데이터(변수 값, 북마크, 완료된 활동 목록 등)만을 추출하여 구성됩니다. JSON 또는 메시지 팩 형식으로 변환되어 저장됩니다.

### 8. IActivityScheduler.cs
- **기능**: 실행 대기 중인 활동들의 순서를 결정하고 우선순위를 관리하는 스케줄링 엔진입니다.
- **디자인 패턴**: **Strategy Pattern** (LIFO, FIFO 등 스케줄링 알고리즘 선택), **Queueing Pattern**.
- **중요성**: 워크플로의 실행 흐름(Flow control)을 제어하며, 병렬 실행 및 루프 처리의 정확성을 보장합니다.
- **상세 설명**: `WorkflowRunner`로부터 호출받아 실행할 활동을 스택이나 큐에 넣고, 엔진이 다음 단계를 결정할 때 가장 적절한 활동을 제공합니다. 이는 복잡한 그래프 탐색 알고리즘을 내포하고 있습니다.

### 9. ActivityDescriptor.cs
- **기능**: 활동의 속성, 이름, 카테고리 등 UI 렌더링 및 유효성 검사에 필요한 메타데이터를 정의합니다.
- **디자인 패턴**: **Metadata Pattern**, **Self-describing Component**.
- **중요성**: 코드로 작성된 활동과 시각화 디자이너(Studio) 사이의 브릿지 역할을 수행합니다.
- **상세 설명**: 리플렉션을 통해 활동 클래스를 분석하여 생성되거나, 명시적으로 정의된 속성 정보를 포함합니다. 이 정보는 Studio에서 활동 라이브러리를 구축하고 속성 편집창을 동적으로 생성하는 데 사용됩니다.

### 10. WorkflowGraphBuilder.cs
- **기능**: 선언적인 워크플로 정의(JSON 등)를 분석하여 실행 가능한 방향성 순환 그래프(DAG) 구조로 변환합니다.
- **디자인 패턴**: **Builder Pattern** (복잡한 그래프 구조 생성), **Composite Pattern** (계층적 활동 구조 처리).
- **중요성**: 설계 단계의 워크플로가 실행 엔진이 이해할 수 있는 객체 모델로 전환되는 핵심 변환 레이어입니다.
- **상세 설명**: 노드(Activity)와 엣지(Connection)를 파싱하여 관계를 설정하고, 각 활동 간의 부모-자식 관계 및 트리 구조를 유효성 검사합니다. 이 과정에서 순환 참조 여부나 도달 불가능한 노드를 사전에 식별합니다.
