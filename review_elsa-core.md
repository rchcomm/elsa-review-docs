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

*이 문서는 Elsa Core의 전체 코드베이스를 모듈별로 분석한 결과이며, 각 모듈은 독립적인 확장성과 결합도를 유지하도록 설계되어 있습니다.*
