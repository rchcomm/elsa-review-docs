# Elsa Apps 전체 아키텍처 및 코드 분석 리뷰

이 문서는 Elsa의 다양한 호스팅 시나리오를 보여주는 `elsa-apps` 프로젝트의 전체 구조와 실제 서버 구동 방식에 대한 심층 리뷰를 제공합니다.

---

## 🏗️ 1. 참조 서버 호스트 (Reference Server Hosts)
Elsa 엔진을 독립적인 서비스로 구동하기 위한 표준 구성입니다.

### 📂 Elsa.Server
워크플로 엔진의 모든 API와 백엔드 로직이 통합된 메인 서버 프로젝트입니다.
*   **Program.cs**: ASP.NET Core 기반의 서버 구동 및 서비스 의존성 주입(DI) 설정 지점입니다.
*   **Endpoints**: HTTP 요청을 통해 워크플로를 시작하거나 관리할 수 있는 API 엔드포인트들을 노출합니다.

### 📂 Elsa.Server.Shared
다양한 서버 호스트에서 공통으로 사용되는 인프라 설정 및 미들웨어 구성을 포함합니다.
*   **ElsaServerExtensions**: `AddElsa` 및 관련 옵션 설정을 간편화하는 확장 메서드를 제공합니다.
*   **CommonFilters**: API 요청에 대한 공통 필터링 및 보안 정책을 정의합니다.

---

## 🎨 2. 통합 스튜디오 호스트 (Integrated Studio Hosts)
백엔드 서버와 프론트엔드 Studio UI를 하나의 프로젝트에서 서빙하는 구성입니다.

### 📂 Elsa.Server.Studio (Blazor Server / Wasm)
워크플로 엔진과 시각적 디자이너를 결합한 관리자용 통합 포탈입니다.
*   **BlazorServer**: 모든 UI 상호작용이 서버에서 처리되어 실시간 상태 반영에 유리한 구성입니다.
*   **BlazorWasm**: 클라이언트 브라우저에서 UI가 동작하며 서버와는 REST API로 통신하는 분리형 아키텍처입니다.

---

## 💾 3. 배포 및 설정 (Deployment & Configuration)
실제 운영 환경에 맞춘 유연한 설정 방식을 제공합니다.

### 📂 설정 파일 및 전략
*   **appsettings.json**: 데이터베이스 연결 문자열, 인증 정책, 확장 모듈 활성화 여부 등을 관리합니다.
*   **NuGet.Config**: Elsa의 내부 패키지 및 확장 패키지 참조를 위한 최적화된 구성을 포함합니다.

---

*이 문서는 Elsa를 사용하여 실제 비즈니스 애플리케이션을 구축할 때의 표준 설계 패턴을 분석한 결과입니다.*
