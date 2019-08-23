---
title: "파이썬 과제 - 터틀을 이용한 리듬게임"
date: 2019-06-02 08:26:28 -0400
categories: Assignment
tags: 파이썬 과제 터틀 리듬게임
---

이번에 파이썬을 처음 배우면서, 터틀을 이용한 게임을 제작하게 되었다.

'두근두근 파이썬' 교재 내에서 나왔던 예제들을 활용하여 새로운 것을 만드는 일은 생각보다 더 재미있었다.

개인적으로 예전부터 리듬게임을 즐겨왔기 때문에 프로그래밍을 배우게 된다면 훗날 리듬게임을 만들어보고 싶다는 생각을 했었다. 마침 이번에 배운 내용들로 간단한 리듬게임을 제작할 수 있을 것 같아서 실행으로 옮겨봤다.

* 애스터로이드 게임에서 랜덤 오브젝트를 생성시키고 이동하는 것을,
거북이 대포 게임에서 부딪혔을 때의 판정 표시를,
터틀런에서 화면 설정과 움직임 주기를 이용하자고 생각했다.

그 결과는 이렇다.
![](/img/시작화면.png)

70년대 게임도 컬러풀하거늘 나의 조악한 디자인 센스를 깨달을 수 있었다!

아마도 아직 더 만드는 중이어서 디자인에 미처 신경을 쓰지 못한 것이다.

기왕 만들었으니 코드를 옮겨봤다.

<b> 함수 추가
<br>
  
> 판정을 1초만 띄우고 사라지게 하고 싶어서 방법을 궁리하던 중 time.sleep() 함수를 알게되어 이를 사용하기 위해 time 패키지를 넣었다.

<pre><code>import turtle as t
import random #랜덤 노트 발생 시 사용
import time
</code></pre>

<br>
<b>시작할 때</b>
<pre><code>t.color
t.up()
t.hideturtle()
t.goto(0,50)
t.write("Rhythm Game", False, "center",("",15))
</code></pre>

<br>
<b>스코어와 penalty 판정 0 으로 초기화</b>

<pre><code>score = 0
penalty = 0
</code></pre>

<br>
<b>판정, 스코어, 패널티 표시할 터틀들을 각각 p, s, pn으로 추가</b>
<br>

>삽입된 turtle들은 추가하는 즉시 화살표 모양으로 화면에 나타나 있다. 화살표 모양을 없애고 싶을 때 hideturtle()로 보이지 않게 할 수 있다.

<pre><code>p = t.Turtle() # 판정 표시할 터틀 추가
p.hideturtle()
s = t.Turtle() # 스코어 표시할 터틀 추가
s.up()
s.hideturtle()
s.goto(180,0)
s.down()
s.color("white")
s.write("SCORE: %s" % score, False, "center", ("", 15))
pn = t.Turtle() # 패널티 표시할 터틀 추가
pn.up()
pn.hideturtle()
pn.goto(180,-50)
pn.down()
pn.color("gray")
pn.write("PENALTY: %s" % penalty, False, "center", ("", 15))
</code></pre>

<br>
<b>떨어지는 노트 설정</b>
<br>

>사각형 모양 터틀을 떨어지는 노트로 생성

<pre><code>note_x = 0
note = t.Turtle()
note.shape("square")
note.color("white")
</code></pre>

<br>
<b>gameover 함수 생성</b>

<pre><code>def gameover(): # 게임오버일 때 게임오버 메시지 띄움
    playing = False
    t.goto(0,0)
    t.down()
    t.color("yellow")
    t.write("Game Over", False, "center", ("", 15))
    exit() # 자동으로 게임 종료
</code></pre>

<br>
<b>play 함수 생성</b>
<pre><code>def play():
    global score # 점수계산
    global note_x # 노트의 x좌표값
    global penalty # 눌렀을 때 타이밍 안맞거나 완전히 놓쳤을 때 쌓임. 5개 쌓이면 게임 오버
    while True:
        if penalty>=5:
            gameover()
            break # penalty판정 5개 이상 받으면 게임이 끝남
        list = [-175,-105,-35,35] # 떨어뜨리는 위치(좌우값) 리스트로 설정
        random.shuffle(list) # 리스트 섞어서
        note_x = list[1] # 첫번째 항목을 떨어뜨리는 x값으로 설정
        note.up() # 노트 지나다니는 선 표시 없애기
        note.hideturtle() # 노트 숨기기
        note.goto(note_x, 200) # 노트 이동
        note.speed(1) # 노트 내려오는 속도
        note.setheading(270) # 밑으로 내려오게 하려면 밑을 바라보게 회전
        note.showturtle() # 노트 보이기
        while True:
            note.forward(10) # 노트 내려오기
            if note.ycor()==-200: break
        if note.ycor()==-200: # 클릭 안하고 노트가 내려갔을 때 MISS 판정, 패널티+1
            p.up()
            p.hideturtle()
            p.goto(-70, 0)
            p.color("red")
            p.write("MISS", False, "center", ("", 20))
            penalty = penalty + 1
            pn.clear()
            pn.write("PENALTY: %s" % penalty, False, "center", ("",15)) # 패널티 재작성
            time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
            p.clear()
</code></pre>

* <code>while True:</code> 패널티 5 쌓일 때까지 반복한다.

* <code>list = [-175,-105,-35,35]</code> 바닥 오브젝트가 깔리는 X좌표 위치이다.

* <code>random.shuffle(list)</code> 무작위의 위치에 노트가 생성되도록 리스트 항목을 섞어준다.

* <code>pn.clear()</code> 점수나 패널티 표시가 겹치므로 그때그때 지웠다가 재작성해주는 코드이다.

<br>
<b>창 설정</b>
<pre><code>t.setup(500,500)
t.bgcolor("black")
</code></pre>

<br>
<b>플레이 시작 전</b>
<br>

> 플레이 시작 전후 장면 전환을 위해 False 상태로 둔다.

<pre><code>playing=False</code></pre>

<br>
<b>start 함수</b>
<br>

>Spacebar를 누를 시 실행된다. (<b>키 입력 받기</b> 부분 코드 참고)

<pre><code>def start():
    global playing
    if playing==False:
        playing=True
        t.clear()
        play()
</code></pre>

<br>
<b>downpart 함수</b>
<br>

> 판정을 위한 바닥 오브젝트를 생성한다.
여기에선 선을 4개 그려서 4개의 판정 선을 생성했다.

<pre><code>def downpart(x,y,z,w):
    x=t.Turtle() # 바닥에 깔린 오브젝트 1
    x.color("white")
    x.pensize(3)
    x.up()
    x.goto(-200,-150)
    x.down()
    x.goto(-150,-150)
    x.hideturtle() # 화살표 모양 거북이 숨기기
    y=t.Turtle() # 바닥에 깔린 오브젝트 2
    y.color("white")
    y.pensize(3)
    y.up()
    y.goto(-130,-150)
    y.down()
    y.goto(-80,-150)
    y.hideturtle() # 화살표 모양 거북이 숨기기
    z=t.Turtle() # 바닥에 깔린 오브젝트 3
    z.color("white")
    z.pensize(3)
    z.up()
    z.goto(-60,-150)
    z.down()
    z.goto(-10,-150)
    z.hideturtle() # 화살표 모양 거북이 숨기기
    w=t.Turtle() # 바닥에 깔린 오브젝트 4
    w.color("white")
    w.pensize(3)
    w.up()
    w.goto(10,-150)
    w.down()
    w.goto(60,-150)
    w.hideturtle() # 화살표 모양 거북이 숨기기
a=t.Turtle()
b=t.Turtle()
c=t.Turtle()
d=t.Turtle()
downpart(a,b,c,d) # 바닥 4개 깔기 완료!
</code></pre>


<br>

<b>키 함수</b>
<br>

> 아래는 숫자 '1'키를 누르면 실행되는 함수이다.

<pre><code>def onekey():
    global score
    global penalty
    d=note.distance(note_x,-150) # 노트와 바닥 오브젝트 사이의 거리 구하기
    if d<5: # 거리 차이가 5보다 작으면 PERFECT 판정, 2점 추가
       p.up()
       p.hideturtle()
       p.goto(-175,-120)
       p.color("pink")
       p.write("PERFECT",False,"center",("",15))
       score=score+2
       s.clear()
       s.write("SCORE: %s" % score, False, "center",("",15))
       time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
       p.clear()
       play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    elif d<10: # 거리 차이가 10보다 작으면 GOOD 판정, 1점 추가
        p.up()
        p.hideturtle()
        p.goto(-175,-120)
        p.color("blue")
        p.write("GOOD",False,"center",("",15))
        score=score+1
        s.clear()
        s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    else: # 클릭했으나 틀리는 경우 BAD 판정, 점수 없음, 패널티+1
        p.up()
        p.hideturtle()
        p.goto(-175,-120)
        p.color("red")
        p.write("BAD",False,"center",("",15))
        penalty=penalty+1
        pn.clear()
        pn.write("PENALTY: %s" % penalty, False, "center",("",15)) # 패널티 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
</code></pre>

* <code>time.sleep()</code> 여기서 time 패키지가 사용된다.

* <code>play()</code> 이미 판정이 한 번 뜬 상태에서 노트가 멈추지 않고 내려오면 MISS가 계속 뜨므로 이를 방지하기 위해 play()를 재호출하여 새로운 노트를 내보낸다.

<br>

> 남은 3개의 바닥 오브젝트들도 위와 같이 코딩한다.

<pre><code>def twokey():
    global score
    global penalty
    d=note.distance(note_x,-150) # 노트와 바닥 오브젝트 사이의 거리 구하기
    if d<5: # 거리 차이가 5보다 작으면 PERFECT 판정, 2점 추가
       p.up()
       p.hideturtle()
       p.goto(-105,-120)
       p.color("pink")
       p.write("PERFECT",False,"center",("",15))
       score=score+2
       s.clear()
       s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
       time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
       p.clear()
       play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    elif d<10: # 거리 차이가 10보다 작으면 GOOD 판정, 1점 추가
        p.up()
        p.hideturtle()
        p.goto(-105,-120)
        p.color("blue")
        p.write("GOOD",False,"center",("",15))
        score=score+1
        s.clear()
        s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    else: # 클릭했으나 틀리는 경우 BAD 판정, 점수 없음, 패널티+1
        p.up()
        p.hideturtle()
        p.goto(-105,-120)
        p.color("red")
        p.write("BAD",False,"center",("",15))
        penalty=penalty+1
        pn.clear()
        pn.write("PENALTY: %s" % penalty, False, "center",("",15)) # 패널티 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
def threekey():
    global score
    global penalty
    d=note.distance(note_x,-150) # 노트와 바닥 오브젝트 사이의 거리 구하기
    if d<5: # 거리 차이가 5보다 작으면 PERFECT 판정, 2점 추가
       p.up()
       p.hideturtle()
       p.goto(-35,-120)
       p.color("pink")
       p.write("PERFECT",False,"center",("",15))
       score=score+2
       s.clear()
       s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
       time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
       p.clear()
       play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    elif d<10: # 거리 차이가 10보다 작으면 GOOD 판정, 1점 추가
        p.up()
        p.hideturtle()
        p.goto(-35,-120)
        p.color("blue")
        p.write("GOOD",False,"center",("",15))
        score=score+1
        s.clear()
        s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    else: # 클릭했으나 틀리는 경우 BAD 판정, 점수 없음, 패널티+1
        p.up()
        p.hideturtle()
        p.goto(-35,-120)
        p.color("red")
        p.write("BAD",False,"center",("",15))
        penalty=penalty+1
        pn.clear()
        pn.write("PENALTY: %s" % penalty, False, "center",("",15)) # 패널티 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
def fourkey():
    global score
    global penalty
    d=note.distance(note_x,-150) # 노트와 바닥 오브젝트 사이의 거리 구하기
    if d<5: # 거리 차이가 5보다 작으면 PERFECT 판정, 2점 추가
       p.up()
       p.hideturtle()
       p.goto(35,-120)
       p.color("pink")
       p.write("PERFECT",False,"center",("",15))
       score=score+2
       s.clear()
       s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
       time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
       p.clear()
       play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    elif d<10: # 거리 차이가 30보다 작으면 GOOD 판정, 1점 추가
        p.up()
        p.hideturtle()
        p.goto(35,-120)
        p.color("blue")
        p.write("GOOD",False,"center",("",15))
        score=score+1
        s.clear()
        s.write("SCORE: %s" % score, False, "center",("",15)) # 점수 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
    else: # 클릭했으나 틀리는 경우 BAD 판정, 점수 없음, 패널티+1
        p.up()
        p.hideturtle()
        p.goto(35,-120)
        p.color("red")
        p.write("BAD",False,"center",("",15))
        penalty=penalty+1
        pn.clear()
        pn.write("PENALTY: %s" % penalty, False, "center",("",15)) # 패널티 재작성
        time.sleep(1) # 판정 1초 뒤 사라지게 하기위해 time.sleep()함수 사용
        p.clear()
        play() # 입력받아 판정 뜬 노트가 MISS 띄우는 일 방지
</code></pre>

<br>
<b>키 입력 받기</b>
<br>

> 숫자키패드 1&2&3&4키를 누르면 각 버튼이 입력되도록 키 입력을 받는다.
키 입력 시 각 함수가 실행된다.

<pre><code>t.onkeypress(onekey,"1") # 1키 누르면  onekey 함수 실행
t.onkeypress(twokey,"2")
t.onkeypress(threekey,"3")
t.onkeypress(fourkey,"4")
t.onkeypress(start,"space")
t.listen()</code></pre>

<br>
<b>소감</b>

랜덤하게 생성된 노트들이 내려오는 식으로 프로그램을 짰기에 음악을들으며 플레이하는 게임은 아니라는 점이 살짝 아쉬웠지만, 나름 처음 짜보는 게임이므로 이렇게나마 완성한 것만으로도 충분히 뿌듯했다.
디자인적인 감각이 부족해서 먼 옛날에서 건너온 것만 같은 비주얼이지만, 앞으로도 계속 공부하다 보면 디자인 방면으로도 프로그래밍 방면으로도 점점 능력이 길러질 것이라고 믿는다.
자연과학대학 학생으로서 스스로 게임을 만들어볼 기회가 별로 없었으나 연계전공 파이썬 수업을 듣게 되어 배운 내용을 바탕으로 게임을 만들어본 것은 좋은 기회였다. 확실히 직접 코드를 짜는 과정에서 많은 공부를 하게 된 것 같다.
인생 첫 게임을 만들어본 것에 대한 만족도는 별 다섯 개 만점이라 할 수 있겠다.
