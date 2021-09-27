# 스프레드시트 버전 기록 기능



## Office365 Excel



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



## Google SpreadSheet



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

