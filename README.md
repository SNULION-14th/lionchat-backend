# LionChat Backend

멋사 선배 사자에게 질문하면 Google Gemini가 답해주는 챗봇 백엔드.

## 기술 스택

- Backend: Django 5 + DRF + simplejwt
- LLM: Google Gemini (`google-generativeai`)
- Project name: `seminar` (작년 가이드와 동일)
- DB: SQLite (로컬) / MySQL (배포)
- WSGI: gunicorn

## 환경변수

`.env.example`를 복사해 `.env` 생성:

- `SECRET_KEY` — Django 시크릿 키
- `LLM_API_KEY` — Google Gemini API key

### Gemini API key 발급

1. <https://aistudio.google.com/app/apikey> 접속 (Google 계정 로그인)
2. "Create API key" 클릭
3. 발급된 키를 `.env`의 `LLM_API_KEY`에 붙여넣기
4. **무료 티어 사용** : Gemini 2.5 Flash 모델, 분당 15 RPM 한도, 카드 등록 X
5. 연속 채점은 *분당 2~3회 이하*로 자제 (RPM 한도 초과 방지)

## 로컬 실행

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# SECRET_KEY, LLM_API_KEY를 실제 값으로 교체
python manage.py migrate
python manage.py runserver
```

> 원격 배포시에는 배포 가이드를 따라 `manage.py runserver`를 그대로 사용하는 것이 아닌 gunicorn을 사용해야하는데, 본 과제에서는 올바른 챌린지 수행을 위해서 배포 가이드에 나와있는 gunicorn 실행 명령 대신 아래 명령어를 사용해주세요. 배포 가이드대로 gunicorn을 실행하면 과제 채점이 실패합니다!
>
> ```bash
> gunicorn --workers 2 --bind 0.0.0.0:8000 seminar.wsgi:application
> ```

## AWS 배포

**AWS 배포 가이드**를 그대로 따라가면 됩니다.
차이점:

- 가이드의 `11th-week-back` → `lionchat-backend`
- 추가 환경변수: `LLM_API_KEY` (`.env`에 추가)
- 추가 requirements 패키지: `google-generativeai` (이미 `requirements.txt`에
  포함되어 있음)
- gunicorn 워커는 *2개 이상*으로 띄우기 (`--workers 2` 명시)

## API 요약

| Method | Path | 인증 | 설명 |
|---|---|---|---|
| POST | `/api/auth/signup/` | X | 회원가입 |
| POST | `/api/auth/login/` | X | JWT 발급 |
| GET | `/api/users/me/` | O | 본인 정보 |
| POST | `/api/chat/` | O | 질문 → Gemini 답변. `{ prompt }` |
| GET | `/api/chats/` | O | 내 채팅 로그 |
| GET | `/api/chats/<id>/` | O | 특정 채팅 |
