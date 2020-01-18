---
layout: post
title: "[python] 커맨드 패턴 적용- 닌텐도/ 파일 생성,제거"
date: 2019-12-06
excerpt: ""
tags: [Command, Command Pattern, 커맨드 패턴, 커맨드 패턴 적용, 매크로 커맨드 패턴, design pattern]
comments: true
design_patterns: true
---
# 👀 목차 

- 닌텐도에 커맨드 패턴을 적용하자.
- 파일 생성, 제거에 매크로 커맨드 패턴을 적용하자. 

# 1. 닌텐도에 적용해 보자.

> 닌텐도 스위치에 커맨드 패턴을 적용해보자. 닌텐도 스위치 개발자라고 생각하자.

## 1-1. 고려 사항

    1. 게임 별로 닌텐도 버튼 별 동작을 적용시켜야 한다. 
    
    2. 사용자의 버튼을 기록하여 갑자기 닌텐도가 꺼져도 원복이 가능해야한다. 
    
    3. 여기서는 간단하게 하기 위해 8가지 버튼만 존재한다고 가정 (왼쪽 4개 + 오른쪽 4개 버튼) 
    > 테스트는 간단히 4개만 셋팅하겠습니다. 

![](/assets/img/design_pattern/command_ex/Untitled.png)

[https://brunch.co.kr/@wikitree/2809](https://brunch.co.kr/@wikitree/2809) 참고 이미지 

## 1-2. 커맨드 기능 정리

- **리시버**: 젤다-더 와일드, 젤다-꿈꾸는 섬, 동물의 숲, 마리오 카트 등이 존재
- **클라이언트**: 젤다1 개발자, 젤다2 개발자, 동물의 숲 개발자 / 사용자
- **커맨드**: 동물의 숲 의 경우,  1) 위로 가기 2) 오른쪽으로 가기 3) 왼쪽으로 가기 4) 뒤로 가기 5) 말걸기 6) 뛰기 등 존재
- **인보커**: 닌텐도 스위치

## 1-3. UML

![python/Untitled%201.png](/assets/img/design_pattern/command_ex/Untitled%201.png)

## 1-4. 소스 코드

### 1) 커맨드-  오른쪽으로 가기, 왼쪽으로 가기, 앞으로 가기, 뒤로 가기 커맨드 정의
{% highlight python %}
    import abc
    
    class Command(object):
        """
        커맨드 인터페이스
        """
    
        __metaclass__ = abc.ABCMeta
    
        @abc.abstractclassmethod
        def execute(self):
            """
            커맨드를 실행하기 위한 추상 메소드
            :return:
            """
            pass
    
        @abc.abstractclassmethod
        def undo(self):
            """
            커맨드를 실행하기 취소하기 위한 메소드
            :return:
            """
            pass
    
    
    class NoCommand(Command):
        """
        아무 작업도 안하는 커맨드
        """
        def __init__(self):
            pass
    
        def execute(self):
            pass
    
        def undo(self):
            pass
    
    class GoBackCommand(Command):
        """
        뒤로 가는 커맨드
        """
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            """
            뒤로 가기
            :return:
            """
            self.receiver.back()
    
        def undo(self):
            """
            앞으로 가기
            :return:
            """
            self.receiver.forward()
    
    
    
    class GoForwardCommand(Command):
        """
        앞으로 나가는 커맨드
        """
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            """
            리시버로 앞으로 가기  호출
            :return:
            """
            self.receiver.forward()
    
        def undo(self):
            """
            리시버로 앞으로 가기 취소
            :return:
            """
            self.receiver.back()
    
    
    class GoLeftCommand(Command):
        """
        앞으로 나가는 커맨드
        """
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            """
            리시버로 왼쪽으로 가기  호출
            :return:
            """
            self.receiver.left()
    
        def undo(self):
            """
            리시버로 왼쪽으로 가기 취소
            :return:
            """
            self.receiver.right()
    
    
    class GoRightCommand(Command):
        """
        오른쪽으로 가는 커맨드
        """
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            """
            리시버로 오른쪽 가기  호출
            :return:
            """
            self.receiver.right()
    
        def undo(self):
            """
            리시버로 오른쪽 가기 취소
            :return:
            """
            self.receiver.left()
{% endhighlight %}
### 2) 리시버 - 실제 작업자

> 여기서는 동물의 숲 게임만 정의함
{% highlight python %}
    class AnimalForest(object):
        """
        리시버: 실제 동물의 숲 행동 메소드 저장소
        """
        def forward(self):
            """
            앞으로 나가기
            :return:
            """
            print("앞으로 나간다")
    
        def back(self):
            """
            뒤로 가기
            :return:
            """
            print("뒤로 간다.")
    
        def left(self):
            """
            오른쪽으로 이동
            :return:
            """
            print("왼쪽으로 간다.")
    
        def right(self):
            """
            오른쪽으로 이동
            :return:
            """
            print("오른쪽으로 간다.")
{% endhighlight %}
### 3) 인보커 - 닌텐도 머신
{% highlight python %}
    class NitendoMachine(object):
        """
        인보커 - 닌텐도 머신 
        """
        def __init__(self):
            self.array_command = [NoCommand()] *8 # 커맨드 리스트
            self.history =[]         # 명령 히스토리 저장
    
        def set_command(self, slot =int, command = Command):
            """
            버튼 별 커맨드 적용
            :param slot:
            :param command:
            :return:
            """
            self.array_command[slot] = command
    
        def button_was_pushed(self, slot):
            self.array_command[slot].execute()
            self.history.append(self.array_command[slot])
    
        def undo_all(self):
            print('Undo all')
            for command in reversed(self.history):
                command.undo()
            print('Undo all finished.')
    
        def show_history(self):
            print("*******history******")
            for history in self.history:
                print("history:", history)
{% endhighlight %}
### 4) 클라이언트
{% highlight python %}
    if __name__ == '__main__':
        """
        클라이언트 
        """
        # 1. 리시버 정의
        animal_game = AnimalForest()
    
        # 2. 커맨드 정의
        go_forward_command = GoForwardCommand(receiver=animal_game)
        go_back_command = GoBackCommand(receiver=animal_game)
        go_left_command = GoLeftCommand(receiver=animal_game)
        go_right_command = GoRightCommand(receiver=animal_game)
    
    
    
        # 3. 인보커 정의 = 리모컨 정의 및 커맨드와 연결
        remote_nintendo = NitendoMachine()
        remote_nintendo.set_command(0, go_forward_command)
        remote_nintendo.set_command(1, go_back_command)
        remote_nintendo.set_command(2, go_left_command)
        remote_nintendo.set_command(3, go_right_command)
    
        # 4. 실제 게임 진행
        remote_nintendo.button_was_pushed(1)
        remote_nintendo.button_was_pushed(0)
        remote_nintendo.button_was_pushed(2)
        remote_nintendo.button_was_pushed(3)
    
        # 5. history 출력
        remote_nintendo.show_history()
    
        # 6. undo all
        remote_nintendo.undo_all()
{% endhighlight %}
---

# 2. 파일 생성 및 삭제를 커맨드에 적용하자.

> 파일 생성 및 제거하는 작업을 매크로 패턴에 적용해 보자. 추가적으로 UNDO 작업도 넣을 것이다.

## 2-1. 기능 정리

- **커맨드**:  ls 커맨드 , touch 커맨드, rm 커맨드 존재 > exexute(), undo() 정의
- **리시버**: ls, touch, rm  실제 작업자
- **인보커**: 커맨드 세팅과 커맨드의 execute() 호출하여 실제 작업, undo() 호출하여 작업 취소 기능
- **클라이언트**: 파일 생성, 제거 작업을 진행

 

## 2-2. UML

![python/1.png](/assets/img/design_pattern/command_ex/1.png)

## 2-3.소스 코드

### 1) 커맨드 클래스 정의
{% highlight python %}
    import abc
    class Command(object):
        """
        커맨드 인터페이스
        """
        __metaclass__ = abc.ABCMeta
    
        @abc.abstractclassmethod
        def execute(self):
            """
            커맨드 실행 메소드
            :return:
            """
            pass
    
        @abc.abstractclassmethod
        def undo(self):
            """
            커맨드 실행 취소 메소드
            :return:
            """
            pass
    
    
    class LsCommand(Command):
        """
        유닉스 명령어 ls 흉내내는 실제 커맨드
        """
    
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            """
            리시버의 행동 호출
            :return:
            """
            self.receiver.show_current_dir()
    
        def undo(self):
            """
            ls 커맨드는 취소 못함
            :return:
            """
            pass
    
    
    class RmCommand(Command):
        """유닉스 명령어 rm 실제 커맨드"""
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            self.receiver.delete_file()
    
        def undo(self):
            self.receiver.undo()
    
    
    class TouchCommand(Command):
        """ 유닉스 명령어 touch 실제 커맨드"""
    
        def __init__(self, receiver):
            self.receiver = receiver
    
        def execute(self):
            self.receiver.create_file()
    
        def undo(self):
            self.receiver.delete_file()
{% endhighlight %}
### 2) 리시버 - 실제 작업자
{% highlight python %}
    import os
    
    class LsReceiver(object):
        def show_current_dir(self):
            """
            실제 ls 동작 메소드
            :return:
            """
            cur_dir = './'
    
            filenames = []
            for filename in os.listdir(cur_dir):
                if os.path.isfile(os.path.join(cur_dir, filename)):
                    filenames.append(filename)
    
            print('Content of dir: ', ' '.join(filenames))
    
    
    class RmReceiver(object):
    
        def __init__(self, filename):
            self.filename = filename
            self.backup_name = None
    
        def delete_file(self):
            """파일 삭제를 실제하는 메소드"""
            self.backup_name = '.' + self.filename
            os.rename(self.filename, self.backup_name)
    
        def undo(self):
            """파일 삭제 복구 매소드"""
            original_name = self.backup_name[1:]
            os.rename(self.backup_name, original_name)
            self.backup_name = None
    
    
    
    class TouchReceiver(object):
    
        def __init__(self, filename):
            self.filename = filename
    
        def create_file(self):
            """실제 파일을 생성하는 메소드"""
            with open(self.filename, 'a'):
                os.utime(self.filename, None)
    
        def delete_file(self):
            """파일 생성을 취소하는 메소드"""
            os.remove(self.filename)
{% endhighlight %}
### 3) Invoker
{% highlight python %}
    class Invoker(object):
        def __init__(self, create_file_commands, delete_file_commands):
            self.create_file_commands = create_file_commands
            self.delete_file_commands = delete_file_commands
            self.history = []
    
        def create_file(self):
            print('파일 생성중')
            for command in self.create_file_commands:
                command.execute()
                self.history.append(command)
            print('File 생성됨\n')
    
        def delete_file(self):
            print ('파일 제거중')
            for command in self.delete_file_commands:
                command.execute()
                self.history.append(command)
            print('File 제거됨.\n')
    
        def undo_all(self):
            print('Undo all')
            for command in reversed(self.history):
                command.undo()
            print('Undo all finished.')
    
        def show_history(self):
            """ 히스토리 print """
            for his in self.history:
                print("history", his)
{% endhighlight %}
### 4) client
{% highlight python %}
    if __name__ == '__main__':
        # Client
    
        # 1. LS 관련
        # 1-1. 리시버 정의 : 실제 작업자
        ls_receiver = LsReceiver()
        # 1- 2. 커맨드 정의: 커맨드에 리시버 전달
        ls_command = LsCommand(ls_receiver)
    
        # 2. Touch 관련
        # 2-1. 리시버 정의
        touch_receiver = TouchReceiver('test.text')
        # 2-2. 커맨드 정의
        touch_command = TouchCommand(touch_receiver)
    
        # 3. RM 관련
        # 3-1. 리시버 정의
        rm_receiver = RmReceiver('test.text')
        # 3-2. 커맨드 정의
        rm_command = RmCommand(rm_receiver)
    
        # 매크로 커맨드 정의: 여러 작업을 한번에 진행
        create_file_commands = [ls_command, touch_command, ls_command]
        delete_file_commands = [ls_command, rm_command, ls_command]
    
        # 인보커 정의: 인보커에게 커맨드 전달
        invoker = Invoker(create_file_commands, delete_file_commands)
        # 인보커가 리시버에게 작업해달라고 요청
        invoker.create_file()
        invoker.delete_file()
        invoker.show_history()
        invoker.undo_all()
{% endhighlight %}
---

# 소스 코드

- [https://github.com/JUNGEEYOU/Command-Pattern-example](https://github.com/JUNGEEYOU/Command-Pattern-example)

# 참고

- python design patterns 책
