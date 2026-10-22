**한국어** | [English](README.en.md)

# youtube-to-notion

> Notion 페이지에 유튜브 링크를 쌓아 두고, Claude Code에서 `/youtube-to-notion` 한 번이면 영상을 요약해 Notion 데이터베이스에 정리해 주는 Claude Code 스킬.

- 자막이 있으면 자막으로, **자막이 없으면 Groq Whisper로 받아써서** 정리합니다.
- 영상마다 Notion 페이지 1개: 썸네일, 영상 정보, 한 줄 요약, 핵심 요약 표, 흐름별 섹션, 전체 구조 트리.
- 채널·태그·분류는 기존 선택지를 재사용하고, 없으면 자동으로 추가합니다.
- 처리한 링크는 '작업 대기 영상(URL)' 페이지에서 자동으로 체크(`- [x]`)되고, 실패한 영상은 다음 실행 때 다시 시도합니다.

## 💡 개발 배경

빠르게 변화하는 IT 환경에서 새로운 서비스, 애플리케이션, 플러그인, 스킬 및 방법론을 익히기 위해 출퇴근 시간에 IT 관련 유튜브 영상을 자주 시청합니다. 영상을 볼 때는 유용한 정보를 기억하고 활용할 수 있을 것 같지만, 며칠이 지나면 구체적인 내용은 물론 서비스나 도구의 이름조차 잊어버리는 경우가 많았습니다.

유튜브의 '나중에 볼 동영상'이나 별도의 저장 목록에 영상을 보관하더라도 다시 찾아보거나 학습 내용을 복습하기는 쉽지 않았습니다. 단순히 영상을 저장하는 것을 넘어, 유용한 영상의 핵심 내용을 정리하고 필요할 때 언제든 다시 확인할 수 있는 개인 학습 환경이 필요했습니다.

## 🎯 해결 방법

이러한 불편함을 해결하기 위해 Claude Code의 커스텀 스킬인 `youtube-to-notion`을 개발했습니다.

모바일에서도 쉽게 접근할 수 있는 Notion에 유튜브 URL을 모아두고, 노트북에서 Claude Code의 `/youtube-to-notion` 스킬을 실행하면 미처리 영상을 자동으로 확인합니다. AI가 영상의 자막(없으면 음성 받아쓰기)과 화면 정보를 바탕으로 핵심 내용을 요약하고, 정리된 결과를 Notion의 별도 페이지에 저장합니다.

## 🚀 기대 효과

- **학습 자료의 체계화**: 흩어져 있던 유튜브 영상과 학습 내용을 Notion에서 통합 관리
- **반복 학습 환경 구축**: 영상의 핵심 내용과 활용 방법을 필요할 때 다시 확인
- **정리 작업 자동화**: 영상별 요약과 페이지 작성을 자동화하여 기록에 드는 시간과 노력 절감
- **지속적인 지식 축적**: IT 트렌드와 새로운 도구에 대한 학습 내용을 개인 지식 자산으로 축적

이를 통해 단순히 영상을 시청하는 데 그치지 않고, 유용한 정보를 지속적으로 축적하고 활용할 수 있는 개인화된 학습 워크플로우를 구축하고자 했습니다.

## 📸 사용 예시

**① 영상 링크 쌓아 두기** — 출퇴근길에 휴대폰으로 Notion '작업 대기 영상(URL)' 페이지에 링크를 붙여 넣습니다. 체크가 안 된 항목이 처리 대상입니다.

<img src="docs/images/01-queue.png" width="520" alt="작업 대기 영상(URL) 페이지">

**② 노트북에서 한 줄 실행** — Claude Code에서 `/youtube-to-notion`을 입력하면 자막(없으면 음성 받아쓰기)을 받고, 도구·인물 이름이 애매하면 해당 장면만 캡처해 확인한 뒤 요약합니다.

<img src="docs/images/02-run.png" width="620" alt="Claude Code 실행 화면">

**③ 데이터베이스에 정리 완료** — 순번·설명·분류·태그가 채워진 새 행이 맨 아래에 쌓이고, 처리한 링크는 자동으로 체크됩니다.

<img src="docs/images/03-db-row.png" alt="데이터베이스에 추가된 행">
<img src="docs/images/04-checked.png" width="520" alt="자동 체크된 링크">

**④ 결과 페이지** — 영상 정보, 한 줄 요약, 핵심 요약 표, 흐름별 정리, 전체 구조 트리까지 한 페이지에 정리됩니다.

<details>
<summary>결과 페이지 미리보기 (클릭)</summary>

<img src="docs/images/05-result-page.png" width="600" alt="정리된 Notion 페이지">
</details>

## 동작 흐름

```
Notion '작업 대기 영상(URL)' 페이지 (- [ ] 유튜브 링크)
  → /youtube-to-notion
  → watch 스킬 (yt-dlp + ffmpeg): 자막·메타데이터, 자막 없으면 Groq Whisper
  → 요약 작성, 채널·태그·분류 결정
  → Notion DB에 새 페이지 (순번 +1)
  → '작업 대기 영상(URL)' 페이지 해당 줄 - [x] 체크
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

Claude Code에서 두 줄이면 됩니다 (Claude Code 2.1.275 이상).

```
/plugin install watch --marketplace bradautomates/claude-video
/plugin install youtube-to-notion --marketplace rsohyun/youtube-to-notion
```

- 첫 줄은 필수 의존성인 [watch 스킬](https://github.com/bradautomates/claude-video)입니다. 설치 후 watch 안내에 따라 yt-dlp·ffmpeg를 준비하세요.
- **(선택) Groq 키**: 자막 없는 영상용. [console.groq.com/keys](https://console.groq.com/keys)에서 키를 만들어 `~/.config/watch/.env`의 `GROQ_API_KEY=` 뒤에 붙여 넣습니다. **키를 대화창이나 저장소에 올리지 마세요.**

<details>
<summary>플러그인 없이 수동 설치</summary>

`skills/youtube-to-notion/SKILL.md`를 `~/.claude/skills/youtube-to-notion/SKILL.md`(어디서나) 또는 `<프로젝트>/.claude/skills/youtube-to-notion/SKILL.md`(그 프로젝트에서만)에 복사합니다.
</details>

## Notion 준비

**미리 만들 것은 없습니다.** 처음 `/youtube-to-notion`을 실행하면 Claude가 묻습니다.

- **아직 없다** → 원하는 Notion 페이지 아래에 **작업 대기 영상(URL) 페이지**와 **정리용 데이터베이스**(아래 속성 + 순번 정렬 보기)를 자동으로 만들어 줍니다.
- **이미 있다** → 두 링크를 알려 주면 연결하고, DB에 빠진 속성이 있으면 추가해 줍니다.

설정은 `~/.config/youtube-to-notion/config.json`에 저장되어, 다음부터는 묻지 않습니다.

그다음 '작업 대기 영상(URL)' 페이지에 체크리스트로 링크를 쌓으면 됩니다.

```
- [ ] [영상 제목](https://youtu.be/...)
```

<details>
<summary>데이터베이스 속성 (참고용 — 자동 생성됨)</summary>

| 속성 | 타입 | 내용 |
|------|------|------|
| 이름 | 제목 | 영상 주제를 짧게 정리한 제목 |
| 순번 | 숫자 | 1부터 자동 증가 (정렬 기준) |
| 영상 제목 | 텍스트 | 유튜브 원제목 |
| 채널 | 선택 | 유튜브 채널 |
| 설명 | 텍스트 | 한 줄 설명 |
| 태그 | 다중 선택 | 영상 속 도구·기능·인물 |
| 기록날짜 | 날짜 | 정리한 날 |
| 영상날짜 | 날짜 | 업로드일 |
| 스터디 분류 | 다중 선택 | 기초지식 / 공부법 / 최신APP (자동 추가 가능) |
</details>

## 사용법

Claude Code에서:

```
/youtube-to-notion
```

(플러그인으로 설치하면 `/youtube-to-notion:youtube-to-notion`으로 보일 수 있습니다) 또는 "대기 영상 정리해줘". 끝나면 성공·실패 목록과 새 페이지 링크를 알려 줍니다.

## 참고와 주의

- 영상 하나가 실패해도 나머지는 계속 처리합니다.
- 자막이 없는 영상은 오디오가 Groq 서버로 전송됩니다.
- 아주 긴 영상은 Groq 업로드 크기 제한에 걸릴 수 있습니다.
- 요약은 한국어로 작성되도록 되어 있습니다. 다른 언어가 필요하면 `SKILL.md`의 "한국어로 작성" 줄을 바꾸세요.

## 감사

영상 다운로드·프레임 추출·받아쓰기는 [bradautomates](https://github.com/bradautomates)의 **[watch 스킬 (claude-video)](https://github.com/bradautomates/claude-video)** (MIT)을 사용합니다. 이 저장소에는 watch의 코드가 포함되어 있지 않으며, 별도로 설치해야 합니다.

## 라이선스

[MIT](LICENSE)
