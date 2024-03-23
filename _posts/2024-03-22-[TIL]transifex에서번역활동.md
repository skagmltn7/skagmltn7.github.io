---
title: "[TIL]transifex에서 번역 활동"
author: heesu
date: 2024-03-22 19:29:00+0900
categories: [TIL]
tags: [번역]
---

## 1. 프로젝트 동기
오픈소스를 수강하면서 다양한 오픈 소스를 실습할 수 있었다. 사실 오픈소스 수업을 수강하기 전까지 git이나 linux와 같은 정말 기본적인 오픈소스만 사용할 줄 알았다. 기말프로젝트를 진행하는 것에 있어 다른 오픈소스 강좌나 팁같은 동영상을 만들기에는 내가 친숙하고 사용할 줄 아는 오픈소스의 수가 적었기에 그나마 내가 기여할 수 있는 오픈소스 번역을 선택하게 되었다. 그 중에서도 어떤 오픈소스가 있는지 찾아보다가 예전에 교환학생 친구들과 비대면에 대해서 대화하다가 들은 적이 있었던 opendedX선택하였다. openedX를 번역하는 데에도 다양한 선택지가 있었는데 release항목을 선택하게 되었다. 처음에는 release를 지원하는 프레임워크 중 지난 학기에 사용해본 적이 있는 Koa를 선택하였다. 그런데 프로젝트를 진행하면서 openedX 홈페이지 release관련을 살펴보다가 현재 release하는 버전은 lilac뿐인 것을 알고 프로젝트 주제를 openedX-release-lilac.js로 결정하였다. 번역은 transifex에서 지원하는 <https://www.transifex.com/open-edx/open-edx-releases/release-lilac-js/>에서 진행하였다.
## 2. OpenedX
<img src="https://git.ajou.ac.kr/Heesu/foss-final/-/raw/main/openedx-2021-logo.png">
현재 코로나 상황에서 비대면에 알맞는 오픈소스로 전세계 어디에서든 해외의 대학 강의를 들을 수 있고 다른 수강생들과 교류할 수 있는 교육용 오픈소스이다. 무료로 공개된 강의와 구매해야하는 강의가 있으며 openedX 플랫폼 내에서 시험, 토론 등을 진행할 수 있다. 강의를 모두 수강하면 수료증을 발급받을 수 있어 열린 교육 목적에 알맞는 오픈소스라 할 수 있다.  

## 3. 번역 정도
일단 제일 먼저 한국어로 번역되지 않은 untranslated 항목들 부터 최우선적으로 번역하였으며, 번역 하기전 untranslated는 총 735 strings이었으며 번역 후 untranslated는 총 89strings이었다.  
아래 이미지는 내가 openedX-release-lilac.js를 번역한 words와 sritngs를 보여준다.

><img src="https://git.ajou.ac.kr/Heesu/foss-final/-/raw/main/After/after_translation.PNG">
>
***총 639 strings / 5337 words***를 한국어로 번역하였다.  

번역 내용의 대부분은 openedX의 웹페이지에서 나타나는 가입, 수업, 설정, 로그인 등 user case에 관련된 내용이었다.
#### 번역을 하면서 어려웠던 점
___
단어에 대한 막연함이 제일 컸다. 물론 단어 자체는 해석이 되지만 그 단어가 openedX에서 그 의미의 단어로 사용되는지 알 수 없었다. 이를 해결하기 위해 openedX홈페이지의 glossary<https://edx.readthedocs.io/projects/edx-developer-guide/en/latest/glossary.html>를 참고하여 번역을 진행하였다.  또 번역을 하면서 고질적인 어려움이라 할 수 있는 영어 문장을 직역을 해야하는 것인가 의역을 해야하는 것인가에 대한 문제도 있었다. 한국어같은 경우 대체적으로 주어를 생략해도 한국어를 사용하는 사람으로서 어려움이 없는데 영어같은 경우는 주어를 밝혀주어야하기 때문에 어순에 관련하여 고민할 점이 있었다.


## 4. 느낀점
일단 오픈소스에 대해서 배울 수 있어서 좋았다. 기말 프로젝트 주제를 정하면서 새로운 오픈소스에 대해서 접할 수 있었고 또 가장 좋았던 점은 transifex홈페이지를 알게 된 것이었다. 편리한 UI로 되어있고 전세계 사람들이 참여하는 프로젝트를 큰 어려움없이 참여할 수 있다는 것도 뿌듯했다. 그리고 explore에서 현재 활성화된 오픈소스 종류에 대해서 간략하게나마 알 수 있었다. 오픈소스 수업이 끝나도 계속 transifex를 사용할 예정이며 번역에서 더 나아가 번역하는 오픈소스를 사용하면 좋을 것같다는 생각이 들었다.  

