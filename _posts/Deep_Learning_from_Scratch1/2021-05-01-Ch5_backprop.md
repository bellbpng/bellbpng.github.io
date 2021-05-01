---
layout: single
title:  "Deep Learning from Scratch1 - Ch5 역전파"
excerpt: "연쇄법칙과 역전파, 단순계층(곱셈,덧셈) 구현"

categories:
  - Deep Learning from Scratch1
# tags:
#   - [ML, Python]

toc: true
toc_sticky: true
---
# 역전파(backpropagation)
- 국소적 미분을 순방향과 반대방향으로 진행하여 전달한다.

## 연쇄법칙(chain rule)과 합성함수의 미분
- 역전파의 국소적 미분을 전달하는 원리

<img src="https://user-images.githubusercontent.com/59792046/116776688-d7fd6b00-aaa4-11eb-93d4-f22b9070d153.png">

- 덧셈노드의 역전파는 싱류의 값을 그대로 전달한다.
- 곱셈노드의 역전파는 곱셈 노드로 들어오는 입력신호를 서로 바꾸어 하류로 전달한다.

## 사과와 귤 쇼핑의 역전파 구하기(계산그래프 포스트에서 소개한 내용)

<img src = "https://user-images.githubusercontent.com/59792046/116776792-6e319100-aaa5-11eb-8987-b855dcc36910.png">

## 곱셈과 덧셈계층 구현
### 곱셈계층
```python
class MulLayer:
    def __init__(self):
        self.x = None
        self.y = None
    
    def forward(self, x, y):
        self.x = x
        self.y = y
        out = x*y

        return out

    def backward(self, dout):
        dx = dout*self.y
        dy = dout*self.x

        return dx, dy
```
- 곱셈노드의 경우 입력값을 모두 기억해두어야하므로 인스턴스변수로 선언한다.

### 덧셈계층
```python
class AddLayer:
    def __init__(self):
        pass

    def forward(self, x, y):
        out = x+y
        return out

    def backward(self, dout):
        dx = dout*1
        dy = dout*1

        return dx,dy
```
- 덧셈노드의 경우 상류값을 하류로 그대로 흘려보내기 때문에 별도의 인스턴스 변수가 필요하지 않다. 따라서 pass로 초기화 단계를 건너뛴다.
