---
title: "2021년 하반기, AI/ML 공부 시작 + 개발 환경 세팅 메모"
description:
date: 2021-09-05
update: 2021-09-05
tags:
  - conda
  - m1
  - AI
  - ML
series:
---

## AI의 시대, 나도 탑승 좀 하자

![AI가 세계를 지배한다...!](meme.png)
NodeJS BackEnd 개발자로 살아온 지 어느덧 2년이 되어간다. 2년이란 시간이 무색할 만큼 개발이란 게 공부하면 끝이 없다고 매번 느낀다. 이젠 AI도 공부해야 한다. 해야 한다긴보단 그냥 공부하고 싶어 졌다. AI 관련 일로 진로를 정해야 앞으로 먹고 살길은 보장될 거란 느낌이라 해야 하나. 암튼 AI의 시대, 늦었지만 "막차 탑승" 좀 해보려 한다.

그래서 이번 대학교 수강 과목으로 2학년임에도 3학년 4학년 AI, ML 관련 전공을 담아놨다. 선 이수 과정을 밟아오고, 암기를 잘하는 선배들에게 학점이 밀릴 거란 불안한 기분은 들지만 나한텐 학점은 중요하지 않다. 일단 AI, ML 경험을 하루라도 빨리 하는 게 이득일 것 같다는 생각이 들었기 때문이다. 덤으로 패스트캠퍼스에서 50만 원어치 머신, 딥 러닝 강의도 사놨다.

### 잘 가 virtualenv, 안녕 anaconda

가끔 python crawling 관련 외주 하는데, 여기서 사용되는 패키지는 requests, BS4 이 두 개가 다였기에 anaconda 같은 패키지 관리는 염두에 안 뒀다. virtualenv로 간단하게 가상 환경 세팅해서 사용하는 편이었는데, 이번 AI/ML 관련 과목을 수강하게 되면서 TensorFlow, Jupyter, R,... 등과 같은 패키지를 설치하면서 생각이 바뀌었다. AI/ML 관련 작업은 그냥 anaconda 사용해야겠다고.

일단 기본적으로 연관 종속 패키지가 엄청나기에 이걸 일일이 하나하나 맞춰주는 것도 정신건강에 나쁠뿐더러 에러 하나라도 뜨면 시간도 많이 낭비된다.

#### m1은 anaconda를 지원 안 한다.

음, 일단 전 글에도 명시해놨지만 ARM 기반 m1에서는 native 단에서 anaconda를 지원 안 한다. rosetta로 구동하느니 차라리 ARM native를 지원하는 miniforge를 설치하자.

```bash
brew install miniforge
```

그럼 이제 conda 패키지 관리를 사용할 수 있다!

다음으로 conda에서 지원해주는 가상 환경 관리를 통해 앞으로 어떤 식으로 짜야할지 고민할 차례가 남았다.

초기 작업 환경 세팅을 중요하게 생각하는 편인데, 지금까지 외주도 그렇고 어떤 작업을 할 때 작업 환경이 제대로 세팅이 안되면 나중에 가서 엄청 고생한다는 걸 몸으로 겪었기 때문이다. 그래서 Windows 안 쓰고 Mac 사용하는 이유도 그중 하나다. UNIX 기반이기에 Terminal 환경이 매우 편리하고, 가이드 문서도 상당히 linux 기반으로 되어있는 게 많다.

요즘 Windows 진영에서 WSL라는 native로 리눅스를 구동하는 게 나왔다고 하던데 좀만 더 빨리 나왔더라면 아마 지금쯤 내가 Mac을 안 쓰고 Windows를 사용하고 있었을지 모르겠다. 어쨌든 지금은 애플충이라 Windows로 넘어갈 생각은 아직 없다. 애플 만세

### conda 환경 관리 고민

앞으로 대학교 강의 수강과 관련하여 공부를 할 때, 필요한 환경을 3가지로 구분해봤다.

- base : jupyter 구동 용

- Renv : R 공부할 때

- TFenv : TensorFlow 공부할 때

- PythonEnv : 마이닝 및 시각화 공부할 때

TFenv 랑 Python Env는 Python 버전이 동일하기에 통합해도 상관없긴 한데, 앞으로 하다가 패키지 종속 에러가 나타날지 모르니까... 분리하는 게 맞다고 생각했다. 그리고 가상 환경 세팅해서 작업하라는 말만 있었지 가상 환경이 너무 많아서 문제가 됐다는 글은 한 번도 못 봤고.

#### 내 기준 conda env 세팅 메모

내 기준이긴 한데, 참고하실 분은 참고하면 될 것 같다.

```bash
# brew 만 설치 된 m1 mac 기준으로
brew install miniforge  # miniforge 설치, zshrc or bashrc 스크립트 작업은 알아서
# conda init "$(basename "${SHELL}")"

# base 환경
conda activate base  # base 환경 활성화
conda install -c conda-forge jupyter  # base 환경에 jupyter 설치

# R 환경
conda create -n Renv -c conda-forge r python ipykernel  # Renv 환경 생성 + R, Python, ipykernel 설치 (R 만 사용하는게 아니기에, python 도 같이 설치)
conda activate Renv  # Renv 환경 활성화
python -m ipykernel install --user --name Renv --display-name "R"  # jupyter kernel 등록
# 아래 구문 미처리
# conda install -c conda-forge ipykernel  # ipykernel 설치 (이때 python도 같이 설치됨)
# R  # R 커맨드 입장
# R > install.packages(c('repr', 'IRdisplay', 'IRkernel'), type = 'source')  # kernel 관련 패키지 설치
# R > IRkernel::installspec()  # R 패키지 IRkernel 으로 jupter kernel 등록 처리
# R > q()  # R 종료

# TF 환경 (참고 https://developer.apple.com/metal/tensorflow-plugin/)
conda create -n TFenv  # TFenv 환경 생성
conda activate TFenv  # TFenv 환경 활성화
conda install -c apple tensorflow-deps  # m1 관련 tensorflow 의존성 설치
pip install tensorflow-macos  # tensorflow 설치 1
pip install tensorflow-metal  # tensorflow 설치 2
conda install -c conda-forge ipykernel  # ipykernel 설치
python -m ipykernel install --user --name TFenv --display-name "TF 2.6.0"  # jupyter kernel 등록

# Python 환경
conda create -n PythonEnv -c conda-forge python=3.8  # PythonEnv 환경 생성
conda activate PythonEnv  # PythonEnv 환경 활성화
conda install -c conda-forge ipykernel  # ipykernel 설치
python -m ipykernel install --user --name PythonEnv --display-name "Python 3.8"  # jupyter kernel 등록
```

참고로 등록된 kernel 이름을 변경하고자 할 때, 아래 과정을 따라주면 된다.

```bash
conda activate base  # base 환경 활성화 (jupyter 설치 된 환경)
jupyter kernelspec list  # jupyter kernel 리스트 출력

# 나는 아래와 같이 나온다.
# Available kernels:
#   ir                  /Users/timetree/Library/Jupyter/kernels/ir
#   studypython38env    /Users/timetree/Library/Jupyter/kernels/studypython38env
#   tfenv               /Users/timetree/Library/Jupyter/kernels/tfenv
#   python3             /opt/homebrew/Caskroom/miniforge/base/share/jupyter/kernels/python3

# 여기서 변경하고자 하는 커널의 dir을 복사해 vi {복사한 dir}/kernel.json 하면 된다.

# 아래는 tfenv 커널을 변경하고자 할 때, 위 출력된 결과를 기준으로

vi /Users/timetree/Library/Jupyter/kernels/tfenv/kernel.json  # tfenv 커널의 정보를 담는 json 편집

# 그 다음에 열린 vi 에디터에서 display_name 항목을 수정해주면 수정한 대로 jupyter kernel 에 나온다!
```

수많은 삽질의 결과로 내 기준 아주 깔끔하게 작업 환경 구성이 완료되었다!

앞으로 공부를 하면서 m1 Mac에서 AI/ML 패키지 호환 이슈가 안 터지길 기도하는 것 만 남은 것 같다. ~~기도 메타 합류~~
