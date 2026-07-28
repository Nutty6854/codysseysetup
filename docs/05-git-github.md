# Git 및 GitHub 연동

[← README로 돌아가기](../README.md)

### 4-9. Git 설정 및 GitHub 연동

Git은 로컬 파일의 변경 이력과 브랜치를 관리하는 버전 관리 도구이고, GitHub는 Git 저장소를 원격으로 공유하여 백업과 협업을 지원하는 플랫폼입니다.

사용자 정보와 기본 브랜치 설정을 확인했습니다. 공개 문서에는 이메일 주소를 노출하지 않도록 마스킹했습니다.

```bash
$ git config --get user.name
“Nutty6854”

$ git config --get user.email
"choiwj0520@gmail.com"

$ git config --get init.defaultBranch
main
```

원격 저장소 연결과 현재 커밋을 확인했습니다.

```bash
$ git remote -v
origin  https://github.com/Nutty6854/codysseysetup.git (fetch)
origin  https://github.com/Nutty6854/codysseysetup.git (push)

$ git log --oneline --decorate -2
b9423e7 (HEAD -> main, origin/main, origin/HEAD) Add local project
804cf0a Initial commit
```

`origin/main`과 로컬 `main`이 같은 커밋을 가리키므로 해당 시점의 로컬 커밋이 GitHub에 푸시된 것을 확인했습니다. 최종 문서 변경사항은 아래 순서로 다시 커밋하고 푸시합니다.

```bash
git status
git add -A
git commit -m "Document Docker storage and Git workflow"
git push origin main
```

> **최종 제출 전 수동 확인:** VS Code의 Accounts 또는 Source Control 화면에서 GitHub 로그인과 현재 저장소 연결이 보이도록 캡처하되, 이메일·토큰·인증 코드 등 민감정보는 가리킨 뒤 이 위치에 이미지를 첨부합니다.
