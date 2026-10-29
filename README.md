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
- [x] Dockerfile 빌드 / 실행
- [x] 포트 매핑 접속
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성 검증
- [x] VSCode 설치 및 Microsoft WSL 확장 추가
- [x] VSCode에서 WSL(Ubuntu) 연결

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
git config --local user.email "bo******@naver.com"
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

### 3-1. 실습 로그 (발췌)

```text
$ pwd
/home/user/practice/terminal

$ echo "Hello Codyssey" > hello.txt
$ echo "second line" >> hello.txt
$ cat hello.txt
Hello Codyssey
second line

$ cp hello.txt copy.txt
$ mv copy.txt renamed.txt
$ mkdir sub && mv renamed.txt sub/
$ ls sub
renamed.txt

$ cat /etc/os-release | grep PRETTY_NAME
PRETTY_NAME="Ubuntu 26.04.1 LTS"
$ echo $SHELL
/bin/bash
```

> 각 명령어를 직접 실행하고 출력 결과를 확인하며 실습하였습니다.

---

## 4. 파일 권한 (chmod)

### 4-1. 파일 권한 변경 (644 → 755)

```bash
chmod 755 sample.sh
```

| 변경 전 | 변경 후 |
|---------|---------|
| `-rw-r--r--` (644) | `-rwxr-xr-x` (755) |

### 4-2. 디렉토리 권한 변경 (755 → 700)

```bash
chmod go-rx sampledir   # 그룹·기타 사용자의 읽기/실행 권한 제거
```

| 변경 전 | 변경 후 |
|---------|---------|
| `drwxr-xr-x` (755) | `drwx------` (700) |

### 4-3. 실습 로그 (발췌)

```text
$ ls -l
-rw-r--r-- 1 user user    0 sample.sh
drwxr-xr-x 2 user user 4096 sampledir

$ chmod 755 sample.sh
$ chmod go-rx sampledir
$ ls -l
-rwxr-xr-x 1 user user    0 sample.sh
drwx------ 2 user user 4096 sampledir
```

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

### 5-4. Dockerfile 빌드 및 실행

선택 기준 : (B) Linux 베이스 이미지 활용을 선택했습니다. 해당 이미지에 Python 웹서버와 헬스체크 기능을 추가하여 커스텀 이미지를 제작하였습니다. Ubuntu Linux 환경에서 필요한 패키지를 직접 설치하였습니다. 
`ubuntu:22.04` 베이스 이미지에 Python 웹서버(`http.server`)를 올려 직접 이미지를 빌드했습니다.

```dockerfile
FROM ubuntu:22.04
LABEL org.opencontainers.image.title="my-ubuntu-web"
ENV APP_ENV=dev
ENV PORT=8000
RUN apt-get update && apt-get install -y python3 curl && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY app/ /app/
HEALTHCHECK --interval=10s --timeout=3s CMD curl -f http://localhost:8000/ || exit 1
EXPOSE 8000
CMD ["python3", "-m", "http.server", "8000"]
```


```커스텀 포인트
FROM ubuntu:22.04	Ubuntu 22.04를 기반으로 커스텀 이미지의 실행 환경 구성
LABEL	이미지의 제목을 my-ubuntu-web으로 지정하여 이미지 식별 및 관리
ENV APP_ENV=dev	개발 환경을 나타내는 환경 변수 설정
ENV PORT=8000	웹서버 포트 정보를 환경 변수로 설정
RUN apt-get ...	Python3와 curl을 설치하고, 패키지 목록 파일을 삭제하여 불필요한 이미지 용량을 줄임
WORKDIR /app	컨테이너 내부의 기본 작업 디렉터리 지정
COPY app/ /app/	호스트의 app/ 디렉터리에 있는 파일을 컨테이너로 복사
HEALTHCHECK	HTTP 요청을 통해 웹서버의 정상 동작 여부를 확인
EXPOSE 8000	웹서버가 사용하는 포트 8000을 명시
CMD	컨테이너 실행 시 Python 내장 HTTP 서버를 자동으로 시작하도록 설정
```


```bash
docker build -t my-ubuntu-web:1.0 .   # Dockerfile로 이미지 빌드
docker images                          # 빌드된 이미지 확인
```

| 명령어 | 설명 |
|--------|------|
| `docker build -t 이름:태그 .` | 현재 경로의 Dockerfile로 이미지 빌드 |
| `docker images` | 로컬 이미지 목록 확인 |

> 빌드 결과 `my-ubuntu-web:1.0` 이미지가 정상 생성되었습니다. (약 171MB)

### 5-5. 포트 매핑 접속

호스트 포트와 컨테이너 포트(`8000`)를 연결(`-p`)하여 `curl`로 접속을 확인했습니다.

```bash
docker run -d -p 8080:8000 --name web8080 my-ubuntu-web:1.0   # 호스트 8080 → 컨테이너 8000
docker run -d -p 8081:8000 --name web8081 my-ubuntu-web:1.0   # 호스트 8081 → 컨테이너 8000
curl http://localhost:8080
curl http://localhost:8081
```

| 옵션 | 설명 |
|------|------|
| `-d` | 백그라운드(detached) 실행 |
| `-p 호스트:컨테이너` | 포트 매핑 (여기서는 `8080:8000`) |
| `--name` | 컨테이너 이름 지정 |

**접속 화면:**

![포트 매핑 접속 화면](https://github.com/USU091/codyssey/blob/main/mission1/screenshots/8080web.png)

> 접속 로그에서 `GET / HTTP/1.1 200` 응답과 `(healthy)` 상태를 확인했습니다.

### 5-6. 바인드 마운트

호스트의 디렉토리를 컨테이너 내부 경로(`/app`)에 연결하여, **호스트에서 수정한 내용이 컨테이너에 즉시 반영**되는지 확인했습니다.

```bash
# 1) 호스트에 디렉토리/파일 준비
mkdir -p ~/codyssey/mission1/site
echo "<h1>Bind Mount Test (host)</h1>" > ~/codyssey/mission1/site/index.html

# 2) 바인드 마운트로 컨테이너 실행
docker run -d -p 8082:8000 \
  -v ~/codyssey/mission1/site:/app \
  --name web-bind \
  my-ubuntu-web:1.0

curl http://localhost:8082
# 출력: <h1>Bind Mount Test (host)</h1>

# 3) 호스트 파일 수정 → 컨테이너 재시작 없이 반영 확인
echo "<h1>Bind Mount Test (edited!)</h1>" > ~/codyssey/mission1/site/index.html
curl http://localhost:8082
# 출력: <h1>Bind Mount Test (edited!)</h1>
```

> 컨테이너를 **재빌드·재시작하지 않고도** 호스트에서 수정한 파일이 즉시 반영됨을 확인했습니다.
> 바인드 마운트는 호스트 경로를 그대로 컨테이너에 연결하기 때문입니다.
### 5-7. 볼륨 영속성 (Docker Volume)

Docker가 관리하는 **볼륨**을 생성해 컨테이너에 연결하고,
**컨테이너를 삭제한 뒤 재생성해도 데이터가 유지되는지** 확인했습니다.

```bash
# 1) 볼륨 생성 및 확인
docker volume create mydata
docker volume ls

# 2) 볼륨을 연결해 컨테이너 실행 후 데이터 기록
docker run -d -p 8083:8000 \
  -v mydata:/app \
  --name web-vol \
  my-ubuntu-web:1.0
docker exec web-vol sh -c 'echo "<h1>Volume Persist Test</h1>" > /app/index.html'
curl http://localhost:8083
# 출력: <h1>Volume Persist Test</h1>

# 3) 컨테이너 삭제 후 → 같은 볼륨으로 재생성
docker rm -f web-vol
docker run -d -p 8083:8000 \
  -v mydata:/app \
  --name web-vol2 \
  my-ubuntu-web:1.0
curl http://localhost:8083
# 출력: <h1>Volume Persist Test</h1>  ← 데이터 유지됨!
```

| 구분 | 바인드 마운트 | 볼륨(Volume) |
|------|--------------|--------------|
| 저장 위치 | 호스트의 지정 경로 | Docker가 관리하는 영역 |
| 사용 목적 | 개발 중 실시간 수정 반영 | 데이터 영속 보관 |
| 경로 지정 | 호스트 절대/상대 경로 | 볼륨 이름 |

> ✅ 컨테이너를 삭제(`rm -f`)하고 재생성해도, 볼륨에 기록한 데이터가 그대로 유지됨을 확인했습니다.

---

## 6. 트러블슈팅

실습 중 실제로 마주친 문제와 해결 과정을 기록합니다.

### 6-1. 컨테이너 시작 직후 접속 실패 (Connection reset)

**증상**
```text
$ curl http://localhost:8080
curl: (56) Recv failure: Connection reset by peer
```

**원인**
- 컨테이너는 떴지만, 내부 웹서버(`http.server`)가 아직 완전히 기동되기 전에 접속을 시도함.

**해결**
- 잠깐 대기 후 다시 접속하니 정상 응답(`200`)을 받았습니다.
- `HEALTHCHECK`가 `(health: starting)` → `(healthy)`로 바뀐 뒤 접속하도록 확인.

```bash
docker ps              # STATUS가 (healthy)인지 확인
sleep 2 && curl http://localhost:8080   # 잠시 대기 후 재접속 → 정상
```

> 💡 컨테이너 실행 직후에는 **애플리케이션 기동 시간**이 필요하다는 점을 배웠습니다.

### 6-2. 히스토리 확장 문법 오류 (`!` 문자)

**증상**
```text
$ echo "<h1>Bind Mount Test (edited!)</h1>" > index.html
bash: !)</h1>": event not found
```

**원인**
- bash에서 `!`는 **히스토리 확장(history expansion)** 특수문자로 해석됨.
- 큰따옴표(`"`) 안에서도 `!`는 확장이 시도되어 오류 발생.

**해결**
- 문자열을 **작은따옴표(`'`)** 로 감싸서 해결했습니다. (작은따옴표 안에서는 확장이 일어나지 않음)

```bash
echo '<h1>Bind Mount Test (edited!)</h1>' > index.html   # 작은따옴표 사용
```

> 💡 셸에서 `!`, `$` 등 특수문자가 포함된 문자열은 **작은따옴표** 처리가 안전합니다.

### 6-3. 디렉토리 없이 파일 생성 시도 (No such file or directory)

**증상**
```text
$ echo "test" > ~/codyssey/mission1/site/index.html
bash: ~/codyssey/mission1/site/index.html: No such file or directory
```

**원인**
- 상위 디렉토리(`site`)가 존재하지 않는 상태에서 파일 생성을 시도함.
- `>` 리다이렉션은 **디렉토리를 자동으로 만들어 주지 않음**.

**해결**
- `mkdir -p`로 상위 디렉토리를 먼저 생성한 뒤 파일을 만들었습니다.

```bash
mkdir -p ~/codyssey/mission1/site   # -p: 중간 경로까지 한 번에 생성
echo "test" > ~/codyssey/mission1/site/index.html
```

> 💡 `-p` 옵션은 중간 경로가 없어도 한 번에 생성해 주고, 이미 있어도 에러를 내지 않습니다.

---

## 7. VSCode 설치 및 Microsoft WSL 확장 추가

![vscode - Github 연동 화면](https://github.com/USU091/codyssey/blob/main/mission1/screenshots/github-vscode.png)

## 8. VSCode에서 WSL(Ubuntu) 연결

![VSCode WSL연결 화면](https://github.com/USU091/codyssey/blob/main/mission1/screenshots/vscode-commit.png)

## 9. 마무리

### 배운 점
- **WSL2 + Ubuntu**로 Windows 위에 리눅스 개발 환경을 구축하는 전체 흐름을 익혔습니다.
- 터미널 기본 명령어와 **파일 권한(chmod)** 의 숫자/기호 표기법을 이해했습니다.
- **Git/GitHub** 연동, 자격증명 저장, 커밋·푸시 과정을 직접 수행했습니다.
- **Docker**의 이미지 빌드 → 컨테이너 실행 → 포트 매핑 → 바인드 마운트 → 볼륨까지
  단계별로 실습하며 컨테이너의 동작 원리를 체감했습니다.

- **바인드 마운트 vs 볼륨의 차이**
   - 바인드 마운트는 호스트의 특정 경로를 컨테이너에 직접 연결해서,
     로컬 파일을 수정하면 컨테이너에 바로 반영된다. (개발 중 코드 수정에 유용)
   - 볼륨은 Docker가 관리하는 저장 공간으로, 컨테이너를 삭제해도
     데이터가 남아있어 영속성이 필요한 DB 데이터 등에 적합하다.

- **컨테이너는 기본적으로 격리되어 있다**
   - 컨테이너는 호스트와 분리된 독립 공간에서 실행되므로,
     파일·네트워크·프로세스가 기본적으로 외부와 차단되어 있다.
   - 그래서 데이터를 유지하려면 볼륨/바인드 마운트,
     외부 접속을 허용하려면 포트 매핑처럼 "연결 통로"를 따로 열어줘야 한다.

- **포트 매핑이 필요한 이유**
   - 컨테이너 내부의 포트는 외부에서 바로 접근할 수 없다.
   - `-p 호스트포트:컨테이너포트` 로 매핑해야
     호스트(브라우저 등)에서 컨테이너 안의 서비스에 접속할 수 있다.
   - 예: `-p 8080:80` → 브라우저에서 `localhost:8080` 으로 접속하면
     컨테이너 내부 80번 포트의 웹 서버로 연결된다.

### 핵심 개념 정리
| 개념 | 요점 |
|------|------|
| 이미지 vs 컨테이너 | 이미지는 '설계도', 컨테이너는 '실행된 인스턴스' |
| 포트 매핑 | `-p 호스트:컨테이너`로 외부 접속 경로 연결 |
| 바인드 마운트 | 호스트 경로 연결, 실시간 수정 반영 (개발용) |
| 볼륨 | Docker 관리 영역, 데이터 영속 보관 |

### 향후 개선 / 더 해보고 싶은 것
- `docker compose`로 다중 컨테이너 구성해보기
- 이미지 용량 최적화(멀티 스테이지 빌드) 적용해보기
- `.dockerignore`로 빌드 컨텍스트 줄이기


---

> 본 문서는 Codyssey Mission 1 수행 과정을 직접 실습하며 기록한 README입니다.