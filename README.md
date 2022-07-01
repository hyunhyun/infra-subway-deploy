<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 README.md 파일에 작성하여 PR을 보내주세요.

### 0단계 - pem 키 생성하기

1. 서버에 접속을 위한 pem키를 [구글드라이브](https://drive.google.com/drive/folders/1dZiCUwNeH1LMglp8dyTqqsL1b2yBnzd1?usp=sharing)에 업로드해주세요

2. 업로드한 pem키는 무엇인가요.
    -> hyunhyun-aws-key.pem

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 : 
- 192.168.12.0/26
- 192.168.12.64/26
- 192.168.12.128/27
- 192.168.12.160/27

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요
- URL : http://hyunhyunsubway.o-r.kr/



---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : https://hyunhyunsubway.o-r.kr/

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.
deploy.sh
 ```shell
 #!/bin/bash

## 변수 설정
txtrst='\033[1;37m' # White
txtred='\033[1;31m' Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray

PROJECT_PATH=/home/ubuntu/nextstep/infra-subway-deploy
BRANCH=$1
PROFILE=$2

## 조건 설정
if [[ $# -ne 2 ]]
then
    echo -e "${txtylw}=======================================${txtrst}"
    echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
    echo -e ""
    echo -e "${txtgrn} $0 브랜치이름 ${txtred}{ prod | dev }"
    echo -e "${txtylw}=======================================${txtrst}"
    exit
else
    echo -e "${txtylw}=======================================${txtrst}"
    echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
    echo -e "${txtylw}=======================================${txtrst}"
fi

function pull() {
    echo -e ""
    echo -e ">> Pull Request x🏃♂️ "
    git checkout $BRANCH
    git pull
}

function build() {
    echo -e ""
    echo -e ">> Build Gradle🏃♂️ "
    ./gradlew clean build
}

function shutdown_process() {
    echo -e ""
    echo -e ">> Find Process ID🏃♂️ "
    CURRENT_PID=$(pgrep -f ${PROJECT_NAME}.*.jar)
    echo -e "현재 구동중인 애플리케이션 pid  : $CURRENT_PID "

    echo -e ">> 구동 중 애플리케이션 종료"
    if [ $CURRENT_PID  -eq 0 ]; then
      echo -e "현재 구동중이 애플리케이션이 없으므로 종료할게 없습니다.."
    else
      kill -2 $CURRENT_PID
      echo "애플리케이션 종료 완료"
    fi
}
function deploy() {
    echo -e ""
    echo -e ">> Deploy 🥳"
    JAR_NAME=$(find $PROJECT_PATH/build/ -name "subway*.jar")
    echo -e "JAR_NAME : $JAR_NAME"
    nohup java -jar -Dspring.profiles.active=$PROFILE $JAR_NAME > $PROJECT_PATH/subway.log 2>&1 &
    echo -e ""
    echo -e ">> Deploy Success"
}
function start(){
  ## 저장소 pull
  pull;
  ## gradle build
  build;
  ## 기존 프로세스를 종료하는 명령어
  shutdown_process;
  ## 새 애플리케이션 배포
  deploy;
}

start;
 
 ```


