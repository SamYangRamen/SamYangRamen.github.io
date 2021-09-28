# Office 버전 기록 기능



## 스프레드시트



### Office365 Excel



![image](https://user-images.githubusercontent.com/53200166/134471683-a77a6900-0be2-4ef1-8da6-e7112d86c9c6.png)

'김성보' 계정과 '백경훈' 계정으로 번갈아가면서 본인 이름에 해당하는 컬럼에 본인 순서에 숫자를 매기고 저장하는 행위를 반복하였다.

즉, 김성보 계정으로 1을 적고 저장, 백경훈 계정으로 2를 적고 저장, 김성보 계정으로 3을 적고 저장, ..., 백경훈 계정으로 8을 적고 저장하였다.

![image](https://user-images.githubusercontent.com/53200166/134472072-ab1fba78-31d6-409d-98c5-114ed8fe38dc.png)

그 다음, 상단의 저 버튼을 클릭하고 '버전 기록' 버튼을 클릭하면 아래와 같은 화면이 뜬다.

![image](https://user-images.githubusercontent.com/53200166/134472225-70bf75db-78d9-4dfc-9494-54e5b77385e2.png)

여기에서 오른쪽의 '버전 기록' 툴페인을 보면

![image](https://user-images.githubusercontent.com/53200166/134472374-3a22a643-773b-4b18-b2e5-b64744fddd5b.png)

위와 같이, 매 분마다 누가 어떻게 수정을 했는지 기록된다. 즉, 1분 이내에 여러 번의 저장을 수행하였다면, N초 전 등 매 초마다 툴페인에 나타나는 것이 아니라, 해당 분에 묶여서 기록이 된다. 

![image](https://user-images.githubusercontent.com/53200166/134471808-761e9fc7-f480-4a82-a85e-5027a41fa220.png)

만약 1분 이내에 (즉, 초 단위로) 5회의 저장을 수행하였을 경우, 우측 상단에 [확인중인버전]/[저장횟수] 가 뜨며, < > 화살표 버튼을 눌러 각 버전을 확인할 수 있다. 위 스크린샷은 1분 이내에 5회 저장된 내역 중에서 3번째 버전을 확인해 보았더니 '김성보' 계정이 '5'를 입력한 것이 확인되는 것을 볼 수 있다.

![image](https://user-images.githubusercontent.com/53200166/134835458-847ef5a3-1d01-4327-b61a-b199ebbeb9c5.png)

툴페인에서 과거 버전을 클릭하면 상단에 위와 같은 버튼이 나타나 복사본 저장 및 복원이 가능하다.



### Google SpreadSheet



![image](https://user-images.githubusercontent.com/53200166/134832844-4da6a5f0-f12f-4794-9c6c-c0859a64b9d8.png)

'띵똥' 계정과 '김성보' 계정으로 번갈아가면서 본인 이름에 해당하는 컬럼에 본인 순서에 숫자를 매기고 저장하는 행위를 반복하였다.

즉, 띵똥 계정으로 1을 적고 저장, 김성보 계정으로 2를 적고 저장, 띵똥 계정으로 3을 적고 저장, ..., 김성보 계정으로 20을 적고 저장하였다.

![image](https://user-images.githubusercontent.com/53200166/134833175-91493279-47a8-4abb-885e-493d1aa26501.png)

그 다음, '파일->버전 기록->버전 기록 보기' 버튼을 클릭하면 아래와 같은 화면이 나타난다.

![image](https://user-images.githubusercontent.com/53200166/134832878-67419192-c1a4-45c8-9412-351ed0135f5d.png)

여기에서 오른쪽의 '버전 기록' 툴페인을 보면, 위와 같이, 매 분마다 누가 어떻게 수정을 했는지 기록된다. 즉, 1분 이내에 여러 번의 저장을 수행하였다면, 해당 분에 묶여서 기록이 된다. 

![image](https://user-images.githubusercontent.com/53200166/134832912-8b391870-e0fa-46be-b7fe-7fc4e4446dca.png)

![image](https://user-images.githubusercontent.com/53200166/134832950-542a5c35-b5e7-48c8-a6f4-d7ee21a1ca8d.png)

단, MS365 Excel과는 달리 초 단위로 수정된 내역들은 초 단위로 기록이 되지 않는다. 즉, 아무리 초 단위로 수정되었다고 하더라도 그 내역들을 개별적으로 확인할 수가 없으며, 해당 분 내에 수정된 내역들은 한꺼번에 하나로 기록이 된다.

![image](https://user-images.githubusercontent.com/53200166/134835623-425c65c6-70fa-4c6c-b808-2fb008c619fb.png)

툴페인에서 과거 버전의 저 버튼을 클릭하면 드롭다운이 표시되어 복사본 저장 및 복원이 가능하다.



------



## 프레젠테이션



### Office365 PowerPoint



**결론**

- 편집 시작 시점부터 10분 간격으로 버전 기록을 수행함
- 각 10분 기록 내에서는 세부 편집 사항을 확인할 수 없음. 즉, 10분 간격으로 한 번에 하나씩 기록됨
- 다중 편집 시 개개인의 편집 내역이 기록되지 않음

- 다중 편집 시, 마지막으로 타 계정이 편집을 마친 시점부터 본인이 현재까지 편집한 시점 사이에서만 Undo, Redo를 수행할 수 있음
  - 즉, 과거의 본인 편집 내역을 Undo, Redo 하고 싶다면, 이후에 수행되어 있는 타 계정의 편집이 모두 Undo된 상태여야 함



#### 테스트 1

빈 슬라이드의 왼쪽 상단 끝부분에 텍스트 상자를 두고, 숫자 1부터 9까지 키보드 키를 꾹 눌러서 슬라이드 안을 꽉 채우는 행위를 약 10분 30초 동안 반복한 결과 ![image](https://user-images.githubusercontent.com/53200166/134852570-8af07eaa-2e07-4f19-840a-0fa6b920d834.png)

시작한지 약 10분쯤 되었을 때 버전 기록이 위와 같이 저장되었고

![image](https://user-images.githubusercontent.com/53200166/134852608-d4e6aa9b-234f-44d9-8886-d8732aaa39eb.png)

최종 버전은 위와 같이 저장되었다.



#### 테스트 2

테스트1 수행을 끝마치고 잠시 후부터 시작함. 이때, 뒤로가기 버튼을 누른 뒤 다시 이 파일을 선택하거나 새로고침을 하는 등의 편집 재시작 행위를 하지 않았음

15시 5분부터 15시 30분까지 1분마다 텍스트 상자에 시간을 기록하였음

![image](https://user-images.githubusercontent.com/53200166/134856429-e4334c01-bdc4-443e-9d42-c934a668a2da.png)

위 시점은 1부터 9까지 입력하는 행위를 포함하여 20분째가 되는 시점으로 예상됨

![image](https://user-images.githubusercontent.com/53200166/134856468-89c9db91-df6f-4fa7-b05e-9e72fe68644d.png)

이어서 10분 동안 위와 같이 저장되었음

![image](https://user-images.githubusercontent.com/53200166/134856514-0e6842d9-69df-4327-ae19-375f0b8d83af.png)

이어서 위와 같이 입력한 뒤에 새로고침을 수행하였음

![image](https://user-images.githubusercontent.com/53200166/134856554-c58e8a40-8721-4e9c-ad5b-665b1eff28f0.png)

그랬더니 위와 같이 새롭게 10분 간격으로 저장이 되었음



#### 테스트 3

게스트 참가자 계정으로 1 입력, 백경훈 계정으로 2 입력, ..., 백경훈 계정으로 8 입력을 수행하였음

![image](https://user-images.githubusercontent.com/53200166/134857917-96d53977-ea69-4d72-92de-aa161487c0d8.png)

위와 같이 나타나며, 각 계정별 세부 수정 내역은 확인할 수 없었음



#### 테스트 4

백경훈 계정으로 b를, 게스트 참가자 계정으로 s를 입력하는 행위를 아래와 같이 반복하였음

```
bbbbbbbbb
sssssssss
bbbbbbbbb
sssssssss
```

위 상태에서 백경훈 계정으로 Undo를 시도하였으나 수행되지 않았고, 게스트 참가자 계정으로 Undo를 시도하였더니

```
bbbbbbbbb
sssssssss
bbbbbbbbb
```

위와 같이 Undo가 성공적으로 수행되었음. 위 상태에서 게스트 참가자 계정으로 Undo를 시도하였으나 수행되지 않았고, 백경훈 계정으로 Undo를 시도하였더니

```
bbbbbbbbb
sssssssss
```

위와 같이 Undo가 성공적으로 수행되었음. 위 상태에서 게스트 참가자 계정으로 Redo를 시도하였으나 수행되지 않았고, 백경훈 계정으로 Redo를 시도하였더니

```
bbbbbbbbb
sssssssss
bbbbbbbbb
```

위와 같이 Redo가 성공적으로 수행되었음. 

따라서 본인만 편집한 시간대에 접근하였을 경우 Undo, Redo를 수행할 수 있다는 사실을 알게 되었음



### Google Presentation



#### 테스트 1

빈 슬라이드의 왼쪽 상단 끝부분에 텍스트 상자를 두고, 숫자 1부터 9, 알파벳 a부터 n까지 키보드 키를 꾹 눌러서 슬라이드 안을 꽉 채우는 행위를 20분 넘게 반복한 결과

![image](https://user-images.githubusercontent.com/53200166/135023220-b44ff6ba-92ec-46c0-a768-b0181d0e0b62.png)

위와 같이 5분에 한 번씩 버전 기록이 저장이 되는 것을 확인하였음



#### 테스트 2

약 26분 동안 1분마다 텍스트 상자에 시간을 기록하였음

![image](https://user-images.githubusercontent.com/53200166/135028822-4a77e6f0-105b-4aed-8b32-aed7da9c59ef.png)

위와 같이 1분에 한 번씩 버전 기록이 저장이 되는 것을 확인하였음



#### 테스트 3

a 계정과 b 계정으로 각각 a를 입력하고 b를 입력하는 행위를 아래와 같이 반복하였음

```
aaaa
bbbb
aaaa
bbbb
```

이때, 아래와 같이 수정 내역이 표시되는 것을 확인하였음. 단, 계정별로 각각의 수정 내역이 표시되지는 않았음.

![image](https://user-images.githubusercontent.com/53200166/135029304-c5deaef0-319c-4c2b-b774-c7b4cb7c4212.png)

한편, MS365에서는

```
aaaa
bbbb
aaaa
bbbb
```

위 상황에서는 b 계정의 편집이 마지막으로 수행되었으므로, a 계정으로 Undo를 시도하면 수행되지 않았음.

하지만 Google Presentation에서는

````
aaaa
bbbb
bbbb
````

b 계정의 편집 내역은 그대로 두고, a 계정의 편집 내역 중 일정 부분에 대하여 Undo를 수행하는 것을 확인하였음. 위 상태에서 a 계정에 대하여 Undo를 한 번 더 수행하면

````
bbbb
bbbb
````

위와 같이 수정됨.



------



## 워드프로세서



### Office365 Word



**결론**

- 편집 시작 시점부터 10분 간격으로 버전 기록을 수행함
- 각 10분 기록 내에서는 세부 편집 사항을 확인할 수 없음. 즉, 10분 간격으로 한 번에 하나씩 기록됨
- 다중 편집 시 개개인의 편집 내역이 기록되지 않음
- 다중 편집 시, 마지막으로 타 계정이 편집을 마친 시점부터 본인이 현재까지 편집한 시점 사이에서만 Undo, Redo를 수행할 수 있음
  - 즉, 과거의 본인 편집 내역을 Undo, Redo 하고 싶다면, 이후에 수행되어 있는 타 계정의 편집이 모두 Undo된 상태여야 함



#### 테스트 1

![image](https://user-images.githubusercontent.com/53200166/134861438-dd8298bc-872d-46d3-a72e-4d50d9e1316c.png)

약 21분 동안 1~0, a~k 키보드 키를 위와 같이 꾹 눌러서 입력하는 행위를 반복하였음

![image](https://user-images.githubusercontent.com/53200166/134861670-4d0a0e05-55e2-4207-8beb-a9dbf23da5db.png)

첫 10분 동안은 1부터 0까지 입력한 내용이 한꺼번에 저장됨

![image](https://user-images.githubusercontent.com/53200166/134861719-6ffd1100-ebf4-4dfa-bdf2-77047e556a99.png)

이후 10분 동안은 a부터 j까지 입력한 내용이 한꺼번에 저장됨

![image](https://user-images.githubusercontent.com/53200166/134861763-f2804a78-b15b-4b5b-b925-8586a1a2c8f2.png)

이후 남은 1분 동안 k를 입력한 내용이 저장됨



#### 테스트 2

16시 20분부터 16시 31분까지 게스트 참가자와 백경훈 계정으로 번갈아가면서 2분 마다 한 번씩 아래와 같이 입력한 결과 아래와 같이 10분 간격으로 저장이 된 것을 확인할 수 있으며, 각 계정별 세부 수정 내역은 확인할 수 없었음

![image](https://user-images.githubusercontent.com/53200166/134863966-86803099-800e-42fa-baf3-796606613448.png)



#### 테스트 3

![image](https://user-images.githubusercontent.com/53200166/134864432-9e0ada36-5e25-4467-81f4-eca5d4a3a608.png)

게스트 참가자 계정으로 위 상태에서 Undo를 계속해서 수행하였으나

![image](https://user-images.githubusercontent.com/53200166/134864462-cc1c4362-be8e-452b-957c-5665ef6cbd9a.png)

게스트 참가자 계정이 편집을 시작하고 끝마친 내용인 '성 16:31'만 Undo가 수행되었고, 타 계정인 백경훈 계정이 편집한 '백 16:30'의 이전 시점으로는 Undo를 수행할 수 없었음.

그러나, 백경훈 계정으로 위 상태에서 Undo를 수행하였더니 '백 16:30'이 사라졌음.

따라서 본인만 편집한 시간대에 접근하였을 경우 Undo, Redo를 수행할 수 있다는 사실을 알게 되었음



### Google Document



#### 테스트 1



17시 10분부터 17시 35분까지 1분마다 한 번씩 아래와 같이 입력하였음

```
21.09.27

17:10
17:11
17:12
17:13
17:14
17:15
17:16
17:17
17:18
17:19
17:20
17:21
17:22
17:23
17:24
17:25
17:26
17:27
17:28
17:29
17:30
17:31
17:32
17:33
17:34
17:35
```

그랬더니 아래와 같이 1분 간격으로 버전 기록이 수행된다는 것을 확인하였음

![image](https://user-images.githubusercontent.com/53200166/134874044-46a85355-57f0-49c6-9553-657af2a9a95b.png)



#### 테스트 2

약 17시 40분부터 17시 51분까지 11분 간 1~0 키보드 키를 꾹 눌러서 입력하는 행위를 반복하였더니, 테스트 1과 같이 분 단위로 저장되지 않았음. 입력하기 시작한 순간부터 입력을 끝마친 순간까지의 모든 내용이 하나의 버전으로 기록되었음

![image](https://user-images.githubusercontent.com/53200166/134876368-8fe485a1-887c-43b5-a4da-11e0317a9d6a.png)



#### 테스트 3

a 계정과 b 계정으로 각각 a를 입력하고 b를 입력하는 행위를 아래와 같이 반복하였음

```
aaaa
bbbb
aaaa
bbbb
```

이때, 아래와 같이 각 계정별 수정 내역이 표시되는 것을 확인하였음

![image](https://user-images.githubusercontent.com/53200166/134877766-8bcd9711-7fc8-4421-8bf5-4f8af2ad7a8c.png)

한편, MS365에서는

```
aaaa
bbbb
aaaa
bbbb
```

위 상황에서는 b 계정의 편집이 마지막으로 수행되었으므로, a 계정으로 Undo를 시도하면 수행되지 않았음.

하지만 Google Document에서는

````
aaaag
bbbb
bbbb
````

b 계정의 편집 내역은 그대로 두고, a 계정의 편집 내역 중 일정 부분에 대하여 Undo를 수행하는 것을 확인하였음. 위 상태에서 a 계정에 대하여 Undo를 한 번 더 수행하면

````
bbbb
bbbb
````

위와 같이 수정됨.

