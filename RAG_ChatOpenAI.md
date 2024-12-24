### ChatOpenAI

OpenAI 사의 채팅 전용 Large Language Model(llm) 입니다.

객체를 생성할 때 다음을 옵션 값을 지정할 수 있습니다. 옵션에 대한 상세 설명은 다음과 같습니다.

`temperature`

- 사용할 샘플링 온도는 0과 2 사이에서 선택합니다. 0.8과 같은 높은 값은 출력을 더 무작위하게 만들고, 0.2와 같은 낮은 값은 출력을 더 집중되고 결정론적으로 만듭니다.
- 창의적인 정도 예르들어 마케팅의경우 높아야 창의적인 답이나오고, 서류기반 정확한 답을 원하면 낮게 설정해야한다.

`max_tokens`

- 채팅 완성에서 생성할 토큰의 최대 개수입니다.

`model_name`: 적용 가능한 모델 리스트

- `gpt-4o`
- `gpt-4o-mini`
- `o1-preview`, `o1-preview-mini`: tier5 계정 이상만 사용 가능. $1,000 이상 충전해야 tier5 계정이 됩니다.

```python

# langchain_openai 패키지
# gpt를 쉽게 사용할 수 있도록 만듬
from langchain_openai import ChatOpenAI

# 객체 생성
llm = ChatOpenAI(
    temperature=0.1,  # 창의성 (0.0 ~ 2.0)
    model_name="gpt-4o-mini",  # 모델명
)

# 질의내용
question = "대한민국의 수도는 어디인가요?"

# 질의
print(f"[답변]: {llm.invoke(question)}")

```
### 답변의 형식(AI Message) 객체에 대해서

```python

# 질의내용
question = "대한민국의 수도는 어디인가요?"

# 질의
# invoke 원하고자하는 답변을 넣어준다
response = llm.invoke(question)

# 답변을 얻기위한 과정을 얻을 수 있다.
response
# AIMesseage라는 객체를 가져다 준다.
# content 라는 키 : 답변
# response_metadata 라는 답변을 준다.

```
- AIMessage객체

```bash
AIMessage(content='대한민국의 수도는 서울입니다.', additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 8, 'prompt_tokens': 16, 'total_tokens': 24, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_3de1288069', 'finish_reason': 'stop', 'logprobs': None}, id='run-0855d18f-9dcc-49c2-bfce-e41eb2cbb488-0', usage_metadata={'input_tokens': 16, 'output_tokens': 8, 'total_tokens': 24, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})
```

- response.content : 답변 출력
- response.response_metadata : 토큰 사용량


### LogProb 활성화

- 주어진 텍스트에 대한 모델의 **토큰 확률의 로그 값** 을 의미
- 토큰이란 문장을 구성하는 개별 단어나 문자 등의 요소를 의미하고, 확률은 **모델이 그 토큰을 예측할 확률**


```python
# 객체 생성
llm_with_logprob = ChatOpenAI(
    temperature=0.1,  # 창의성 (0.0 ~ 2.0)
    max_tokens=2048,  # 최대 토큰수
    model_name="gpt-4o-mini",  # 모델명
).bind(logprobs=True)
# 토큰의 확률값을 준다

# 질의내용
question = "대한민국의 수도는 어디인가요?"

# 질의
response = llm_with_logprob.invoke(question)

# 결과 출력
response.response_metadata
# 0의 확률일수록 작은 값을 갖는다.
```

### 스트리밍 출력

- 스트리밍 옵션은 질의에 대한 답변을 실시간으로 받을 때 유용합니다.
- 스트림 방식으로 질의
- answer 에 스트리밍 답변의 결과를 받습니다.
- 스트리밍 : 한개의 토큰별로 찍어서 알려준다.

```python

# BaseChatModel stream 형식을 갖는다.
answer = llm.stream("대한민국의 아름다운 관광지 10곳과 주소를 알려주세요!")

# 출력을 위해 다음과 같은 방식으로 진행해야한다.

streaming_answer = "
for token in answer:
    print(token.content, end = "", flush = True)
    final_answer += token.content

```


## 프롬프트 캐싱

- 참고 링크: https://platform.openai.com/docs/guides/prompt-caching

- 프롬프트 캐싱 기능을 활용하면 반복하여 동일하게 입력으로 들어가는 토큰에 대한 비용을 아낄 수 있습니다.

- 다만, 캐싱에 활용할 토큰은 고정된 PREFIX 를 주는 것이 권장됩니다.

- 아래의 예시에서는 `<PROMPT_CACHING>` 부분에 고정된 토큰을 주어 캐싱을 활용하는 방법을 설명합니다.
