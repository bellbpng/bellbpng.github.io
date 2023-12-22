---
layout: single
title:  "[회고] 임베디드 개발자 그리고 다음은? "
excerpt: "retrospect"

categories:
  - retrospect
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---

## 프로그래밍과 소프트웨어 개발자

학부과정 당시 전공과목 중에 C언어 프로그래밍 강의가 굉장히 힘든 강의로 악명이 높았다(과제 공개는 밤 12시, 정답률과 선착순 제출로 결정되는 점수..). 그 당시에는 프로그래밍이라는 새로운 분야에 대한 관심 절반, 학점을 잘 받기 위한 공부 절반 정도로 C언어 독학을 먼저 시작했었다. 그래도 혼자 공부하는 것보단 같이 공부하는 걸 좋아하는 성격에 컴퓨터공학을 전공하는 10년지기 친구를 한명 꼬셔서 C언어 스터디를 만들었다.


확실히 프로그래밍은 나에게 잘 맞는다고 생각했다. 학창시절에 늘 자신하던 과목은 수학이었는데(실제로 수능에서 만점을 받기도 했고..) 프로그래밍은 수학 문제를 푸는 것과 전혀 다르지 않다고 느꼈다. C언어는 풀이과정을 써내려가기 위한 하나의 도구일뿐, 해답을 구해가는 과정에서의 즐거움과 성취는 내가 프로그래밍을 좋아하기에 충분한 이유가 되었다.


C언어에 어느정도 익숙해지고 나니 자연스럽게 **C언어 개발자는 어떤 일을 할 수 있을까?** 에 대한 고민이 생겼다. 새로운 언어에 대한 관심보다 당장 써먹을 수 있는 분야를 찾게 되었다. 그렇게 임베디드 분야에 대해 알게 되었고 하드웨어에 대한 이해도를 높이고, 다양한 프로젝트를 경험해보고자 전자공학을 복수전공으로 이수하게 되었다.


## 교환학생을 통해 받은 자극

2021-2학기에 오스트리아 인스브루크 MCI 대학으로 교환학생을 다녀올 기회가 있었다. 20살에 한달 유럽 베낭여행을 다녀오면서 언젠가 꼭 다시 오겠노라 다짐하고, 군대에서부터 토플공부를 하면서 영어 공부를 정말 열심히 했었다. 마침 학과에서 진행하는 해외 교류 프로그램이 있었고, 코로나도 겹치고 경쟁률이 낮았던 시기였어서 운좋게도 그토록 원하던 유럽에 교환학생을 다녀올 수 있었다.


현지에서 만나 로봇손 프로젝트도 같이 하고 학업 외적으로 추억도 많이 쌓은 독일인 친구가 있었는데 한국 학교에서는 만나볼 수 없었던 완전 임베디드 공돌이였다. 학교에서 뿐만 아니라 집에서도 이것저것 가져다가 만드는 걸 좋아하는 내가 생각하는 **찐 개발자!** 아직도 기억나는게 학교에서 공모전 같은걸 열어서 우승팀에게 다양한 개발부품(라즈베리파이, 블루투스 모듈 등등)을 살 수 있는 인터넷 사이트의 현금성 쿠폰을 상금으로 주었다. 그때 그 친구네 팀이 거기서 최우수상을 받았는데 미리 해당 사이트에서 찜해둔 것들을 상금으로 사면서 굉장히 들떠있는 모습을 보고, 개발을 즐길 줄 안다는게 이런거구나 생각했다.


## 임베디드 개발자

2023년 임베디드 개발자로 첫 커리어를 시작하게 되었다. 현재는 500명 규모의 회사고 내가 맡는 업무는 CT, C-arm과 같은 X-ray 시스템에 들어가는 디텍터의 펌웨어 로직을 설계하고 구현하는 업무를 맡고 있다. 임베디드 분야는 하드웨어에 대한 이해를 기반으로 소프트웨어를 설계하기 때문에 진입장벽이 높고 아무래도 한번 자리를 잡아두면 대체되기 힘들다는 장점이 있다고 생각한다. 또, 특정한 모듈(메모리, Phy, FPGA)에만 갇혀 있는 것이 아니라 다양한 컴포넌트들을 유기적으로 연결하는 **시스템** 을 다룬다는 점도 큰 그림을 보고 개발 역량을 쌓아가는데 좋은 기반이 되어줄 것 같다.


나는 임베디드 개발자가 가질 수 있는 강점 중 하나는 **기본기**에 있다고 생각한다. 임베디드 분야는 야생에 가깝다. 그 어떤 것도 편하게 개발할 수 없다. 예를 들어, 윗단에서는 read, write 함수와 같이 시스템콜을 호출하면 메모리에서 원하는 데이터를 읽어올 수 있겠지만 임베디드 분야에서는 read, write 와 같은 역할을 하는 시스템콜 자체를 직접 구현해야 한다. 이 과정에서 메모리 자체의 동작 원리를 이해해야 하고 IC 마다 다른 사양을 이해하고 호환성을 맞춰주는 작업이 필요하다. 이러한 경험은 컴퓨터구조, 운영체제와 같이 컴퓨터과학의 근본적인 내용을 이해하는데 도움이 된다. 


두번째 강점은 **소프트웨어를 바라보는 시각**이 넓다는 점이다. 예를 들어, 우리는 소프트웨어 로직의 비효율성에 대해 이야기를 할때 흔히 시간복잡도를 고려하는 경우가 많다. 여기서 임베디드 개발자는 **왜 시간 복잡도가 높을까?** 라는 질문에 대한 어느정도 답변이 가능해진다. 원인은 결국 하드웨어 사양에 있기 때문이다. CPU, GPU가 사용하는 레지스터의 크기나 명령어 처리 방식이 영향을 주고 있기 때문이다. 따라서, 특정 알고리즘이나 프로그램에 따른 최적화 작업이 필요할 때 하드웨어의 사양까지 고려해볼 수 있다는 점도 강점이라고 생각한다. 


하지만, 임베디드 개발의 아쉬운 점도 많다. 일단 기술스택이 너무 제한적이다. 물론 개발 분야나 규모에 따라 다르겠지만, 보통 실무를 하다보면 STM 이나 i.MX 와 같이 한번 결정된 MCU를 그대로 몇년, 몇십년을 사용하는 경우가 많다. 따라서, 기술의 트렌드를 반영하기에는 발전 속도가 너무나 느리다. 클라우드, AI 와 같이 데이터 중심의 기술 트렌드와는 다소 거리가 있어서 새로움을 추구하는 개발자에게는 가장 아쉬움이 크게 다가오는 단점이다.


## 개발자로서 다음 단계와 목표

학부 때부터 네트워크, 운영체제와 같이 밑단의 기술에 많은 관심을 가지고 있었고 임베디드 개발자로 근무하며 RTOS, 리눅스, 쉘프로그래밍, 네트워크 프로그래밍 역량을 쌓을 수 있었다. 이러한 역량을 바탕으로 개발자로서 새롭게 성장할 수 있는 분야는 어디일까? 밑단의 시스템에 대한 이해를 기반으로 고객에게 안정적인 서비스를 운영할 수 있도록 책임을 다하는 개발자는 어떨까? 이러한 생각을 바탕으로 **백엔드 개발자로의 전직**에 대해 생각해보게 되었다.


그중 첫번째 목표로 새로운 언어를 습득해보자 생각했다. 임베디드 개발을 하다보니 C/C++에 대한 이해도와 활용 능력은 키울 수 있지만, 개발자로서 기술스택이 너무 갇혀 있다는 생각이 들었다. C/C++ 언어에 대한 이해도를 기반으로 java 를 배워볼까 생각해봤지만, java 를 사용하는 뛰어난 개발자가 너무나 많고, 백엔드 분야에서 앞으로 내가 과연 경쟁력을 가질 수 있을지 고민하게 되었다. 그래서 **Go** 언어를 습득해볼까 한다. 이미 넷플릭스, 드롭박스와 같은 많은 IT 기업들이 선택한 언어이기도 하고 IT 분야에서 핫한 쿠버네티스, 도커 등이 Go 언어가 활용된 시스템이기 때문에 앞으로도 필요한 기술 스택이 되어줄 것이라 생각하기 때문이다.


이제는 나도 **즐기는 개발자**가 되려고 한다. 교환학생 기간동안 만난 친구에게 받았던 자극을 나도 누군가에게 줄 수 있는 개발자가 되고 싶다. 지금은 소켓 프로그래밍을 회사와는 별개로 공부하고 있다. C언어를 기반으로 작성하고 있는 프로그램을 Go 언어로 번역하는 작업부터 시작해보려고 한다. 앞으로의 6개월, 1년, 5년을 그리며 나도 언젠가 기술 컨퍼런스의 발표자가 되고, 인터뷰도 하는 그런 시니어 개발자로 성장하고 싶다. 따라서, 지금은 기술에 대한 이해도를 높이고 성장해야 할 때라고 생각한다. 특정한 기술 스택에 갇혀 있지 않고 AI, 클라우드, 보안 분야에 지속적인 관심을 가지고 나의 커리어에 어떻게 녹여낼 수 있을지 고민하며 개발을 해내갈 생각이다.
