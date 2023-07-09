## 📝 CI/CD
- 컴파일: 컴퓨터가 이해할 수 있는 언어로 번역
- 빌드: 컴파일 된 코드를 실제 실행할 수 있는 상태로 만드는 것
- 배포: 빌드가 완성된 파일을사용자가 접근할 수 있는 환경에 배치

### CI(Continuous Integration)
: 개발을 하면서 코드에 대한 통합을 지속적으로 진행함으로써 코드 품질을 유지하는 것

### CD(Continuous Delivery / Deploy)
: 배포를 자동화하여 지속적으로 배포하는 것
(CI를 통과한 코드를 CD)

장점
- 탁원한 코드 품질 보장
- 릴리즈 속도 단축

### CI/CD 툴 
🔻`젠킨스`

👍 장점
- 편리한 설정
- 안정적인 빌드 / 배포 환경
- 다양한 활용 및 손쉬운 확장

👎 단점
- 설치형 
- 대량 리소스 발생

🔻`Travis CI`

👍 장점
- 오픈 소스 웹 서비스
- yml을 통한 쉬운 설정

👎 단점
- 일정 크레딧까지 무료
- Github Action보다 속도가 느림

🔻`Github Actions`

👍 장점
- Github에서 제공
- Github repository에서 관리 가능

👎 단점
- private repository에서는 요금 발생(public에서는 무료!)

## Github Actions
- Workflow: 자동화된 전체 프로세스
- Event: 워크플로우를 실행시키는 특정 행동
- Runner: Github Actions Runner Application이 설치된 서버
- Step: 명령어를 실행시키는 테스크
- Job: 하나의 처리 단위, Step들의 집합

## Code Deploy
: 자동화 배포 서비스
+ codedeploy-agent
+ appspec.yml

### appspec.yml

배포 주기에 따른 스크립 순서
Start -> ApplicationStop -> DownloadBundle ...

### S3
객체 스토리지 서비스
- 트래픽 관리
- 버전 관리
- 파일 접근 권한 지정 가능

```shell
- name: Make zip file
	run: zip -r ./[zip파일 이름]./deploy
	shell: bash

- name: Configure AWS credentials
	uses: aws-actions/configure-aws-credentials@v1
	with:
		aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
		aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
		aws-region: [region 작성]

- name: Upload to S3
	run: aws s3 cp --region [region 작성] ./[zip파일 이름 작성] s3://[버킷 이름]/        
```

### 아키텍쳐
![](https://velog.velcdn.com/images/chhaewxn/post/8757480b-794d-4674-b432-5e10a66120a5/image.png)


## Github Actions + AWS CodeDeploy를 이용한 CI/CD 실습

### EC2 설정 
- tag 추가 : 태그 관리에 키 입력
- IAM 역할 추가 : EC2 인스턴스에서 S3에 올려놓은 파일에 접근할 수 있도록

➕ 여기서 IAM 이란? 
AWS Identity and Access Management(IAM)은 AWS 리소스에 대한 액세스를 안전하게 제어할 수 있는 웹 서비스, IAM을 사용하여 리소스를 사용하도록 인증(로그인) 및 권한 부여(권한 있음)된 대상을 제어

사용자
- AWS Identity and Access Management(IAM) 사용자는 AWS에서 생성하는 엔티티
- IAM 사용자는 AWS와의 상호 작용하는 인간 사용자 또는 서비스를 나타냄(ex. Github Actions)

역할
- IAM 역할은 계정에 생성할 수 있는, 특정 권한을 지닌 IAM 자격 증명
- 역할은 IAM 사용자, AWS에서 제공하는 웹 서비스, 인증된 외부 사용자에게 부여됨

#### EC2에 CodeDeploy Agent 설치 
AWS CLI 또는 openSSH 또는 putty를 이용해 EC2 서버에 원격 접속 후 명령어 순차적으로 입력

🔻 명령어
```shell
$ sudo apt update

$ sudo apt install ruby-full

$ sudo apt install wget

$ cd /home/ubuntu

$ wget https://aws-codedeploy-ap-northeast-2.s3.ap-northeast-2.amazonaws.com/latest/install

$ chmod +x ./install

$ sudo ./install auto > /tmp/logfile

$ sudo service codedeploy-agent status
```

#### EC2 서버에 루트 디렉토리 만들기
```shell
mkdir root directory_name
```
- 프로젝트를 빌드하고 실행할 루트 디렉토리

#### AWS S3 생성 
Github Actions가 zip 파일을 올릴 s3 만들기

#### CodeDeploy 애플리케이션 생성 
- 애플리케이션 이름을 입력하고, 플랫폼은 EC2/온프레미스 선택
- CodeDeploy 배포 그룹 생성
	- 앞서 생성한 codedeploy 전용 IAM을 EC2에 엑세스 할 수 있도록 역할을 입력해 줌
    - 아까 만든 Tag로 대상 인스턴스를 지정해줌
    
#### Github Actions를 위한 IAM 사용자 추가
- S3 full access, code deploy full access를 부여한 사용자 생성
- 보안 자격 증명에서 엑세스 키 만들기
- AWS 외부에서 실행되는 애플리케이션 설정
- 여기서 .csv 파일 다운로드(다운로드 시, 재다운로드가 어려우니 저장 잘 해놓을 것!)

#### Github Secrets에 IAM User 액세스 키 정보 추가
- 깃허브 Settings에 들어가서 Application에 전에 만든 RDS 엔드포인트와 전에 만든 DB PASSWORD를 담은 .yml 파일을 작성

#### appspec.yml 작성
- CodeDeploy에서 배포를 위해 참조할 파일 루트 디렉토리에 작성
```shell
version: 0.0
os: linux

files:
  - source:  /
    destination: /home/ubuntu/demo
    overwrite: yes
file_exists_behavior: OVERWRITE
permissions:
  - object: /
    pattern: "**"
    owner: ubuntu
    group: ubuntu

hooks:
  AfterInstall:
    - location: scripts/stop.sh
      timeout: 60
      runas: ubuntu
  ApplicationStart:
    - location: scripts/start.sh
      timeout: 60
      runas: ubuntu
```

#### 배포 스크립트 작성
🔻 start.sh
```shell
#!/usr/bin/env bash

PROJECT_ROOT="/home/ubuntu/demo"
JAR_FILE="$PROJECT_ROOT/spring-webapp.jar"

APP_LOG="$PROJECT_ROOT/application.log"
ERROR_LOG="$PROJECT_ROOT/error.log"
DEPLOY_LOG="$PROJECT_ROOT/deploy.log"

TIME_NOW=$(date +%c)

# build 파일 복사
echo "$TIME_NOW > $JAR_FILE 파일 복사" >> $DEPLOY_LOG
cp $PROJECT_ROOT/build/libs/*.jar $JAR_FILE

# jar 파일 실행
echo "$TIME_NOW > $JAR_FILE 파일 실행" >> $DEPLOY_LOG
nohup java -jar $JAR_FILE > $APP_LOG 2> $ERROR_LOG &

CURRENT_PID=$(pgrep -f $JAR_FILE)
echo "$TIME_NOW > 실행된 프로세스 아이디 $CURRENT_PID 입니다." >> $DEPLOY_LOG
```
🔻 stop.sh
```shell
#!/usr/bin/env bash

PROJECT_ROOT="/home/ubuntu/demo"
JAR_FILE="$PROJECT_ROOT/spring-webapp.jar"

DEPLOY_LOG="$PROJECT_ROOT/deploy.log"

TIME_NOW=$(date +%c)

# 현재 구동 중인 애플리케이션 pid 확인
CURRENT_PID=$(pgrep -f $JAR_FILE)

# 프로세스가 켜져 있으면 종료
if [ -z $CURRENT_PID ]; then
  echo "$TIME_NOW > 현재 실행중인 애플리케이션이 없습니다" >> $DEPLOY_LOG
else
  echo "$TIME_NOW > 실행중인 $CURRENT_PID 애플리케이션 종료 " >> $DEPLOY_LOG
  kill -15 $CURRENT_PID
fi
```

#### build.gradle 파일 수정
- 빌드 시 -plain.jar 파일은 생기지 않도록
```shell
jar {
	enabled = false
}
```

#### Github Actions Workflow 작성
🔻 deploy.yml
```shell
# This is a basic workflow to help you get started with Actions

name: Deploy to Amazon EC2
# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

env:
  AWS_REGION: ap-northeast-2
  S3_BUCKET_NAME: blog1-github-actions-s3-bucket
  CODE_DEPLOY_APPLICATION_NAME: blog-codedeploy-app
  CODE_DEPLOY_DEPLOYMENT_GROUP_NAME: blog-codedeploy-deployment-group
  APPLICATION: ${{ secrets.APPLICATION }}

permissions:
  contents: read

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    environment: production

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        distribution: 'temurin'
        java-version: '11'
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew

    - uses: actions/checkout@v2
    - run: touch ./src/main/resources/application.yml
    - run: echo "${{env.APPLICATION}}" > ./src/main/resources/application.yml
    - uses: actions/upload-artifact@v2
      with:
        name: application.yml
        path: ./src/main/resources/application.yml

    - name: Build with Gradle
      uses: gradle/gradle-build-action@0d13054264b0bb894ded474f08ebb30921341cee
      with:
        arguments: clean build -x test

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ env.AWS_REGION }}

    - name: Upload to AWS S3
      run: |
        aws deploy push \
          --application-name ${{ env.CODE_DEPLOY_APPLICATION_NAME }} \
          --ignore-hidden-files \
          --s3-location s3://$S3_BUCKET_NAME/$GITHUB_SHA.zip \
          --source .

    - name: Deploy to AWS EC2 from S3
      run: |
        aws deploy create-deployment \
          --application-name ${{ env.CODE_DEPLOY_APPLICATION_NAME }} \
          --deployment-config-name CodeDeployDefault.AllAtOnce \
          --deployment-group-name ${{ env.CODE_DEPLOY_DEPLOYMENT_GROUP_NAME }} \
          --s3-location bucket=$S3_BUCKET_NAME,key=$GITHUB_SHA.zip,bundleType=zip
```

- 여기서 결과확인은 commit, push하여 Actions에 들어가면 됨!

### 🚨 발생할 수 있는 에러

1) Build Gradlew에서 execute permission 에러
: 터미널에서 git update-index --chmod=+x ./gradlew 하고 커밋, 푸시하기

2) 폴더 접근 에러
: 경로 지정 다시 해주기
EX) ./src/main/ ... 
