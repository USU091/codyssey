# Codyssey Mission 1 — 개발 워크스테이션 구축

개발에 필요한 기본 환경(WSL2 + Ubuntu)을 구축하고,
터미널 조작 · 파일 권한 · Git/GitHub · Docker 사용법을 익히는 미션 기록입니다.

---

## 0. 실행 환경

| 항목 | 내용 |
|------|------|
| OS | Windows + **WSL2 (Ubuntu 26.04.1 LTS)** |
| 셸 | bash |
| 버전 관리 | Git 2.53.0 |
| 컨테이너 | Docker 29.9.2 |
| 원격 저장소 | https://github.com/USU091/codyssey |

### 0-1. 진행 체크리스트

- [x] WSL2 + Ubuntu 설치 및 초기 설정
- [x] 패키지 최신화 (`apt update` / `apt upgrade`)
- [x] Git 설치 및 저장소 clone
- [x] GitHub 저장소 연동
- [x] 터미널 기본 명령어 실습
- [x] 파일 권한 실습
- [x] Git 설정 · 커밋 · 푸시
- [x] Docker 설치 및 기본 실습

---

## 1. WSL2 + Ubuntu 설치

- Windows에 WSL2를 활성화하고 **Ubuntu 26.04.1 LTS** 설치
- 초기 사용자 계정 생성 및 로그인 확인
- 패키지 저장소 최신화

```bash
sudo apt update
sudo apt upgrade
```

---

## 2. Git + GitHub 연동

### 2-1. Git 설치 및 저장소 clone

```bash
git --version          # git version 2.53.0
git clone https://github.com/USU091/codyssey.git
cd codyssey
```

### 2-2. Git 로컬 설정 (저장소 단위)

```bash
git config --local user.name  "USU091"
git config --local user.email "boneoflily@naver.com"
```

### 2-3. 첫 커밋 & 푸시

- PAT(Personal Access Token)로 인증하여 push 성공
- origin 원격 연결 및 커밋 동기화 확인

```bash
git add .
git commit -m "first commit"
git push origin main
```

### 2-4. 자격증명 저장 설정

- 매번 아이디/토큰을 입력하지 않도록 credential helper를 **저장소 단위**로 설정했습니다.
- 첫 push 때 1회 입력하면 이후에는 자동으로 인증됩니다.

```bash
git config --local credential.helper store
```

> 🔒 자격증명은 원격 URL이 아닌 별도 파일(`~/.git-credentials`)에 저장되며,
> 저장소 URL 자체에는 토큰이 포함되지 않음을 확인했습니다.

### 2-5. 설정 점검 (`git config --list`)

```text
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
remote.origin.url=https://github.com/USU091/codyssey.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.main.remote=origin
branch.main.merge=refs/heads/main
user.name=USU091
user.email=bo******@naver.com
```

> ✅ 원격 URL에 토큰이 포함되지 않았음을 확인했습니다. (자격증명은 별도 저장)
> 🔒 개인 이메일은 공개 노출 방지를 위해 일부 마스킹하여 기재했습니다.

---

## 3. 터미널 기본 조작

| 명령어 | 설명 |
|--------|------|
| `pwd` | 현재 작업 디렉토리 경로 출력 |
| `ls -la` | 숨김 파일 포함 상세 목록 출력 |
| `cd` | 디렉토리 이동 |
| `touch` | 빈 파일 생성 |
| `echo` | 문자열 출력 (파일 기록에도 사용) |
| `cat` | 파일 내용 출력 |
| `cp` | 파일 복사 |
| `mv` | 파일 이동 / 이름 변경 |
| `rm` | 파일 삭제 |

> 각 명령어를 직접 실행하고 출력 결과를 확인하며 실습하였습니다.

---

## 4. 파일 권한 (chmod)

### 4-1. 파일 권한 변경 (644 → 755)

```bash
chmod 755 파일명
```

| 변경 전 | 변경 후 |
|---------|---------|
| `-rw-r--r--` (644) | `-rwxr-xr-x` (755) |

### 4-2. 디렉토리 권한 변경 (755 → 700)

```bash
chmod 700 디렉토리명
```

| 변경 전 | 변경 후 |
|---------|---------|
| `drwxr-xr-x` (755) | `drwx------` (700) |

> 권한 3자리는 각각 **소유자 / 그룹 / 기타 사용자**의 읽기(4)·쓰기(2)·실행(1) 권한 합입니다.

---

## 5. Docker 설치 및 실습

Ubuntu(WSL2) 환경에 **Docker Engine**을 공식 저장소를 통해 직접 설치했습니다.

### 5-1. 설치 및 동작 확인

```bash
docker --version       # Docker version 29.9.2
docker info            # 데몬 정상 동작 확인
docker run hello-world # 설치 검증용 컨테이너 실행 성공
```

### 5-2. sudo 없이 docker 실행 설정

현재 사용자를 `docker` 그룹에 추가하여 `sudo` 없이 명령을 실행하도록 설정했습니다.

```bash
sudo usermod -aG docker $USER
# 적용을 위해 재로그인(또는 셸 재시작) 후 확인
```

### 5-3. 컨테이너 실행 실습

```bash
docker run -it ubuntu   # ubuntu 컨테이너 진입 (내부에서 ls / echo 실행 후 종료)
docker ps -a            # 실행 이력(종료된 컨테이너 포함) 확인
```

| 명령어 | 설명 |
|--------|------|
| `docker run -it` | 컨테이너를 대화형(interactive) 모드로 실행 |
| `docker ps -a` | 전체 컨테이너 목록(종료된 것 포함) 출력 |

### 5-4. 다음 단계 (진행 예정)

- [ ] Dockerfile 빌드 / 실행
- [ ] 포트 매핑 접속
- [ ] 바인드 마운트 반영
- [ ] 볼륨 영속성
