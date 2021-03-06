---
title: "머신러닝 기초 01"
categories: 
  - study
tags: 
    - lecture
    - machine learning
    - youtube
    - python
    - 인공지능
    - 머신러닝
    - tensorflow
toc: true
toc_sticky: true
comments:  true
---

하루 8시간 일주일 중 5일 회사에서 있는 시간은 정말 길다. 와이프보다 오래 같이 있는 회사 사람들인데 같은 팀 내에서 학습에 대한 지속적인 자극을 주는 사람이 있다는건 굉장히 축복받은 것같다.   

나에게 질문을 자주 던져주는 백엔드 개발자가 있는데 지금 이분이 머신러닝관련 선형대수같은 수학을 공부하고있다. 평일은 수학 주말은 React를 강좌를 들으며 공부하고있다고한다.  

```머신러닝``` 은 정말 똑똑하고 공부 잘하는 사람들이 하는거라 생각했고 사실 크게 관심도 없었다. 최근 머신러닝 , 딥려닝, 데이터분석, 빅데이터 등 의 단어들이 유행하면서 이와 관련한 국비지원 교육도 계속 등장하고 있는것같다.  

아직 사실상 내가 업무하는 분야에서는 크게 쓰임새가없고 중요도도 못느끼고 이걸 배워서 과연 내가 뭘 할 수 있을지도 모르기때문에 등한시하고 있었는데 팀내에 백엔드개발자가 공부하는걸 보고 이걸 왜 공부하냐 물어보니 그냥 이라고 한다.   

그래서 나도 그냥 그래도 뭔지는 기본적인 개념정도는 잡고있어야 하지않을까 싶어서 찾던와중 ```TensorFlow``` 라는걸 보게되었고 이게 머신러닝 사용을 더 쉽게 만들어준다고 하여 조금은 가벼운 마음으로 youtube 에 공개된 한글강좌를 찾아서 보고 정리를 해 두려고 한다.

## 머신러닝(Machine Learning)이란
기계가 학습하는것이라고 간략하게 정의할 수 있을것같다.  
단순히 알고리즘에 의해 돌아가는것이 아니라 기계가 스스로 학습을 통해서 문제 해결을 하거나 특정한 결과값을 도출하거나 하는등의 행위라고 볼 수 있을것같다.

## 선형 회귀 (Linear Regression)
선형회귀란 변수 사이의 선형적인 관계를 모델링 한것  
일련의 데이터들을 그래프에 점을 찍고 그 점들을 직선으로 이으면 선형데이터가 된다.  
선형회귀모델을 구축해 학습을 시킨다는건 주어진 데이터를 학습시켜서 가장 합리적인 직선을 찾아 내는 것이라고 볼 수 있다.  
따라서 이 데이터의 조건은 최소 3개 이상의 데이터일때 의미가 있을 수 있다.  

주어진 데이터들을 이용해 계속해서 식을 수정해 나가며 가장 합리적인 식을 찾아내는데 이를 위해서는 가설을 세워야한다.  
> H(x) = Wx + b  

하나의 일차방적식을 이용해 직선을 표현할 수 있다.  


**비용**  
가설이 얼마나 정확한지 판단하는 기준  
하나의 가설을 세운 선형데이터를 토대로 이 데이터가 실제 데이터와의 차이가 얼마나 되는지를 구하는것이 비용이다.

**비용함수**  
(예측값 - 실제값)2제곱근 의 평균  
비용함수로 구한 비용이 적을수록 좋은것

**경사하강**  
경사하강을 통해 합리적인 식을 도출  
경사를 타고 내려가며 가장 기울기가 낮은 합리적인 식을 도출하는데 만약 이 경사를 타고 내려가는폭이 너무 크다면 정확한 값을 산출하기 힘들고 너무 폭이 작다면 식을 세우는데 많은 시간이 소요되게된다.  
(텐서플로우는 이미 이러한 경사하강 라이브러리를 제공한다.)

``` python
import tensorflow as tf
xData = [1, 2, 3, 4, 5, 6, 7]
yData = [25000, 50000, 750000, 110000, 1280000, 1550000, 1800000]

W = tf.Variable(tf.random_uniform([1], -100, 100))
b = tf.Variable(tf.random_uniform([1], -100, 100))
X = tf.placeholder(tf.float32)
Y = tf.placeholder(tf.float32)

H = W * X + b

cost = tf.reduce_mean(tf.square(H - Y))
a = tf.Variable(0.01) # 얼마나 점프할건지 
optimizer = tf.train.GradientDescentOptimizer(a) # 경사하강 기울기 구하기

train = optimizer.minimize(cost)
init = tf.global_variables_initializer()
sess = tf.Session()
sess.run(init)

# 학습을 진행 5000번
for i in range(5001):
    sess.run(train, feed_dict={X: xData, Y: yData})
    if i % 500 == 0:
        print(i, sess.run(cost, feed_dict={X: xData, Y: yData}), sess.run(W), sess.run(b))

# 원하는 입력에 대한 결과값 출력
print(sess.run(H, feed_dict={X: [8]}))
```