# Elsa Workbench 전체 아키텍처 및 작업 환경 분석 리뷰

이 문서는 Elsa 에코시스템의 통합 개발 및 패키징 환경인 `elsa-workbench` 프로젝트의 구조와 역할에 대한 심층 리뷰를 제공합니다.

---

## 🏗️ 1. 통합 개발 환경 (IDE & Solution Structure)
여러 하위 프로젝트와 솔루션을 체계적으로 관리하기 위한 최상위 워크벤치입니다.

### 📂 솔루션 구성 (Solutions)
*   **Elsa.Workbench.sln**: 전체 프로젝트를 아우르는 메인 솔루션 파일입니다.
*   **Elsa.Workbench.Server.sln**: 서버 사이드 구성 요소에 집중한 솔루션입니다.
*   **Elsa.Workbench.Studio.sln**: UI 및 스튜디오 구성 요소 전용 솔루션입니다.
*   **Elsa.Workbench.Packages.sln**: NuGet 패키징 및 배포를 위한 프로젝트 모음입니다.

---

## 📦 2. 패키징 및 배포 (Packaging & Distribution)
Elsa의 다양한 모듈들을 NuGet 패키지로 빌드하고 관리하기 위한 설정을 포함합니다.
*   **NuGet.Config**: 패키지 소스 및 의존성 관리 정책을 정의합니다.
*   **Directory.Build.targets**: 공통 빌드 로직과 패키징 메타데이터를 관리하여 일관된 버전 관리를 보장합니다.

---

## 🐳 3. 로컬 개발 오케스트레이션 (Local Orchestration)
개발자가 로컬 환경에서 전체 시스템(DB, 메시징 큐, 서버, UI)을 한 번에 구동할 수 있도록 돕습니다.
*   **docker-compose.yml**: SQL Server, Redis, RabbitMQ 등 Elsa 구동에 필요한 인프라 서비스를 컨테이너로 정의합니다.
*   **환경 설정**: 로컬 개발 시 필요한 환경 변수와 서비스 간 연결 설정을 총괄합니다.

---

## 🛠️ 4. 소스 코드 구조
*   **src/**: 워크벤치 전용 유틸리티나 통합 테스트 코드가 위치할 수 있습니다.
*   **submodules/**: 필요한 경우 다른 Elsa 저장소를 서브모듈로 포함하여 통합 빌드를 지원합니다.

---

*이 문서는 Elsa 프로젝트의 거대한 코드베이스를 효율적으로 개발하고 배포하기 위한 인프라 구조를 분석한 결과입니다.*
