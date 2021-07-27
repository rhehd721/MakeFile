# MakeFile
- 파일 간의 종속관계를 파악하여 Makefile( 기술파일 )에 적힌 대로 컴파일러에 명령하여 SHELL 명령이 순차적으로 실행될 수 있게 합니다.
- linux상에서 반복 적으로 발생하는 컴파일을 쉽게하기위해서 사용하는 make 프로그램의 설정 파일이다.
- Makefile을 통하여 library 및 컴파일 환경을 관리 할수 있다.

## MakeFile의 구조

- 파일명 : Makefile
- 기본 구조는 다음과 같이 매크로 정의, 룰 , 명령어로 되어있다.
```Makefile
CC = gcc    # 메크로 정의

target1 : dependency1 dependency2   # 룰 1
    command1    # 명령
    command2    # 명령

target2 : dependency3 dependency4 dependency5   # 룰 2
    command3    # 명령
    command4    # 명령

clean :
    rm *.o  # 더미타켓(파일을 생성하지 않는 개념적인 타겟)
```

* 만약 make를 실행할 때 인자를 지정하지 않으면, 기본값으로 Makefile의 타겟 중 가장 위에 있는 타겟의 규칙을 수행합니다.

## Makefile의 매크로 정의란?

- Makefile에서 미리 정의 되어있는 환경 변수라고 생각하면된다.
- 기본 구조에서 CC = gcc 에서 CC가 매크로이다.

## Makefile에서의 매크로의 종류

- makefile에서 미리 정의된 매크로 확인 방법
    - #make -p
- 주요 매크로

| 매크로 명 |용도|
|------|---|
|DEFS |Define 추가 할때 사용한다|
|CFLAGS|gcc의 옵션을 추가 할때 사용한다.|
|CC|컴파일러는 변경할때 사용한다.|
|CPPFLAGS|c++ 의 보션을|
|CXX |c++ 의 컴파일러는 선택|
|LDFLAGS| ld 의 옵션 세팅|
|LD = ld| - |

## Makefile 내부 매크로
- $@ : 현재 타겟의 이름
- $^ : 현재 타겟의 종속 항목 리스트
- $(word n,text) : text의 n번째 단어를 리턴
    - n의 합법적인 값은 1부터 시작한다. n가 text에 있는 단어들 개수보다 더 크다면 그 값은 빈 것이 된다. 
```Makefile
$(word 2, foo bar baz)
# 'bar'를 리턴한다.
```
- $(shell cmd) : cmd에 해당하는 shell 명령어를 가져온다
```Makefile
$(shell pwd))
# 현재위치 리턴
```
- $(subst from,to,text) : text 텍스트에 대해서 텍스트의 대치를 수행한다: 
    - 이것 안에서 from이 나오면 to로 대치된다. 그 결과가 대입된다.
```makefile
$(subst ame,AmE, game jame)
# gAmE jAmE
```
- $(strip string) : string의 앞뒤에 있는 공백문자들을 제거하고 내부에 있는 하나 이상의 공백문자들을 단일 스페이스로 교체한다.
```makefile
$(strip a b c )
#`a b c'
``` 
- $(wildcard pattern) : 특정 파일 명칭 패턴을 통해 원하는 종류의 파일들을 불러오는데 사용
```makefile
$(wildcard ../target/*.c)
# ../target/main.c ../target/source.c
``` 
- $(patsubst pattern,replacement,text) : subst와 다른점은 from, to와 같이 특정 문자열을 대체하는 것이 아닌, 특정 패턴을 대체한다는 것과 공백 문자로 각 단어를 구분한다는 것
```makefile
$(patsubst %.c,%.o,x.c.c bar.c)
# x.c.o bar.o
``` 
- $(notdir names...) : names에 입력된 파일명들 중에서 경로라고 판단되는 부분은 제거
```makefile
$(notdir forge/target.c name.c pms/fiya.mp3)
# target.c name.c fiya.mp3
``` 

## EX
- hello word Make 파일
```Makefile
cc = gcc

TARGET = hello

$(TARGET) : hello.o
	$(cc) -o $(TARGET) hello.o

hello.o : hello.c
	$(cc) -c -o hello.o hello.c

clean :
	rm -rf $(TARGET)
```
- hello.c 파일
```c
#include<stdio.h>

int main(int argc,  char** argv) {
	printf("Hellow Makefile\n");
	return 0;
}
```

## PHONY (Phony Targets)
- 더미타켓과 동일한 이름의 파일로 인해 명령어가 실행되지 않는것을 방지하기 위한 것
```Makefile
.PHONY : clean 

clean: 
    rm *.o temp
```

## = AND :=
- = (정의에 다른 변수가 포함되어 있다면 해당 변수가 정의되기 될 때 까지 변수의 값이 정해지지 않는다)
```Makefile
B = $(A)
C = $(B)
A = a
# C의 값은 a이다
```
- := (해당 시점에의 변수의 값만 확인 합니다)
```Makefile
B := $(A)
A = a
# B의 값은 비어있다. 
```

## include
```Makefile
include make.common

$(TARGET) : hello.o
	$(cc) -o $(TARGET) hello.o
```

## wildcard
- 현 디렉토리에서 주어진 패턴에 일치하는 파일을 찾아 리스트업 한다.
```Makefile
$(wildcard pattern)
```
- Ex
```Makefile
# 현 디렉토리의 모든 .c 파일 리스트
$(wildcard *.c)
# 모든 .o 파일 리스트
OBJECTS := $(wildcard *.o)
# .c 리스트에서 .c를 .o로 치환
OBJECTS := $(patsubst %.c,%.o,$(wildcard *.c))
```

## export or unexport
- 현재 makefile에서 선언된 변수를 다른 makefile에서도 사용가능하도록 하고자할 때
- export : 모든 Makefile 참조 가능하게
```Makefile
export IPCUDS_LIB
IPCUDSDIR	=../../lg_bridge/ipcuds
IPCUDSLIB	=$(IPCUDSDIR)/libipcuds.so
CFLAGS	      += -I$(IPCUDSDIR)
COMMON_CFLAGS += -I$(IPCUDSDIR)
IPCUDS_LIB     = libipcuds.so
```

## .SUFFIXES
- 적용 전
```makefile
main.o : main.c
    $(CC) $(CFLAGS) -c $^
read.o : read.c
    $(CC) $(CFLAGS) -c $^
write.o : write.c
    $(CC) $(CFLAGS) -c $^
```
- 적용 후
```makefile
.SUFFIXES: .c .o

.c .o :
    $(CC) $(CFLAGS) -c $<
```

# ERROR

## *** missing separator. Stop.
- tab 대신 space가 존재하여 발생하는 error
