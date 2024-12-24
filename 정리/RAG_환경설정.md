# 환경 설정

## OpenAPI 키발급 및 라이브러리 간단요약

- 직접 api키 연결하기

```python

```

- .env 파일에 api 키 관리하기
    - 프로젝트 루트 디렉토리에 `.env` 파일을 생성
    - 파일에 API 키를 다음 형식으로 저장
    - `OPENAI_API_KEY` 에 발급받은 API KEY 를 입력
    - `.env` 파일에 발급한 API KEY 를 입력


```python
# API KEY를 환경변수로 관리하기 위한 설정 파일
from dotenv import load_dotenv

# API KEY 정보로드
load_dotenv()
```

- dotenv 라이브러리는 환경변수를 .env에서 쉽게 관리할 수 있도록 도움을 준다.

- 본 정리에서 사용되는 라이브러리 버전 정리
```
langchain: 0.3.9
- LangChain의 핵심 라이브러리로, 다양한 LLM(대규모 언어 모델) 기반 애플리케이션 개발을 위한 프레임워크 제공. 체인 및 에이전트 설정을 지원.

langchain-core: 0.3.21
- LangChain의 주요 구성 요소와 데이터 처리 기능을 포함한 코어 라이브러리. 다양한 LLM 및 데이터 커넥터를 통합하는 데 사용.

langchain-experimental: 0.3.3
- 새롭거나 실험적인 기능을 테스트하고 활용하기 위한 확장 라이브러리. 안정성 검증 이전의 기능 제공.

langchain-community: 0.3.8
- 커뮤니티 기여를 통해 추가된 기능 및 모듈을 제공. 사용자 정의 및 외부 통합이 용이함.

langchain-openai: 0.2.10
- OpenAI API와의 통합을 제공. GPT 모델 및 관련 기능을 LangChain 환경에서 활용 가능.

langchain-teddynote: 0.3.33
- Teddynote 플랫폼과의 통합을 지원하여 특정 작업 환경에서의 데이터 및 AI 활용 최적화.

langchain-huggingface: 0.1.2
- Hugging Face 모델과의 통합을 지원. 사전 학습된 모델 및 커스텀 모델을 LangChain 내에서 활용 가능.

langchain-google-genai: 2.0.6
- Google GenAI API와의 통합을 제공. Google의 대규모 언어 모델 기능을 LangChain에서 사용할 수 있음.

langchain-anthropic: 0.2.4
- Anthropic의 Claude 모델과의 통합을 지원하여 자연어 처리 애플리케이션 개발 가능.

langchain-cohere: 0.3.3
- Cohere의 NLP 모델과 통합. 텍스트 생성, 분류, 임베딩 생성 등에 사용.

langchain-chroma: 0.1.4
- Chroma 벡터 데이터베이스와 통합하여 효율적인 벡터 검색 및 관리 지원.

langchain-elasticsearch: 0.3.0
- Elasticsearch와 통합을 지원하여 대규모 데이터 검색 및 분석 작업에 활용.

langchain-upstage: 0.3.0
- Upstage 플랫폼과의 통합을 지원하여 해당 플랫폼의 AI 기능을 LangChain에서 활용 가능.

langchain-milvus: 0.1.7
- Milvus 벡터 데이터베이스와 통합하여 대규모 벡터 데이터 검색 및 처리를 지원.

langchain-text-splitters: 0.3.2
- 텍스트 분할 기능을 제공하여 긴 텍스트를 처리하기 쉽게 분리하고 효율적으로 관리 가능.

```

- 간단하게 각 라이브러리 사용 요약


## Langsmith 

- 토큰 및 과금 확인, 그리고 어떤 프롬프팅 및 RAG의 출처까지 확인 가능한 라이브러리
- LangSmith 추적을 설정합니다. https://smith.langchain.com
- .env 파일에 LANGCHAIN_API_KEY를 입력합니다.

```python
import os
def langsmith(project_name=None, set_enable=True):
    if set_enable:

        result = os.environ.get(“LANGCHAIN_API_KEY”)

        if result is None or result.strip() == “”:
            print(
                “LangChain API Key가 설정되지 않았습니다.
            )
            return
        os.environ[“LANGCHAIN_ENDPOINT”] = (
            “https://api.smith.langchain.com”  # LangSmith API 엔드포인트
        )

        os.environ[“LANGCHAIN_TRACING_V2"] = “true”  # true: 활성화
        os.environ[“LANGCHAIN_PROJECT”] = project_name  # 프로젝트명
        print(f”LangSmith 추적을 시작합니다.\n[프로젝트명]\n{project_name}“)

    else:
        os.environ[“LANGCHAIN_TRACING_V2”] = “false”  # false: 비활성화
        print(“LangSmith 추적을 하지 않습니다.“)


def env_variable(key, value):
    os.environ[key] = value
    
# langhsmith 프로젝트 생성
langsmith("Project_Make") 
```




---
### 엔드포인트(Endpoint)

- 엔드포인트란, 네트워크 상에서 특정 서비스나 리소스에 접근하기 위한 ***접점(주소)***을 의미
- 주로 클라이언트와 서버 간의 통신에서 서버의 특정 기능이나 데이터를 호출하거나 접근하기 위해 사용

1. API의 엔드포인트:
    - 웹 API에서 엔드포인트는 특정 리소스나 기능을 호출하기 위한 URL의미
    - 클라이언트가 요청을 보낼 때, 서버는 이 엔드포인트를 통해 요청을 받고 응답을 제공
    - 예시 https://api.example.com/v1/users
    - 기본 도메인 : https://api.example.com
    - 특정 기준 : v1/users

2. 주요 특징 :
    - 주소: 엔드포인트는 리소스나 서비스에 접근하기 위한 고유 주소입니다.
    - 통신 방식: 주로 HTTP/HTTPS 프로토콜을 통해 동작합니다.
    - 목적: 특정 데이터를 가져오거나 서버의 기능을 호출하기 위한 명확한 접점 역할.