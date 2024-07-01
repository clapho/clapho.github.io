---
title: "[RN/AI] Pytorch로 AI 모델을 학습시켜 모바일 앱에 적용하기"
comments: true
categories:
  - Blog
tags:
  - Pytorch, React Native, labelImg
last_modified_at: 2024-06-30T
---

# [현재 작성중인 글입니다.]

앱을 만들다보면 자연스레 AI 기술에 관심을 가지게 된다.

"요즘 chatGPT가 그렇게 핫하다는데 나도 AI 좀 접목시켜 볼까...?" 라는 상상을 하게 된다

그렇다면 어떻게 해야할까? 하나하나 차근차근 파헤쳐보자.

## 🍀 프로세스

전체적인 흐름은 다음과 같다.

![이미지](/assets/img/ai-on-app-process.png)

### 1. 미래의 석유! 데이터

AI 모델을 훈련시키기 위해서는 그에 적합한 데이터가 필요하다.

오픈소스 데이터를 활용하는 것이 가장 좋지만, 원하는 데이터가 없다면 직접 만들 수밖에 없다.

여기서는 `labelImg`라는 라벨러를 사용하여 데이터를 직접 만들어볼 것이다.

(이 과정이 정 귀찮다면 파인튜닝된 모델을 깃허브, 허깅페이스 같은 곳에서 받아와서 사용해도 좋다.)

### 2. 데이터 전처리

모델을 학습시키는 데이터의 형식에는 여러가지가 있지만 우리가 만들 모델은 csv를 필요로 하기에 xml 형태의 데이터를 csv로 변환할 것이다.

실전에서는 원하는 모델의 형식에 맞게 데이터셋을 변형해보자.

### 3. 모델 훈련

만들어 놓은 데이터셋으로 모델을 학습시켜본다.

GPU가 있으면 더 빠르고 좋지만, CPU로도 나름 괜찮은 모델을 학습할 수 있다.

우리는 도커를 통해 딥러닝용 가상환경 위에서 학습을 진행할 것이다.

### 4. 모델 테스트

의도한대로 잘 학습이 진행되었는지, 정확도는 얼마인지, 실제로 잘 작동하는지 확인한다.

더불어 라이브 테스트도 진행해보자.

### 5. 모델 변형

우리가 훈련시킨 모델을 모바일 앱에서 사용하기 위해서는 특정 형식이 필요하다.

모델 파일 형식이 `.pth`로 되어있을텐데, 이를 `.ptl`파일로 변경해주어야 한다.

이 과정에서 `torch.jit`을 통한 모델 튜닝에 관한 이야기를 할 것이다.

### 6. 모델 적용

해당 모델을 `Snack`에서 확인해보고, 실제로 `React Native` 프로젝트에도 적용해보자.

결과물을 보고 모델 최적화에 대해서도 고민해보자.

<br/>

---

# 🏷️ 데이터 라벨링 (Data Labeling)

모델 학습에 필요한 데이터를 만들기 위해서는 원본(raw) 데이터와 라벨링 툴이 필요하다.

여기서는 [labelImg](https://github.com/HumanSignal/labelImg)라는 툴을 소개하고자 한다. 간단히 설명하자면 다음과 같다.

> Object Detection 모델 학습에 필요한 주석(Annotation) 처리된 이미지를 생성할 수 있게 도와주는 도구이다.

깃허브 페이지에 들어가보면 설치 및 사용 방법에 대해 상세히 설명하고 있다.

단, 필자는 `M3 MacBook Air 13`를 사용하고 있는데 설치 가이드에 부족한 내용이 있어 관련 내용을 추가한다.

**⚠️ pyrcc5: No such file or directory** 혹 이러한 에러를 만난다면 아래 코드대로 설치한다.

```bash
brew install qt qt5
brew install libxml2
brew install pyqt5
pip3 install pyqt5 lxml
make qt5py3
python3 labelImg.py
```