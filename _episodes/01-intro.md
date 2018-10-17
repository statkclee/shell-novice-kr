---
title: "쉘(Shell) 소개"
teaching: 5
exercises: 0
questions:
- "명령쉘(command shell)이 무엇이고 왜 쉘을 사용해야 하는가?"
objectives:
- "쉘(shell)이 어떻게 키보드, 화면, 운영체제, 사용자 프로그램에 연관되는지 설명한다."
- "명령-라인(CLI, command-line interface) 인터페이스가 화면 사용자 인터페이스(GUI, graphic user interface)대신에 언제, 왜 사용되어야 하는지 설명한다."
objectives:
- "쉘 읽기-실행-출력(read-run-print) 사이클 단계를 설명한다."
- "명령어 대부분은 `-`으로 시작하는 플래그(옵션)을 갖는다."
- "명령라인 호출에 사용되는 실제 명령어, 플래그, 파일명을 식별한다."
- "탭완성(tab completion) 사용법을 시연하고 장점을 설명한다."
keypoints:
- "쉘은 프로그램으로 주된 목적은 명령을 읽고 다른 프로그램을 실행시키는 것이다."
- 쉘의 주된 장점은 높은 액션대비 키보드 입력비율이다. 예를 들어 반복되는 작업을 자동화하는 것과 네트워크 컴퓨터에 접속하는 능력이 포함된다."
- "쉘의 주된 단점은 주로 태생이 텍스트라는 것과 명령어와 동작이 수수께끼 같다는 점이다."
---

### 배경

상위 수준에서 컴퓨터는 네가지 일을 수행한다:

-   프로그램 실행
-   데이터 저장
-   컴퓨터간 상호 의사소통
-   사람과 상호작용


마지막 작업을 뇌-컴퓨터 연결, 음성 인터페이스를 포함한 다양한 많은 방식으로 수행한다. 
하지만, 아직은 초보적인 수준이어서, 대부분은 WIMP((Window) 윈도우, (Icon)아이콘, (Mouse)마우스, (Pointer)포인터)를 사용한다. 
1980년대까지 이러한 기술은 보편적이지 않았지만, 기술의 뿌리는 1960년대 Doug Engelbart의 작업에 있고, 
"[The Mother of All Demos](http://www.youtube.com/watch?v=a11JDLBXtPQ)"로 불리는 것에서 볼 수 있다.

조금 더 멀리 거슬러 올라가면, 초기 컴퓨터와 상호작용하는 유일한 방법은 와이어로 다시 연결하는 것이다. 
하지만, 중간에 1950년에서 1980년 사이 대부분의 사람들이 라인 프린터(line printer)를 사용했다. 
이런 장치는 표준 키보드에 있는 문자, 숫자, 특수부호의 입력과 출력만 허용해서, 
프로그래밍 언어와 인터페이스는 이러한 제약사항에서 설계됐다.

여전히 전통적인 화면, 마우수, 터치패드, 키보드를 사용하지만 터치 인터페이스와 음성 인터페이스가 보편화되고 있다.

이런 종류의 인터페이스를 지금 대부분의 사람들이 사용하는 **그래픽 사용자 인터페이스(GUI, graphical user interface)**과 구별하기 위해서 **명령-라인 인터페이스(CLI, command-line interface)**라고 한다. 
CLI의 핵심은 **읽기-평가-출력(REPL,read-evaluate-print loop)**이다: 사용자가 명령어를 타이핑하고 엔터(enter)/반환(return)키를 입력하면, 
컴퓨터가 읽고, 실행하고, 결과를 출력한다. 
그러고 나면, 사용자는 다른 명령를 타이핑하는 것을 로그 오프해서 시스템을 빠져 나갈때까지 계속한다.

GUI는 WIMP((Window) 윈도우, (Icon)아이콘, (Mouse)마우스, (Pointer)포인터)로 구성되는데 배우기 쉽고, 단순 작업에 대해서는
환상적이다. "클릭"하게 되면 명령이 "내가 원하는 작업을 수행해"라고 손쉽게 컴퓨터에 통역된다. 
하지만, 이런 마술은 단순한 작업을 수행하고, 정확하게 이러한 유형의 작업을 수행할 수 있는 프로그램에 불과하다.

만약 복잡하고, 특정 목적에 부합되는 훨씬 묵직한 작업을 컴퓨터에 내리고자 한다고 해서,
난해하거나 어렵거나할 필요는 없고, 단지 명령 어휘가 필요하고 이를 사용하는데 필요한 단순한 문법만 필요로 한다.

쉘이 이런 기능을 제공한다 - 단순한 언어로 이를 사용하는데 **명령-라인 인터페이스**가 필요하다.
명령라인 인터페이스의 심장은 **읽기-평가-출력(REPL,read-evaluate-print loop)**이다.
REPL로 불리는 이유는 쉘에 명령어를 타이핑하고 <kbd>Return</kbd>를 치게되면 컴퓨터가 명령어를 읽어들이고 나서,
평가(혹은 실행)하고 출력결과를 화면에 뿌린다. 또 다른 명령어를 입력할 때까지 대기하는 루푸를 반복하게 되서 그렇다.


상기 묘사가 마치 사용자가 직접 명령어를 컴퓨터에 보내고, 
컴퓨터는 사용자에게 직접적으로 출력을 보내는 것처럼 들린다. 
사실 중간에 **명령 쉘(command shell)**로 불리는 프로그램이 있다. 
사용자가 타이핑하는 것은 쉘로 간다. 
쉘은 무슨 명령어를 수행할지 파악해서 컴퓨터에게 수행하도록 지시한다.
쉘을 *조개(shell)*로 불리는데 이유는 운영체제를 감싸서, 
복잡성 일부를 숨겨서 운영체제와 더 단순하게 상호작용하게 만든다. 


### 쉘(Shell)

쉘은 다른 것과 마찬가지로 프로그램이다. 
조금 특별한 것은 자신이 연산을 수행하기 보다 다른 프로그램을 실행한다는 것이다. 
가장 보편적인 유닉스 쉘(Unix Shell)은 Bash(Bourne Again SHell)다. 
Stephen Bourne이 작성한 쉘에서 나와서 그렇게 불리우고 --- 프로그래머 사이에 재치로 통한다. 
Bash는 대부분의 유닉스 컴퓨터에 기본으로 장착되는 쉘이고, 
윈도우용으로 유닉스스런 도구로 제공되는 패키지 대부분에도 적용된다.

Bash나 다른 쉘을 사용하는 것이 마우스를 사용하는 것보다 프로그래밍 작성하는 느낌이 난다. 
명령어는 간략해서 (흔히 단지 2~3자리 문자다), 명령어는 자주 암호스럽고, 
출력은 그래프같이 시각적인 것보다 텍스트줄로 쭉 뿌려진다. 
다른 한편으로, 쉘을 사용하여 좀더 강력한 방식으로 현존하는 도구를 단지 키보드 입력값 몇개를 조합해서 대용량의 데이터를 자동적으로 처리할 수 있는 파이프라인을 구축할 수 있게 한다. 
추가로, 명령 라인은 종종 멀리 떨어진 컴퓨터 혹은 슈퍼컴퓨터와 상호작용하는 가장 쉬운 방법이다. 
고성능 컴퓨팅 시스템에 포함된 다양한 특화된 도구와 자원을 실행하는데 쉘과 친숙성이 거의 필연적이다.
클러스트 컴퓨팅과 클라우드 컴퓨팅이 과학 데이터 클런칭(scientific data cruching)이 점점 대중화됨에 따라 원격 컴퓨터를 구동하는 것이 필수적인 기술이 되어가고 있다.
여기서 다뤄지는 명령-라인 기술에 기반해서 광범위한 과학적 질문과 컴퓨터적 도전과제를 처리할 수 있다.


### 어떻게 생겼을까?

전형적인 쉘 윈도우는 다음과 같다:

~~~
bash-3.2$ 
bash-3.2$ ls -F / 
Applications/         System/
Library/              Users/
Network/              Volumes/
bash-3.2$ 
~~~

첫번째 줄은 **프롬프트(prompt)**만 보여주고 있고, 쉘이 입력준비가 되었다는 것을 나타낸다.
프롬프트로 다른 텍스트를 지정할 수도 있다. 가장 중요한 것:
명령어를 타이핑할 때, *프롬프트를 타이핑하지 말고*, 인식되거나 수행할 수 있는 명령어만 타이핑한다.



The part that you type,
`ls -F /` in the second line of the example,
typically has the following structure: a **command**,
some **flags** (also called **options** or **switches**) and an **argument**.
Flags start with a single dash (`-`) or two dashes (`--`), and change the behaviour of a command.
Arguments tell the command what to operate on (e.g. files and directories).
Sometimes flags and arguments are referred to as parameters.
A command can be called with more than one flag and more than one argument: but a
command doesn't always require an argument or a flag.

In the second line of the example above, our **command** is `ls`, with a **flag** `-F` and an
**argument** `/`. Each part is separated by spaces: if you omit the space 
between `ls` and `-F` the shell will look for a command called `ls-F`, which 
doesn't exist. Also, capitalization matters: `LS` is different to `ls`. 

Next we see the output that our command produced. In this case it is a listing 
of files and folders in a location called `/` - we'll cover what all these mean 
later today. Those using a macOS might recognize the output in this example.

Finally, the shell again prints the prompt and waits for you to type the next 
command.

In the examples for this lesson, we'll show the prompt as `$ `. You can make your 
prompt look the same by entering the command `PS1='$ '`. But you can also leave 
your prompt as it is - often the prompt includes useful information about who and where 
you are.

Open a shell window and try entering `ls -F /` for yourself (don't forget that spaces
and capitalization are important!). You can change the prompt too, if you like.

### How does the shell know what `ls` and its flags mean?

Every command is a program stored somewhere on the computer, and the shell keeps a
list of places to search for commands (the list is in a **variable** called `PATH`, 
but those are concepts we'll meet later and are not too important at the moment). Recall
that commands, flags and arguments are separated by spaces.

So let's look at the REPL (read-evaluate-print loop) in more detail. Notice that the
"evaluate" step is made of two parts:

1. Read what was typed (`ls -F /` in our example)  
    The shell uses the spaces to split the line into the command, flags, and arguments
2. Evaluate:  
    a. Find a program called `ls`  
    b. Execute it, passing it the flags and arguments (`-F` and `/`) to 
       interpret as the program sees fit 
3. Print the output produced by the program

and then print the prompt and wait for you to enter another command.

> ## Command not found 
> If the shell can't find a program whose name is the command you typed, it 
> will print an error message like:
> 
> ~~~
> $ ls-F
> ~~~
> {: .language-bash}
> ~~~
> -bash: ls-F: command not found
> ~~~
> {: .output}
> 
> Usually this means that you have mis-typed the command - in this case we omitted
> the space between `ls` and `-F`. 
{: .callout}

### 어려운가요?

GUI와 비교하여 컴퓨터와 상호작용하는데 있어 어려운 모형이고 학습하는데 
노력과 시간이 다소 소요도니다.
GUI는 선택지를 보여주고, 사용자가 선택지중에서 선택하는 하는 것이다.
**명령라인 인터페이스(CLI)**로 선택지가 명령어와 패러미터의 조합으로 표현된다.
사용자에게 제시되는 것이 아니라서 새로운 언어의 어휘를 학습하듯이 일부 학습이 필요하다.
명령어의 일부만 배우게 되면 정말 도움이 많이 되고, 금일 핵심적인 명령어를 다뤄볼 것이다.

### 유연성과 자동화

쉘문법(Grammar of Shell)은 기존 도구를 조합해서 강력한 파이프라인을 구축하도록 해서
방대한 데이터를 자동화하여 다룰 수 있다.
명령 순서는 *스크립트(script)*로 작성하여 작업흐름의 재현가능성을 향상시켜서 쉽게 
반복이 가능하도록 한다.

추가로, 명령 라인은 종종 멀리 떨어진 컴퓨터 혹은 슈퍼컴퓨터와 상호작용하는 가장 쉬운 방법이다. 
고성능 컴퓨팅 시스템에 포함된 다양한 특화된 도구와 자원을 실행하는데 쉘과 친숙성이 거의 필연적이다.
클러스트 컴퓨팅과 클라우드 컴퓨팅이 과학 데이터 클런칭(scientific data cruching)이 점점 대중화됨에 따라 원격 컴퓨터를 구동하는 것이 필수적인 기술이 되어가고 있다.
여기서 다뤄지는 명령-라인 기술에 기반해서 광범위한 과학적 질문과 컴퓨터적 도전과제를 처리할 수 있다.

## Nelle의 파이프라인: 시작점

해양 생물학자 넬 니모(Nell Nemo) 박사가 방금전 6개월간 [북태평양 소용돌이꼴](http://en.wikipedia.org/wiki/North_Pacific_Gyre) 조사를 마치고 방금 귀환했다. 
[태평양 거대 쓰레기 지대](http://en.wikipedia.org/wiki/Great_Pacific_Garbage_Patch)에서 젤리같은 해양생물을 표본주출했다. 
총 합쳐서 1,520개 시료가 있고 다음 작업이 필요하다:

1.  서로 다른 300개 단백질의 상대적인 함유량을 측정하는 분석기계로 시료를 시험한다. 
     한 시료에 대한 컴퓨터 출력결과는 각 단백질에 대해 한 줄 파일형식으로 표현된다.
2.  `goostat`으로 명명된 그녀의 지도교수가 작성한 프로그램을 사용하여 각 단백질에 대한 통계량을 계산한다.
3.  다른 대학원 학생중 한명이 작성한 `goodiff`로 명명된 프로그램을 사용해서, 각 단백질에 대한 통계량과 
     다른 단백질에 대해 상응하는 통계량을 비교한다.
4.  결과를 작성한다. 그녀의 지도교수는 이달 말까지 이 작업을 정말로 마무리해서, 
     논문이 다음번 *Aquatic Goo Letters* 저널 특별판에 게재되기를 희망한다.


각 시료를 분석장비가 처리하는데 약 반시간 정도 소요된다. 
좋은 소식은 각 시료를 준비하는데는 단지 2분만 소요된다. 
연구실에 병렬로 사용할 수 있는 분석장비 8대가 있어서, 이 단계는 약 2주정도만 소요될 것이다.

나쁜 소식은 `goostat`, `goodiff`를 수작업으로 실행한다면, 
파일이름 입력하고 "OK" 버튼을 45,150번 눌려야 된다는 사실이다 (`goostat` 300회 더하기 `goodiff` 300×299/2). 매번 30초씩 가정하면 2주 이상 소요될 것이다. 
논문 마감일을 놓칠 수도 있지만, 이 모든 명령어를 올바르게 입력할 가능성은 거의 0 에 가깝다.

다음 수업 몇개는 대신에 그녀가 무엇을 해야되는지 탐색한다. 
좀더 구체적으로, 처리하는 파이프라인 중간에 반복되는 작업을 자동화하는데 쉘 명령어(command shell)를 어떻게 사용하는지 설명해서, 논문을 쓰는 동안에 컴퓨터가 하루에 24시간 작업한다. 
덤으로 중간 처리작업 파이프라인을 완성하면, 더 많은 데이터를 얻을 때마다 다시 재사용할 수 있게 된다.


