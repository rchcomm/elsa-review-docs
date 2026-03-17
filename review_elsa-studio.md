# Elsa Studio 전체 아키텍처 및 코드 분석 리뷰

이 문서는 Elsa Studio (`elsa-studio`) 프로젝트의 전체 구조와 Blazor 기반 UI 아키텍처, 핵심 모듈들에 대한 심층 리뷰를 제공합니다.

---

## 🏗️ 1. UI 프레임워크 및 기반 구조 (Framework & Infrastructure)
Elsa Studio는 모듈형 Blazor 애플리케이션으로 설계되어 확장성이 뛰어납니다.

### 📂 Elsa.Studio.Framework
UI 모듈 시스템과 공통 서비스를 제공하는 뼈대입니다.
*   **IModule.cs**: 각 UI 모듈이 구현해야 하는 인터페이스로, 메뉴 등록 및 서비스 설정을 담당합니다.
*   **NavigationManager**: 복잡한 UI 내에서의 탐색 로직을 관리합니다.
*   **ThemeManager**: MudBlazor 등을 활용한 UI 테마 및 스타일링을 총괄합니다.

### 📂 Elsa.Studio.Hosts
실제 애플리케이션이 구동되는 호스트 환경입니다.
*   **BlazorServer**: 서버 사이드 렌더링 방식의 호스트입니다.
*   **BlazorWasm**: 클라이언트 사이드(WebAssembly) 방식의 호스트입니다.

---

## 🎨 2. 워크플로 디자이너 (Workflow Designer)
사용자가 시각적으로 워크플로를 설계할 수 있게 돕는 핵심 컴포넌트입니다.

### 📂 Elsa.Studio.Workflows.Designer
X6 또는 Mermaid와 같은 다이어그램 라이브러리를 Blazor와 연동하여 구현한 디자이너 영역입니다.
*   **WorkflowDesigner.razor**: 메인 캔버스 컴포넌트입니다. 활동 배치 및 연결선을 관리합니다.
*   **ActivityNode.cs**: 캔버스 상의 각 활동 노드에 대한 렌더링 및 상태 관리 객체입니다.
*   **DesignerInteractions.js**: 드래그 앤 드롭, 줌/팬 등 복잡한 캔버스 조작을 위한 JavaScript 상호작용 레이어입니다.

### 📂 Elsa.Studio.Workflows.Core
UI에서 워크플로 데이터를 다루기 위한 클라이언트 측 비즈니스 로직입니다.
*   **WorkflowDefinitionService.cs**: Elsa 서버 API와 통신하여 워크플로 정의를 가져오고 저장합니다.
*   **ActivityDisplayManager.cs**: 활동의 아이콘, 색상, 설명 등 UI 표현 방식에 대한 정보를 관리합니다.

---

## 🔐 3. 인증 및 보안 (Authentication & Security)
서버의 Identity 시스템과 연동하여 안전한 관리 환경을 제공합니다.

### 📂 Elsa.Studio.Authentication.ElsaIdentity
Elsa의 기본 인증 시스템과의 연동을 담당합니다.
*   **ElsaAuthProvider.cs**: Blazor의 `AuthenticationStateProvider`를 구현하여 현재 사용자의 인증 상태를 추적합니다.
*   **Login.razor**: 사용자 로그인을 위한 UI 컴포넌트입니다.
*   **OpenIdConnect 통합**: 표준 OIDC 프로토콜을 통한 외부 인증 서버 연동 기능을 포함합니다.

---

## 📦 4. 주요 UI 기능 모듈 (Feature Modules)
워크플로 관리 외에 관리자가 필요로 하는 다양한 기능적 UI입니다.

### 📂 Elsa.Studio.Workflows (Management UI)
*   **WorkflowInstances**: 실행된 워크플로 인스턴스의 목록과 각 인스턴스의 실행 로그를 확인합니다.
*   **WorkflowDefinitions**: 워크플로 정의 리스트를 관리하고 버전 히스토리를 시각화합니다.

### 📂 기타 모듈
*   **Elsa.Studio.Dashboard**: 전체 시스템 상태, 활성 인스턴스 통계 등을 요약하여 보여줍니다.
*   **Elsa.Studio.Labels**: 워크플로에 할당된 라벨(태그)을 관리하는 UI입니다.
*   **Elsa.Studio.Environments**: 멀티 환경 지원을 위한 설정 관리 화면입니다.

---

## 🛠️ 5. UI 가이드 및 인터렉션 (UI Hints & Hints)
서버에서 정의된 메타데이터를 기반으로 UI를 동적으로 생성하는 메커니즘입니다.

### 📂 Elsa.Studio.UIHints
활동의 속성 편집창을 동적으로 구성하는 컴포넌트 집합입니다.
*   **SingleLineHandler / CheckboxHandler**: 텍스트 입력, 체크박스 등 기본 입력 필드 처리기입니다.
*   **DropdownHandler**: 서버에서 받아온 옵션 목록을 드롭다운으로 렌더링합니다.

---

---

*이 문서는 Elsa Studio의 모듈형 Blazor 아키텍처를 분석한 결과이며, 각 컴포넌트는 프레임워크 수준의 추상화를 통해 플러그인 방식으로 확장 가능하게 설계되어 있습니다.*

## 🏆 6. 핵심 코드 상세 리뷰 (Top 10)

### 1. FlowchartDesigner.razor.cs
- **기능**: 워크플로 디자이너의 핵심 로직 및 캔버스 상호작용 관리
- **디자인 패턴**: **Component-based Architecture**
- **중요성**: 사용자가 워크플로를 시각적으로 설계하는 가장 핵심적인 인터페이스입니다.
- **상세 설명**: X6 라이브러리를 기반으로 노드 추가, 삭제, 연결 및 줌/팬 기능을 처리하며 워크플로 모델과 동기화합니다.

### 2. ApiClientFactory.cs
- **기능**: 백엔드 API 통신을 위한 HttpClient 생성 및 관리
- **디자인 패턴**: **Factory Pattern**
- **중요성**: 서버와의 모든 통신 통로를 일관되게 생성합니다.
- **상세 설명**: 인증 토큰 및 베이스 주소를 설정하여 각 모듈이 필요로 하는 API 클라이언트를 동적으로 생성합니다.

### 3. DefaultUIHintService.cs
- **기능**: 액티비티 속성에 적합한 UI 컴포넌트 렌더링 힌트 제공
- **디자인 패턴**: **Registry Pattern**
- **중요성**: 액티비티 속성에 따라 적절한 입력 폼(Dropdown, Code Editor 등)을 동적으로 구성합니다.
- **상세 설명**: 등록된 핸들러를 검색하여 특정 속성 타입에 맞는 UI 컴포넌트를 매핑해줍니다.

### 4. DefaultMenuService.cs
- **기능**: 사이드바 및 상단 메뉴 관리
- **디자인 패턴**: **Composite Pattern**
- **중요성**: 스튜디오의 내비게이션 구조를 동적으로 확장 가능하게 합니다.
- **상세 설명**: 각 모듈이 자신의 메뉴 아이템을 등록할 수 있도록 하며 계층 구조를 관리합니다.

### 5. DefaultBackendApiClientProvider.cs
- **기능**: 활성화된 백엔드 서비스 클라이언트 제공
- **디자인 패턴**: **Strategy Pattern**
- **중요성**: 다중 백엔드 환경에서 현재 선택된 서버와의 연결을 보장합니다.
- **상세 설명**: 사용자 설정에 따라 적절한 백엔드 엔드포인트를 선택하고 통신 객체를 반환합니다.

### 6. DefaultActivityTabRegistry.cs
- **기능**: 액티비티 편집창의 탭 정보 관리
- **디자인 패턴**: **Registry Pattern**
- **중요성**: 액티비티의 상세 설정을 다각도(Settings, Properties 등)로 분류합니다.
- **상세 설명**: 특정 액티비티에 대해 추가적인 커스텀 탭을 표시할 수 있도록 등록 및 조회 기능을 제공합니다.

### 7. DefaultMediator.cs
- **기능**: 컴포넌트 간 비동기 메시지 전달
- **디자인 패턴**: **Mediator Pattern**
- **중요성**: 컴포넌트 간의 직접적인 참조를 줄여 결합도를 낮춥니다.
- **상세 설명**: 특정 이벤트 발생 시 구독 중인 다른 컴포넌트들에게 알림을 전달하여 상태를 동기화합니다.

### 8. App.razor.cs
- **기능**: Blazor 어플리케이션의 엔트리포인트 및 레이아웃 제어
- **디자인 패턴**: **Root Component**
- **중요성**: 어플리케이션의 초기화 및 전역 상태(테마, 인증 등)를 관리합니다.
- **상세 설명**: 라우팅 처리 및 쉘 레이아웃을 구성하며 앱 시작 시 필요한 서비스를 로드합니다.

### 9. ActivityMapper.cs
- **기능**: 백엔드 액티비티 모델과 디자이너용 모델 간 매핑
- **디자인 패턴**: **Data Mapper**
- **중요성**: 서버의 데이터 모델과 UI의 시각적 모델 사이의 간극을 메웁니다.
- **상세 설명**: JSON 직렬화된 액티비티 데이터를 디자이너가 이해할 수 있는 시각적 객체로 변환합니다.

### 10. DefaultThemeService.cs
- **기능**: 사용자 테마(Light/Dark) 및 색상 스킴 관리
- **디자인 패턴**: **Observer Pattern**
- **중요성**: 사용자 경험(UX)의 일관성을 유지하고 개인화된 환경을 제공합니다.
- **상세 설명**: 현재 테마 상태를 저장하고 변경 시 UI에 즉시 반영되도록 이벤트를 발생시킵니다.
