**한국어** | [English](README.en.md)

# youtube-to-notion

> Notion 페이지에 유튜브 링크를 쌓아 두고, Claude Code에서 `/youtube-to-notion` 한 번이면 영상을 요약해 Notion 데이터베이스에 정리해 주는 Claude Code 스킬.

- 자막이 있으면 자막으로, **자막이 없으면 Groq Whisper로 받아써서** 정리합니다.
- 영상마다 Notion 페이지 1개: 썸네일, 영상 정보, 한 줄 요약, 핵심 요약 표, 흐름별 섹션, 전체 구조 트리.
- 채널·태그·분류는 기존 선택지를 재사용하고, 없으면 자동으로 추가합니다.
- 처리한 링크는 받은편지함에서 자동으로 체크(`- [x]`)되고, 실패한 영상은 다음 실행 때 다시 시도합니다.

## 동작 흐름

```
Notion 받은편지함 (- [ ] 유튜브 링크)
  → /youtube-to-notion
  → watch 스킬 (yt-dlp + ffmpeg): 자막·메타데이터, 자막 없으면 Groq Whisper
  → 요약 작성, 채널·태그·분류 결정
  → Notion DB에 새 페이지 (순번 +1)
  → 받은편지함 해당 줄 - [x] 체크
```

## 필요한 것

| 항목 | 설명 |
|------|------|
| [Claude Code](https://claude.com/claude-code) | 스킬을 실행하는 곳 |
| Notion 커넥터 | Claude 앱에서 Notion을 연결 |
| [watch 스킬](https://github.com/bradautomates/claude-video) | 영상 다운로드·자막·받아쓰기를 담당하는 **필수 의존성** |
| yt-dlp, ffmpeg | watch 스킬이 사용 (Windows는 `winget install yt-dlp.yt-dlp`, `winget install Gyan.FFmpeg`) |
| Groq API 키 (선택) | 자막 없는 영상용. 없으면 화면 캡처만으로 정리 |

## 설치

1. **watch 스킬 설치**: [claude-video](https://github.com/bradautomates/claude-video) 안내에 따라 설치하고, 셋업 스크립트로 yt-dlp·ffmpeg를 준비합니다.
2. **이 스킬 넣기**: `SKILL.md`를 아래 둘 중 한 곳에 복사합니다.
   - 특정 프로젝트에서만: `<프로젝트>/.claude/skills/youtube-to-notion/SKILL.md`
   - 어디서나: `~/.claude/skills/youtube-to-notion/SKILL.md`
3. **(선택) Groq 키**: [console.groq.com/keys](https://console.groq.com/keys)에서 키를 만들어 `~/.config/watch/.env`의 `GROQ_API_KEY=` 뒤에 붙여 넣습니다. **키를 대화창이나 저장소에 올리지 마세요.**

## Notion 준비

**1) 받은편지함 페이지**: 아무 페이지에 체크리스트로 링크를 쌓습니다.

```
- [ ] [영상 제목](https://youtu.be/...)
```

**2) 정리용 데이터베이스**: 아래 속성을 만들어 둡니다 (이름 그대로).

| 속성 | 타입 |
|------|------|
| 이름 | 제목 |
| 순번 | 숫자 |
| 영상 제목 | 텍스트 |
| 채널 | 선택 |
| 설명 | 텍스트 |
| 태그 | 다중 선택 |
| 기록날짜 | 날짜 |
| 영상날짜 | 날짜 |
| 스터디 분류 | 다중 선택 |

표 보기를 **순번 오름차순**으로 정렬하면 새 페이지가 맨 아래에 쌓입니다.

**3) 설정 채우기**: `SKILL.md` 맨 위 **설정** 표의 `INBOX_PAGE_ID`, `DATA_SOURCE`를 본인 값으로 바꿉니다. 비워 둔 채 실행하면 Claude가 링크를 물어봅니다.

## 사용법

Claude Code에서:

```
/youtube-to-notion
```

또는 "대기 영상 정리해줘". 끝나면 성공·실패 목록과 새 페이지 링크를 알려 줍니다.

## 참고와 주의

- 영상 하나가 실패해도 나머지는 계속 처리합니다.
- 자막이 없는 영상은 오디오가 Groq 서버로 전송됩니다.
- 아주 긴 영상은 Groq 업로드 크기 제한에 걸릴 수 있습니다.
- 요약은 한국어로 작성되도록 되어 있습니다. 다른 언어가 필요하면 `SKILL.md`의 "한국어로 작성" 줄을 바꾸세요.

## 감사

영상 다운로드·프레임 추출·받아쓰기는 [bradautomates](https://github.com/bradautomates)의 **[watch 스킬 (claude-video)](https://github.com/bradautomates/claude-video)** (MIT)을 사용합니다. 이 저장소에는 watch의 코드가 포함되어 있지 않으며, 별도로 설치해야 합니다.

## 라이선스

[MIT](LICENSE)
