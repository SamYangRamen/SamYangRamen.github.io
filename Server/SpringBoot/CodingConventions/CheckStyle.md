# Intellij CheckStyle



## 코딩 컨벤션이란?

읽기 쉽고, 관리하기 쉬운 코드를 작성하기 위한 코딩 스타일 규약.

여러 개발자가 협업해야 하는 상황에서 이 규약이 있다면, 유지보수 및 가독성이 좋아져 협업이 쉬워진다.

https://055055.tistory.com/97

------



## JAVA 코딩 컨벤션 도구: CheckStyle vs SonarLint

빠르게 자바 코딩 컨벤션을 정해야 하는 상황에서, SonarLint는 정보가 얼마 없었고 CheckStyle은 관련 자료가 많았으며 보편적으로 많이 사용하는 도구이기 때문에 CheckStyle을 적용하기로 결정했다.

------



## Intellij CheckStyle 적용 과정



#### 1. CheckStyle Plugin 설치

```File -> Settings -> Plugins -> Marketplace -> CheckStyle-IDEA```

![image](https://user-images.githubusercontent.com/53200166/130609226-350586fa-27ad-40e3-a7db-03abd4af846e.png)



#### 2. CheckStyle Google 양식 다운로드

google_checks.xml:

https://github.com/checkstyle/checkstyle/blob/master/src/main/resources/google_checks.xml

or

https://raw.githubusercontent.com/checkstyle/checkstyle/master/src/main/resources/google_checks.xml



#### 3. 다운받은 google_checks.xml을 플러그인에 적용

``` File -> Settings -> Tools -> Checkstyle -> Configuration File의 + 버튼 -> 이름 작성 및 google_checks.xml 파일경로 설정 -> APPLY  ```

![image](https://user-images.githubusercontent.com/53200166/130609415-6c80ed5f-4733-45ee-a87c-5a4b6e6f639c.png)

![image](https://user-images.githubusercontent.com/53200166/130609462-b3c9e0df-bfd2-4eef-b5d2-de89a5475db9.png)

![image](https://user-images.githubusercontent.com/53200166/130609485-d9d63d76-2141-4e9e-8b73-169787ff5c24.png)

![image](https://user-images.githubusercontent.com/53200166/130609507-a7521409-e0c0-456a-bbf3-4ed0fad41bee.png)



#### 4. 다운받은 google_checks.xml을 Code Style에 적용

```File -> Settings -> Editor -> Code Style -> Java -> Scheme의 톱니바퀴 버튼 -> Import Scheme -> CheckStyle Configuration -> google_checks.xml 파일 선택 -> APPLY```

※ CheckStyle은 코딩 컨벤션을 확인시켜주는 도구라면, Intellij 내부의 Code Style Setting은 코딩 컨벤션에 맞게 코드들을 정리정돈해주는 도구라고 생각하면 된다.

​	정리정돈하는 단축키는 ```Ctrl + Alt + L```

![image](https://user-images.githubusercontent.com/53200166/130609907-71cc6e3b-559f-484a-a3a7-441304b155ad.png)

![image](https://user-images.githubusercontent.com/53200166/130609709-a8bfa5ac-9002-4db1-b68f-72a3e9447297.png)



#### 5. google_checks.xml의 내용 중 필요에 맞게 수정



------





## CheckStyle 수행 자동화



### git의 pre-commit hook

pre-commit : 코드를 git에 commit할 때마다 자동으로 특정 작업을 실행하게 도와줌

prepare-commit-msg : pre-commit 실행 이후, 원하는 형식의 커밋 메시지를 자동으로 입력해주기 위해 호출함

https://www.daleseo.com/pre-commit/

https://nochoco-lee.tistory.com/196

https://www.huskyhoochu.com/npm-husky-the-git-hook-manager/

https://library.gabia.com/contents/8492/



### git commit/push 시 CheckStyle 수행 자동화 과정



#### 1. CheckStyle .jar File 다운로드

https://github.com/checkstyle/checkstyle/releases/download/checkstyle-8.45.1/checkstyle-8.45.1-all.jar



#### 2. 다운받은 .jar 파일을 수정

다운받은 .jar 파일을 압축풀기한 다음, 안에 들어있던 google_checks.xml  파일을 내가 수정한 google_checks.xml 파일로 대체하여 .jar 확장자로 재압축한다.



#### 3. .jar 파일을 프로젝트 내부로 이동

어디든지 상관없음. 나는 /[프로젝트경로]/config/checkstyle/checkstyle-8.45.1-custom.jar 가 되도록 이동시킴



#### 4. 명령어 수행으로 CheckStyle이 정상적으로 수행되는지 확인

```java -jar /[.jar 파일이 존재하는 경로]/[.jar 파일명].jar -c /google_checks.xml ../*```

```java -jar ../config/checkstyle/checkstyle-8.45.1-custom.jar -c /google_checks.xml ../*```



#### 5. build.gradle 파일 안에 아래와 같이 내용 작성

```js
plugins {
	// 다른 플러그인들
	id 'checkstyle'
}

checkstyle {
	toolVersion = '8.45' // checkStyle 버전은 File -> Settings -> Tools -> Checkstyle에서 확인 가능
	configFile = file("${rootDir}/config/checkstyle/google_checks.xml")
}

checkstyleMain() {

}
```



#### 6. ```./gradlew build``` 명령어 수행

정상적으로 빌드되는지 확인



#### 7. 프로젝트의 ```.git/hooks/pre-commit``` 파일의 내용을 아래와 같이 수정

정상 build 여부 (정상적으로 실행되는가) 및 정상 style check 여부 (checkStyle 코딩 컨벤션에 맞게 작성이 되었는가) 모두 확인하여, 둘 다 만족하면 commit이 되는 쉘 스크립트를 아래와 같이 작성하였음

```shell
#!/usr/bin/bash

buildResult=`./gradlew test`

if [[ "$buildResult" =~ ":compileJava FAILED" ]]; then
	echo "Build Failed."
	echo "commit aborted."
	exit -1
else
	echo "Build Success."
fi

checkStyleResult=$(java -jar "`pwd`/config/checkstyle/checkstyle-8.45.1-custom.jar" -c /google_checks.xml `pwd`/src/)

if [[ "$checkStyleResult" =~ "[WARN]" ]]; then
	echo "CheckStyle Failed."
	echo "commit aborted."
	exit -1
else
	echo "CheckStyle Success."
	
fi

echo "commit Success."
```



#### 8. ```git commit```으로 commit이 되는지 확인