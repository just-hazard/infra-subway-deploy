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
   1. KEY-just-hazard 입니다.

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 : 
  - 192.168.17.0/26 (외부망1, ap-northeast-2a), 
  - 192.168.17.64/26 (외부망2, ap-northeast-2b), 
  - 192.168.17.128/27(내부망, ap-northeast-2a), 
  - 192.168.17.160/27(관리망, ap-northeast-2b)

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요

- URL : http://just-hazard.p-e.kr (3.39.184.31)



---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : https://just-hazard.p-e.kr

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.

#!/bin/bash

txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray


PROJECT_DIRECTORY=/home/ubuntu/infra-subway-deploy
JAR_PATH=$(find ./* -name "*jar")
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
fi

function moveToRepo() {
cd ${PROJECT_DIRECTORY}
}

function checkoutRepoAndPull() {
echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn}  ${BRANCH} REMOTE BRANCH PULL ${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

git checkout ${BRANCH}
git pull origin ${BRANCH}
}

function build() {
echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn} build start ${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

./gradlew clean build
}

function killProcess() {
echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn} infra-subway-deploy process kill ${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

fuser -k 8080/tcp
}

function serverStart() {
echo -e "${txtylw}=======================================${txtrst}"
echo -e "${txtgrn} server start ${txtrst}"
echo -e "${txtylw}=======================================${txtrst}"

nohup java -jar -Dspring.profiles.active=${PROFILE} ${JAR_PATH} &
}

moveToRepo
checkoutRepoAndPull
build
killProcess
serverStart
