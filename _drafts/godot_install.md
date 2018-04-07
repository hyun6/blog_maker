# [godot] Windows MSVC 고도 엔진 컴파일
## 머리말
현재 C++ 응용/임베디드 개발을 하고 있지만
게임 엔진의 소스 코드에 관심이 있고
게임 개발에도 관심이 있어서
오픈소스 게임엔진인 고도를 살펴보기 위해 빌드 해 봄

## 설치
* `visual studio` - 2017 community edition
  - https://www.visualstudio.com/ko/vs/
* `python` - 3.6.5
  - 설치 : https://www.python.org/downloads/
  - PATH 환경 변수에 추가
* `Pywin32` 확장 for 병렬 컴파일
    <pre>pip install pywin32</pre>
* `SCons` 빌드 시스템 : https://scons.org/pages/download.html
    <pre>pip install --egg SCons</pre>
* `Godot` source
  - https://github.com/godotengine/godot
  - 클론 받은 폴더에서 cmd로 scons, python  --version 옵션을 통해 동작 확인

* `MSVC` 솔루션 생성
~~~
  "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvars64.bat" amd64 && scons p=windows vsproj=yes
~~~
* build.bat 작성
  - VS 컴파일러 환경 변수 배치 파일 + scons 빌드 설정  
~~~
"C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvars64.bat" amd64 && scons -j8 platform=windows
~~~

# 참고 
공식 홈페이지 - Compiling for Windows
http://docs.godotengine.org/en/3.0/development/compiling/compiling_for_windows.html

## 그 외 참고할만한 사이트
개발 기록 : http://aigoia.tistory.com/category/%EA%B3%A0%EB%8B%B7%20%EC%97%94%EC%A7%84  
튜토리얼 번역 : http://hugagage.tistory.com/category/%EA%B0%9C%EB%B0%9C/%EA%B3%A0%EB%8F%84%20%EC%97%94%EC%A7%84.Godot%20engine
