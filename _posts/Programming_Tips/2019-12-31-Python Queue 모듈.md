---
layout: post
title: ⭐️Python pop()에 관하여, Queue 사용법⭐️
categories : [프로그래밍 TIPS]
tags: [Python,pop(),Queue,queue]
---

파이썬 pop() 함수
-----
<hr> 

- 파이썬의 list나 tuple을 이용하여 큐(queue)를 구현할때 l.pop(0)을 활용하는 경우가 많다. 물론 틀린 방법은 아니지만 효율적이지 않다.

![time_complexity](/assets/img/programmingskill/2019_12_31/time_complexity.png)

- 위의 표는 dict 의 pop을 제외시킨 것이다.

list의 0번째면 첫번째 인덱스니 l.pop(0)도 O(1)이 맞지 않나? 하는 의문을 갖을 수 있다. 하지만 잘 생각해보면 list나 tuple이나 결국 배열의 형태이다. 첫번째 원소를 없애면 그 공간을 채우기위해서 앞쪽으로 당겨야한다. 

![whyBON](/assets/img/programmingskill/2019_12_31/why_BN.gif)

- 앞에 빈 공간을 채우기 위해 list를 한번 다 돌기 때문에 O(N)이라는 시간복잡도가 나온다.


- 그럼 Queue를 구현하기 위해 가장 좋은 방법은??

<br><br>

Python Queue 모듈
-----
<hr>
- 파이썬에 Queue라는 모듈이 존재한다.
- 우선 사용하기 위해서는 import queue를 사용해야 한다.


- queue.Queue(size)
  - FIFO  Queue 형태의 객체 생성
  - size에 최대 사이즈를 추가한다.
  - size를 비워두면 크기는 메모리 범위안에서 무한이다.

- queue.LifoQueue(size)
  - LIFO Stack 형태의 객체 생성
  - stack은 그냥 list나 tuple에서 pop()으로 구현 가능하므로 패스

- queue.PriorityQueue(size)
  - 우선순위 큐 객체를 생성
  - (priority, value)의 튜플로 입력된다.
  - priority가 작을수록 높은 순위를 갖는다.

<br><br>

Python Queue 메소드
-----
<hr>
- qsize() 
  - 객체의 크기를 반환해준다.
  
- put()
  - 객체에 value를 넣어준다.
  - Priority Queue는 put(순위,아이템) 이런식으로 사용하면 된다.

- get()
  - 큐로 따지면 dequeue를 해주는 메소드

- put_nowait()
  - 블로킹 없이 객체에 value를 입력한다.
  - 크기를 지정해줬다면 queue.Full 예외 발생

- get_nowait()
  - 블로킹 없이 객체에 value를 반환한다.
  - 객체가 비어있다면 queue.Empty 예외 발생

<br>
- 여기서 블로킹은 큐 크기의 한계에 다다르면 put_nowait()을 사용하지 않고 put()을 사용하면 무한으로 대기한다. 이를 방지하기 위해 put_nowait()을 사용하여 예외 발생시킨다.


<br><br>

Python Queue 모듈 사용법
-----
<hr>

<br>

<h3>Queue 사용법</h3>

```python
import queue

q=queue.Queue()
q.put('0')
q.put('1')
q.put('2')

print(q.qsize())
print(q.get())
print(q.get())
print(q.size())

'''
출력 결과 : 

3
0
1
1
'''
```


<br>

<h3>Priority Queue</h3>

```python
import queue

q=queue.PriorityQueue()
q.put((5,'2'))
q.put((10,'3'))
q.put((1,'1'))

print(q.get())
print(q.get())

'''
출력 결과 : 

(1, '1')
(5, '2')
'''
```



<br><br>


출처
-----
<hr>

https://wayhome25.github.io/python/2017/06/14/time-complexity/

https://docs.python.org/3/library/queue.html

https://medium.com/@shuangzizuobh2/how-well-do-you-code-python-9bec36bbc322