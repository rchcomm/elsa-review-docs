# Elsa Copilot 전체 아키텍처 및 코드 분석 리뷰

이 문서는 AI 기반의 워크플로 자동화 보조 도구인 Elsa Copilot 프로젝트의 전체 구조와 지능형 워크플로 생성 매커니즘을 제공합니다.

---

## 🤖 1. 지능형 워크플로 생성 (AI Workflow Generation)
자연어 설명을 기반으로 실행 가능한 워크플로 구조를 제안하는 핵심 기능입니다.

### 📂 Copilot Core Modules
AI가 워크플로의 의도를 파악하고 이를 Elsa의 객체 모델로 변환하는 로직을 담당합니다.
*   **WorkflowGenerator.cs**: 프롬프트를 분석하여 적절한 활동(Activities)을 선택하고 이들의 연결 관계를 구성합니다.
*   **PromptTemplates**: 다양한 비즈니스 시나리오에 최적화된 프롬프트 템플릿들을 관리합니다.

---

## 🛠️ 2. 코파일럿 워크벤치 (Copilot Workbench)
사용자가 AI와 협업하여 워크플로를 다듬고 테스트할 수 있는 전용 작업 환경입니다.

### 📂 Elsa.Copilot.Workbench
독자적인 UI 환경을 제공하며, 워크플로의 실시간 시각화와 AI 수정을 지원합니다.
*   **WorkbenchUI**: 채팅 인터페이스와 디자이너 캔버스가 결합된 혁신적인 UI 구조를 가집니다.
*   **StateManagement**: AI가 제안한 변경 사항을 워크플로 스냅샷으로 관리하여 '이전 상태로 되돌리기' 등의 기능을 지원합니다.

---

## 🧬 3. LLM 연동 및 오케스트레이션 (LLM Integration)
외부의 강력한 언어 모델과의 통신을 추상화하고 관리합니다.

### 📂 Integration Modules
*   **OpenAIProvider**: OpenAI API(GPT-4 등)와의 통신 및 토큰 사용량 관리를 담당합니다.
*   **ContextBuilder**: 현재 워크플로의 구조와 사용할 수 있는 활동 목록을 AI에게 효율적으로 전달하기 위한 컨텍스트 압축 로직을 포함합니다.

---

## 🔍 4. 향후 확장성
Elsa Copilot은 단순한 생성을 넘어, 기존 워크플로의 오류 진단, 성능 최적화 제안 등 '지능형 관리자' 역할로 확장되도록 설계되었습니다.

---

*이 문서는 Elsa 생태계에서 AI가 어떻게 워크플로 생산성을 극대화하는지를 분석한 결과입니다.*

## 🏆 핵심 코드 상세 리뷰 (Top 10)

1.  **`src/Elsa.Copilot.Workbench/Program.cs`**: 코파일럿 전용 워크벤치 애플리케이션의 시작점입니다.
2.  **`src/Modules/Core/Elsa.Copilot.Modules.Core.Chat/Services/CopilotChatService.cs`**: AI 모델과 통신하여 워크플로 관련 답변을 생성하는 핵심 서비스입니다.
3.  **`src/Modules/Core/Elsa.Copilot.Modules.Core.Chat/Controllers/CopilotChatController.cs`**: 채팅 인터페이스를 위한 API 엔드포인트를 제공합니다.
4.  **`src/Modules/Studio/Elsa.Copilot.Modules.Studio.Chat/Services/StudioChatClient.cs`**: Elsa Studio 내에서 코파일럿 기능을 통합하기 위한 클라이언트 구현체입니다.
5.  **`src/Modules/Core/Elsa.Copilot.Modules.Core.Chat/Tools/GetWorkflowDefinitionTool.cs`**: AI가 현재 워크플로 정의를 이해할 수 있도록 돕는 도구(Tool)입니다.
6.  **`src/Modules/Core/Elsa.Copilot.Modules.Core.Chat/Tools/GetWorkflowInstanceStateTool.cs`**: 실행 중인 워크플로 인스턴스의 상태를 AI가 조회할 수 있게 하는 도구입니다.
7.  **`src/Elsa.Copilot.Workbench/Setup/ElsaServerSetup.cs`**: 코파일럿 환경에 최적화된 Elsa 서버 구성 설정입니다.
8.  **`src/Elsa.Copilot.Workbench/Setup/ElsaStudioSetup.cs`**: 코파일럿 기능을 포함한 Elsa Studio 구성 설정입니다.
9.  **`CHAT-MODULE-IMPLEMENTATION.md`**: 채팅 모듈의 설계 원칙과 구현 세부 사항을 다루는 기술 문서입니다.
10. **`functional-requirements.md`**: 코파일럿이 해결하고자 하는 기능적 요구 사항과 비즈니스 목표를 정의합니다.
