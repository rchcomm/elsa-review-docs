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

*이 문서는 Elsa Studio의 모듈형 Blazor 아키텍처를 분석한 결과이며, 각 컴포넌트는 프레임워크 수준의 추상화를 통해 플러그인 방식으로 확장 가능하게 설계되어 있습니다.*
