---
title: "파이프와 필터"
teaching: 25
exercises: 10
questions:
- "새로운 명령어를 만드는데 기존 명령어를 어떻게 조합할 수 있을까?"
objectives:
- "명령어 출력결과를 파일로 방향을 바꾼다(redirect)."
- "키보드 입력 대신에 방향변경(redirection)을 사용하여 파일을 처리한다."
- "2단계 혹은 3단계를 갖는 명령문 파이프라인을 구성한다."
- "프로그램 혹은 파이프라인에 처리할 입력을 주지 않았을 때 통상 발생하되는 것을 설명한다."
- "`작은 조각, 느슨하게 결합하기(small pieces, loosely joined)`라는 유닉스 철학을 설명한다."
keypoints:
- "`cat` 명령어는 입력 파일에 담긴 내용을 화면에 출력한다."
- "`head` 명령어는 입력 파일의 첫 10줄을 화면에 출력한다."
- "`tail` 명령어는 입력 파일의 마지막 10줄을 화면에 출력한다."
- "`sort` 명령어는 입력 파일을 정렬한다."
- "`wc` 명령어는 입력 파일의 행수, 단어수, 문자수를 센다."
- "`command > file` 명령어는 명령어의 출력결과를 파일로 방향을 변경시킨다."
- "`first | second` 이 파이프라인이다: 첫번째 명령어의 출력이 두번째 명령어의 입력으로 사용된다."
- "쉘을 사용하는 최선의 방법은 파이프를 사용해서 간단한 단일 목적 프로그램(필터)을 조합하는 것이다."
---

몇가지 기초 유닉스 명령어를 배웠기 때문에, 
마침내 쉘의 가장 강령한 기능을 살펴볼 수 있게 되었다: 
새로운 방식으로 기존에 존재하던 프로그램을 쉽게 조합해 낼 수 있게 한다. 
간단한 유기분자 설명을 하는 6개 파일을 담고 있는 `molecules`(분자)라는 디렉토리에서 시작한다. 
`.pdb` 파일 확장자는 단백질 데이터 은행 (Protein Data Bank) 형식으로, 
분자의 각 원자 형식과 위치를 표시하는 간단한 텍스트 형식으로 되어 있다.

~~~
$ ls molecules
~~~
{: .language-bash}

~~~
cubane.pdb    ethane.pdb    methane.pdb
octane.pdb    pentane.pdb   propane.pdb
~~~
{: .output}

명령어 `cd`로 해당 디렉토리로 가서 `wc *.pdb` 명령어를 실행한다. 
`wc` 명령어는 "word count"의 축약어로 파일의 라인 수, 단어수, 문자수를 개수한다. (왼쪽에서 오른쪽 순서로)

`*.pdb`에서 `*`은 0 혹은 더 많이 일치하는 문자를 매칭한다.
그래서 쉘은 `*.pdb`을 통해 `.pdb` 전체 리스트 목록을 반환한다:


~~~
$ cd molecules
$ wc *.pdb
~~~
{: .language-bash}

~~~
  20  156  1158  cubane.pdb
  12  84   622   ethane.pdb
   9  57   422   methane.pdb
  30  246  1828  octane.pdb
  21  165  1226  pentane.pdb
  15  111  825   propane.pdb
 107  819  6081  total
~~~
{: .output}


`wc` 대신에 `wc -l`을 실행하면, 출력결과는 파일마다 행수만을 보여준다:

~~~
$ wc -l *.pdb
~~~
{: .language-bash}

~~~
  20  cubane.pdb
  12  ethane.pdb
   9  methane.pdb
  30  octane.pdb
  21  pentane.pdb
  15  propane.pdb
 107  total
~~~
{: .output}

단어 숫자만을 얻기 위해서 `-w`, 문자 숫자만을 얻기 위해서 `-c`을 사용할 수 있다.

파일 중에서 어느 파일이 가장 짧을까요? 
단지 6개의 파일이 있기 때문에 질문에 답하기는 쉬울 것이다. 
하지만 만약에 6000 파일이 있다면 어떨까요? 
해결에 이르는 첫번째 단계로 다음 명령을 실행한다:

~~~
$ wc -l *.pdb > lengths.txt
~~~
{: .language-bash}

`>` 기호는 쉘로 하여금 화면에 처리 결과를 뿌리는 대신에 파일로 **방향변경(redirect)**하게 한다. 
만약 파일이 존재하지 않으면 파일을 생성하고 파일이 존재하면 파일에 내용을 덮어쓰기 한다. 
조용하게 덮어쓰기 하기 때문에 자료가 유실될 수 있어서 주의가 요구된다.
(이것이 왜 화면에 출력결과가 없는 이유다. `wc`가 출력하는 모든 것은 `lengths.txt` 파일에 대신 들어간다.) 
`ls lengths.txt` 을 통해 파일이 존재하는 것을 확인한다:

~~~
$ ls lengths.txt
~~~
{: .language-bash}

~~~
lengths.txt
~~~
{: .output}

`cat lengths.txt`을 사용해서 화면으로 `lengths.txt`의 내용을 보낼 수 있다. 
`cat`은 "concatenate"를 줄인 것이고 하나씩 하나씩 파일의 내용을 출력한다. 
이번 사례에는 단지 파일이 하나만 있어서, `cat` 명령어는 단지 한 파일이 담고 있는 내용만 보여준다:

~~~
$ cat lengths.txt
~~~
{: .language-bash}

~~~
  20  cubane.pdb
  12  ethane.pdb
   9  methane.pdb
  30  octane.pdb
  21  pentane.pdb
  15  propane.pdb
 107  total
~~~
{: .output}

> ## Output Page by Page
>
> We'll continue to use `cat` in this lesson, for convenience and consistency,
> but it has the disadvantage that it always dumps the whole file onto your screen.
> More useful in practice is the command `less`,
> which you use with `$ less lengths.txt`.
> This displays a screenful of the file, and then stops.
> You can go forward one screenful by pressing the spacebar,
> or back one by pressing `b`.  Press `q` to quit.
{: .callout}

이제 `sort` 명령어를 사용해서 파일 내용을 정렬합니다. 

> ## What Does `sort -n` Do?
>
> If we run `sort` on a file containing the following lines:
>
> ~~~
> 10
> 2
> 19
> 22
> 6
> ~~~
> {: .source}
>
> the output is:
>
> ~~~
> 10
> 19
> 2
> 22
> 6
> ~~~
> {: .output}
>
> If we run `sort -n` on the same input, we get this instead:
>
> ~~~
> 2
> 6
> 10
> 19
> 22
> ~~~
> {: .output}
>
> Explain why `-n` has this effect.
>
> > ## Solution
> > The `-n` flag specifies a numeric sort, rather than alphabetical.
> {: .solution}
{: .challenge}

`-n` 플래그를 사용해서 알파벳 대신에 숫자 방식으로 정렬할 것을 지정할 수 있다.
이 명령어는 파일 자체를 변경하지 *않고* 대신에 정렬된 결과를 화면으로 보낸다:

~~~
$ sort -n lengths.txt
~~~
{: .language-bash}

~~~
  9  methane.pdb
 12  ethane.pdb
 15  propane.pdb
 20  cubane.pdb
 21  pentane.pdb
 30  octane.pdb
107  total
~~~
{: .output}

`> lengths.txt`을 사용해서 `wc` 실행결과를 `lengths.txt`에 넣었듯이, 
명령문 다음에 `> sorted-lengths.txt`을 넣음으로서, 
임시 파일이름인 `sorted-lengths.txt`에 정렬된 목록 정보를 담을 수 있다. 
이것을 실행한 다음에, 또 다른 `head` 명령어를 실행해서 `sorted-lengths.txt`에서 첫 몇 행을 뽑아낼 수 있다:

~~~
$ sort -n lengths.txt > sorted-lengths.txt
$ head -n 1 sorted-lengths.txt
~~~
{: .language-bash}

~~~
  9  methane.pdb
~~~
{: .output}

`head`에 `-n 1` 매개변수를 사용해서 파일의 첫번째 행만이 필요하다고 지정한다. 
`-n 20`은 처음 20개 행만을 지정한다. 
`sorted-lengths.txt`이 가장 작은 것에서부터 큰 것으로 정렬된 파일 길이 정보를 담고 있어서, 
`head`의 출력 결과는 가장 짧은 행을 가진 파일이 되어야만 된다.


> ## Redirecting to the same file
>
> It's a very bad idea to try redirecting
> the output of a command that operates on a file
> to the same file. For example:
>
> ~~~
> $ sort -n lengths.txt > lengths.txt
> ~~~
> {: .language-bash}
>
> Doing something like this may give you
> incorrect results and/or delete
> the contents of `lengths.txt`.
{: .callout}

> ## What Does `>>` Mean?
> We have seen the use of `>`, but there is a similar operator `>>` which works slightly differently.
> By using the `echo` command to print strings, test the commands below to reveal the difference
> between the two operators:
>
> ~~~
> $ echo hello > testfile01.txt
> ~~~
> {: .language-bash}
>
> and:
>
> ~~~
> $ echo hello >> testfile02.txt
> ~~~
> {: .language-bash}
>
> Hint: Try executing each command twice in a row and then examining the output files.
> > ## Solution
> > In the first example with `>`, the string "hello" is written to `testfile01.txt`,
> > but the file gets overwritten each time we run the command.
> >
> > We see from the second example that the `>>` operator also writes "hello" to a file
> > (in this case`testfile02.txt`),
> > but appends the string to the file if it already exists (i.e. when we run it for the second time).
> {: .solution}
{: .challenge}

> ## Appending Data
>
> We have already met the `head` command, which prints lines from the start of a file.
> `tail` is similar, but prints lines from the end of a file instead.
>
> Consider the file `data-shell/data/animals.txt`.
> After these commands, select the answer that
> corresponds to the file `animalsUpd.txt`:
>
> ~~~
> $ head -n 3 animals.txt > animalsUpd.txt
> $ tail -n 2 animals.txt >> animalsUpd.txt
> ~~~
> {: .language-bash}
>
> 1. The first three lines of `animals.txt`
> 2. The last two lines of `animals.txt`
> 3. The first three lines and the last two lines of `animals.txt`
> 4. The second and third lines of `animals.txt`
>
> > ## Solution
> > Option 3 is correct. 
> > For option 1 to be correct we would only run the `head` command.
> > For option 2 to be correct we would only run the `tail` command.
> > For option 4 to be correct we would have to pipe the output of `head` into `tail -2` by doing `head -3 animals.txt | tail -2 >> animalsUpd.txt`
> {: .solution}
{: .challenge}

이것이 혼란스럽다면, 좋은 친구네요: 
`wc`, `sort`, `head` 명령어 각각이 무엇을 수행하는지 이해해도, 
중간에 산출되는 파일에 무슨 일이 진행되고 있는지 따라가기는 쉽지 않다. 
`sort`와 `head`을 함께 실행해서 이해하기 훨씬 쉽게 만들 수 있다:

~~~
$ sort -n lengths.txt | head -n 1
~~~
{: .language-bash}

~~~
  9  methane.pdb
~~~
{: .output}

두 명령문 사이의 수직 막대를 **파이프(pipe)**라고 부른다. 
수직막대는 쉘에게 왼편 명령문의 출력결과를 오른쪽 명령문의 입력값으로 사용된다는 뜻을 전달한다. 
컴퓨터는 필요하면 임시 파일을 생성하거나, 
한 프로그램에서 주기억장치의 다른 프로그램으로 데이터를 복사하거나, 
혹은 완전히 다른 작업을 수행할 수도 있다; 
사용자는 알 필요도 없고 관심을 가질 이유도 없다.

어떤 것도 파이프를 연속적으로 사슬로 엮어 사용하는 것을 막을 수는 없다.
즉, 예를 들어 또 다른 파이프를 사용해서 `wc`의 출력결과를 `sort`에 바로 보내고 나서, 
다시 처리 결과를 `head`에 보낸다.
`wc` 출력결과를 `sort`로 보내는데 파이프를 사용했다:

~~~
$ wc -l *.pdb | sort -n
~~~
{: .language-bash}

~~~
   9 methane.pdb
  12 ethane.pdb
  15 propane.pdb
  20 cubane.pdb
  21 pentane.pdb
  30 octane.pdb
 107 total
~~~
{: .output}

또 다른 파이프를 사용해서 `wc`의 출력결과를 `sort`에 바로 보내고 나서, 
다시 처리 결과를 `head`로 보내게 되면 전체 파이프라인은 다음과 같이 된다:

~~~
$ wc -l *.pdb | sort -n | head -n 1
~~~
{: .language-bash}

~~~
   9  methane.pdb
~~~
{: .output}

이것이 정확하게 수학자가 *log(3x)* 같은 중첩함수를 사용하는 것과 같다. 
"*log(3x)*은 x에 3을 곱하고 로그를 취하는 것과 같다." 
이번 경우는, 
`*.pdb`의 행수를 세어서 정렬해서 첫부분만 계산하는 것이 된다.

> ## Piping Commands Together
>
> In our current directory, we want to find the 3 files which have the least number of
> lines. Which command listed below would work?
>
> 1. `wc -l * > sort -n > head -n 3`
> 2. `wc -l * | sort -n | head -n 1-3`
> 3. `wc -l * | head -n 3 | sort -n`
> 4. `wc -l * | sort -n | head -n 3`
>
> > ## Solution
> > Option 4 is the solution.
> > The pipe character `|` is used to feed the standard output from one process to
> > the standard input of another.
> > `>` is used to redirect standard output to a file.
> > Try it in the `data-shell/molecules` directory!
> {: .solution}
{: .challenge}

파이프를 생성할 때 뒤에서 실질적으로 일어나는 일은 다음과 같다. 
컴퓨터가 한 프로그램(어떤 프로그램도 동일)을 실행할 때 프로그램에 대한 소프트웨어와 현재 상태 정보를 담기 위해서 주기억장치 메모리에 **프로세스(process)**를 생성한다. 
모든 프로세스는 **표준 입력(standard input)**이라는 입력 채널을 가지고 있다. 
(여기서 이름이 너무 기억하기 좋아서 놀랄지도 모른다. 하지만 걱정하지 마세요. 대부분의 유닉스 프로그래머는 "stdin"이라고 부른다). 
또한 모든 프로세스는 **표준 출력(standard output)**(혹은 "stdout")이라고 불리는 기본디폴트 출력 채널도 있다.
이 채널이 일반적으로 오류 혹은 진단 메시지 용도로 사용되어서 
터미널로 오류 메시지를 받으면서도 그 와중에 프로그램 출력값이 또다른 프로그램에 파이프되어 
들어가는 것이 가능하게 한다.


쉘은 실질적으로 또다른 프로그램이다. 
정상적인 상황에서 사용자가 키보드로 무엇을 타이핑하는 모든 것은 표준 입력으로 쉘에 보내지고, 
표준 출력에서 만들어지는 무엇이든지 화면에 출력된다. 
쉘에게 프로그램을 실행하게 할때, 
새로운 프로게스를 생성하고, 임시로 키보드에 타이핑하는 무엇이든지 그 프로세스의 표준 입력으로 보내지고, 
프로세스는 표준 출력을 무엇이든 화면에 전송한다.

`wc -l *.pdb > lengths`을 실행할 때 여기서 일어나는 것을 설명하면 다음과 같다. 
`wc` 프로그램을 실행할 새로운 프로세스를 생성하라고 쉘이 컴퓨터에 지시한다.
파일이름을 인자로 제공했기 때문에 표준입력 대신 `wc`는 인자에서 입력값을 읽어온다. 
`>`을 사용해서 출력값을 파일로 방향변경 했기했기 때문에, 
쉘은 프로세스의 표준 출력결과를 파일에 연결한다.


`wc -l *.pdb | sort -n`을 실행한다면, 쉘은 프로세스 두개를 생성한다. 
(파이프 프로세스 각각에 대해서 하나씩) 그래서 `wc`과 `sort`은 동시에 실행된다. 
`wc`의 표준출력은 직접적으로 `sort`의 표준 입력으로 들어간다; 
`>`같은 방향변경이 없기 때문에 `sort`의 출력은 화면으로 나가게 된다. 
`wc -l *.pdb | sort -n | head -1`을 실행하면, 
파일에서 `wc`에서 `sort`로, `sort`에서 `head`을 통해 화면으로 나가게 되는 데이터 흐름을 가진 프로세스 3개가 있게 된다.


![방향변경과 파이프](../fig/redirects-and-pipes.png)

이 간단한 아이디어가 왜 유닉스가 그토록 성공적이었는지를 보여준다. 
다른 많은 작업을 수행하는 거대한 프로그램을 생성하는 대신에, 
유닉스 프로그래머는 각자가 한가지 작업만을 잘 수행하는 간단한 도구를 많이 생성하는데 집중하고, 
서로간에 유기적으로 잘 작동하게 만든다. 
이러한 프로그래밍 모델을 파이프와 필터(pipes and filters)라고 부른다; 
파이프는 이미 살펴봤고, **필터(filter)**는 `wc`, `sort`같은 프로그램으로 입력 스트림을 출력 스트림으로 변환하는 것이다. 
거의 모든 표준 유닉스 도구는 이런 방식으로 동작한다: 
별도로 언급되지 않는다면, 
표준 입력에서 읽고, 읽은 것을 가지고 무언가를 수행하고 표준출력에 쓴다.

중요한 점은 표준입력에서 텍스트 행을 읽고, 
표준 출력에 텍스트 행을 쓰는 임의 프로그램은 이런 방식으로 동작하는 모든 다른 프로그램과 조합될 수 있다는 것이다. 
여러분도 여러분이 작성한 프로그램을 이러한 방식으로 작성할 수 있어야 하고 *작성해야 한다*. 
그래서 여러분과 다른 사람들이 이러한 프로그램을 파이프에 넣어서 생태계 전체 힘을 배가할 수 있다.


> ## 입력 방향변경
>
> 프로그램의 출력 결과 방향변경을 위해서 `>`을 사용하는 것과 마찬가지로, `<`을 사용해서 입력을 되돌릴 수도 있다. 
> 즉, 표준입력 대신에 파일로부터 읽어 들일 수 있다. 
> 예를 들어, `wc ammonia.pdb` 와 같이 작성하는 대신에, `wc < ammonia.pdb` 작성할 수 있다.
> 첫째 사례는, `wc`는 무슨 파일을 여는지를 명령 라인의 매개변수에서 얻는다.
> 두번째 사례는, `wc`에 명령 라인 매개변수가 없다. 
> 그래서 표준 입력에서 읽지만, 쉘에게 `ammonia.pdb`의 내용을 `wc`에 표준 입력으로 보내라고 했다.
{: .callout}

> ## What Does `<` Mean?
>
> Change directory to `data-shell` (the top level of our downloaded example data).
>
> What is the difference between:
>
> ~~~
> $ wc -l notes.txt
> ~~~
> {: .language-bash}
>
> and:
>
> ~~~
> $ wc -l < notes.txt
> ~~~
> {: .language-bash}
>
> > ## Solution
> > `<` is used to redirect input to a command. 
> >
> > In both examples, the shell returns the number of lines from the input to
> > the `wc` command.
> > In the first example, the input is the file `notes.txt` and the file name is
> > given in the output from the `wc` command.
> > In the second example, the contents of the file `notes.txt` are redirected to
> > standard input.
> > It is as if we have entered the contents of the file by typing at the prompt.
> > Hence the file name is not given in the output - just the number of lines.
> > Try this for yourself:
> >
> > ```
> > $ wc -l
> > this
> > is
> > a test
> > Ctrl-D # This lets the shell know you have finished typing the input
> > ```
> > {: .language-bash}
> >
> > ```
> > 3
> > ```
> > {: .output}
> {: .solution}
{: .challenge}

> ## Why Does `uniq` Only Remove Adjacent Duplicates?
>
> The command `uniq` removes adjacent duplicated lines from its input.
> For example, the file `data-shell/data/salmon.txt` contains:
>
> ~~~
> coho
> coho
> steelhead
> coho
> steelhead
> steelhead
> ~~~
> {: .source}
>
> Running the command `uniq salmon.txt` from the `data-shell/data` directory produces:
>
> ~~~
> coho
> steelhead
> coho
> steelhead
> ~~~
> {: .output}
>
> Why do you think `uniq` only removes *adjacent* duplicated lines?
> (Hint: think about very large data sets.) What other command could
> you combine with it in a pipe to remove all duplicated lines?
>
> > ## Solution
> > ```
> > $ sort salmon.txt | uniq
> > ```
> > {: .language-bash}
> {: .solution}
{: .challenge}

> ## Pipe Reading Comprehension
>
> A file called `animals.txt` (in the `data-shell/data` folder) contains the following data:
>
> ~~~
> 2012-11-05,deer
> 2012-11-05,rabbit
> 2012-11-05,raccoon
> 2012-11-06,rabbit
> 2012-11-06,deer
> 2012-11-06,fox
> 2012-11-07,rabbit
> 2012-11-07,bear
> ~~~
> {: .source}
>
> What text passes through each of the pipes and the final redirect in the pipeline below?
>
> ~~~
> $ cat animals.txt | head -n 5 | tail -n 3 | sort -r > final.txt
> ~~~
> {: .language-bash}
> Hint: build the pipeline up one command at a time to test your understanding
> > ## Solution
> > The `head` command extracts the first 5 lines from `animals.txt`.
> > Then, the last 3 lines are extracted from the previous 5 by using the `tail` command.
> > With the `sort -r` command those 3 lines are sorted in reverse order and finally,
> > the output is redirected to a file `final.txt`.
> > The content of this file can be checked by executing `cat final.txt`.
> > The file should contain the following lines:
> > ```
> > 2012-11-06,rabbit
> > 2012-11-06,deer
> > 2012-11-05,raccoon
> > ```
> > {: .source}
> {: .solution}
{: .challenge}

> ## Pipe Construction
>
> For the file `animals.txt` from the previous exercise, the command:
>
> ~~~
> $ cut -d , -f 2 animals.txt
> ~~~
> {: .language-bash}
> 
> uses the `-d` flag to separate each line by comma, and the `-f` flag
> to print the second field in each line, to give the following output:
>
> ~~~
> deer
> rabbit
> raccoon
> rabbit
> deer
> fox
> rabbit
> bear
> ~~~
> {: .output}
>
> What other command(s) could be added to this in a pipeline to find
> out what animals the file contains (without any duplicates in their
> names)?
>
> > ## Solution
> > ```
> > $ cut -d , -f 2 animals.txt | sort | uniq
> > ```
> > {: .language-bash}
> {: .solution}
{: .challenge}

> ## Which Pipe?
>
> The file `animals.txt` contains 586 lines of data formatted as follows:
>
> ~~~
> 2012-11-05,deer
> 2012-11-05,rabbit
> 2012-11-05,raccoon
> 2012-11-06,rabbit
> ...
> ~~~
> {: .output}
>
> Assuming your current directory is `data-shell/data/`,
> what command would you use to produce a table that shows
> the total count of each type of animal in the file?
>
> 1.  `grep {deer, rabbit, raccoon, deer, fox, bear} animals.txt | wc -l`
> 2.  `sort animals.txt | uniq -c`
> 3.  `sort -t, -k2,2 animals.txt | uniq -c`
> 4.  `cut -d, -f 2 animals.txt | uniq -c`
> 5.  `cut -d, -f 2 animals.txt | sort | uniq -c`
> 6.  `cut -d, -f 2 animals.txt | sort | uniq -c | wc -l`
>
> > ## Solution
> > Option 5. is the correct answer.
> > If you have difficulty understanding why, try running the commands, or sub-sections of
> > the pipelines (make sure you are in the `data-shell/data` directory).
> {: .solution}
{: .challenge}

## Nelle의 파이프라인 Pipeline: 파일 확인하기

앞에서 설명한 것처럼 Nelle은 분석기를 통해 시료를 시험해서 1520개 파일을 `north-pacific-gyre/2012-07-03` 디렉토리에 생성했다. 
빠르게 건전성 확인하기 위해, 홈디렉토리에서 시작해서, 다음과 같이 타이핑한다:

Nelle has run her samples through the assay machines
and created 17 files in the `north-pacific-gyre/2012-07-03` directory described earlier.
As a quick sanity check, starting from her home directory, Nelle types:

~~~
$ cd north-pacific-gyre/2012-07-03
$ wc -l *.txt
~~~
{: .language-bash}

The output is 18 lines that look like this:

~~~
300 NENE01729A.txt
300 NENE01729B.txt
300 NENE01736A.txt
300 NENE01751A.txt
300 NENE01751B.txt
300 NENE01812A.txt
... ...
~~~
{: .output}

Now she types this:

~~~
$ wc -l *.txt | sort -n | head -n 5
~~~
{: .language-bash}

~~~
 240 NENE02018B.txt
 300 NENE01729A.txt
 300 NENE01729B.txt
 300 NENE01736A.txt
 300 NENE01751A.txt
~~~
{: .output}

Whoops: one of the files is 60 lines shorter than the others.
When she goes back and checks it,
she sees that she did that assay at 8:00 on a Monday morning --- someone
was probably in using the machine on the weekend,
and she forgot to reset it.
Before re-running that sample,
she checks to see if any files have too much data:

~~~
$ wc -l *.txt | sort -n | tail -n 5
~~~
{: .language-bash}

~~~
 300 NENE02040B.txt
 300 NENE02040Z.txt
 300 NENE02043A.txt
 300 NENE02043B.txt
5040 total
~~~
{: .output}

Those numbers look good --- but what's that 'Z' doing there in the third-to-last line?
All of her samples should be marked 'A' or 'B';
by convention,
her lab uses 'Z' to indicate samples with missing information.
To find others like it, she does this:

~~~
$ ls *Z.txt
~~~
{: .language-bash}

~~~
NENE01971Z.txt    NENE02040Z.txt
~~~
{: .output}

Sure enough,
when she checks the log on her laptop,
there's no depth recorded for either of those samples.
Since it's too late to get the information any other way,
she must exclude those two files from her analysis.
She could just delete them using `rm`,
but there are actually some analyses she might do later where depth doesn't matter,
so instead, she'll just be careful later on to select files using the wildcard expression `*[AB].txt`.
As always,
the `*` matches any number of characters;
the expression `[AB]` matches either an 'A' or a 'B',
so this matches all the valid data files she has.

> ## Wildcard Expressions
>
> Wildcard expressions can be very complex, but you can sometimes write
> them in ways that only use simple syntax, at the expense of being a bit
> more verbose.  
> Consider the directory `data-shell/north-pacific-gyre/2012-07-03` :
> the wildcard expression `*[AB].txt`
> matches all files ending in `A.txt` or `B.txt`. Imagine you forgot about
> this.
>
> 1.  Can you match the same set of files with basic wildcard expressions
>     that do not use the `[]` syntax? *Hint*: You may need more than one
>     expression.
>
> 2.  The expression that you found and the expression from the lesson match the
>     same set of files in this example. What is the small difference between the
>     outputs?
>
> 3.  Under what circumstances would your new expression produce an error message
>     where the original one would not?
>
> > ## Solution
> > 1. 
> >
> > 	```
> > 	$ ls *A.txt
> > 	$ ls *B.txt
> > 	```
> >	{: .language-bash}
> > 2. The output from the new commands is separated because there are two commands.
> > 3. When there are no files ending in `A.txt`, or there are no files ending in
> > `B.txt`.
> {: .solution}
{: .challenge}

> ## Removing Unneeded Files
>
> Suppose you want to delete your processed data files, and only keep
> your raw files and processing script to save storage.
> The raw files end in `.dat` and the processed files end in `.txt`.
> Which of the following would remove all the processed data files,
> and *only* the processed data files?
>
> 1. `rm ?.txt`
> 2. `rm *.txt`
> 3. `rm * .txt`
> 4. `rm *.*`
>
> > ## Solution
> > 1. This would remove `.txt` files with one-character names
> > 2. This is correct answer
> > 3. The shell would expand `*` to match everything in the current directory,
> > so the command would try to remove all matched files and an additional
> > file called `.txt`
> > 4. The shell would expand `*.*` to match all files with any extension,
> > so this command would delete all files
> {: .solution}
{: .challenge}
