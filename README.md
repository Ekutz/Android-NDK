# Android NDK

안드로이드 어플리케이션을 개발하다보면 자바에서 구현할 수는 있지만 퍼포먼스 적인 측면에서 볼때 느린 기능들이 있다. 영상처리나 게임, 신호, 시뮬레이션 등이 그 예시이다. 이러한 불편함을 최소화하기 위해 cpu에서 직접 코드를 돌릴 수 있는 C/C++ 언어를 자바에서도 사용하기 위해 만들어진 툴킷이 바로 NDK이다.

![definition of NDK](https://github.com/Ekutz/Android-NDK/blob/master/definition%20of%20ndk.png?raw=true)

NDK를 사용하여 얻을 수 있는 장점은 크게 2가지가 있다.

**1.게임이나 시뮬레이션을 돌리기 위해 성능을 최대한 짜낼 수 있다.**

**2.기존에 있던 C/C++ 라이브러리를 재사용할 수 있다.**

이러한 이유로 인해 openCV와 같은 영상처리나 물리엔진을 사용할 때 많이 이용된다.

## 설치

### 1. NDK 설치

![install NDK](https://github.com/Ekutz/Android-NDK/blob/master/install%20NDK.png?raw=true)

Tools -> SDK 매니져로 이동하여 NDK, CMake를 모두 설치해준다.

### 2. JNI 폴더 생성

![move to project](https://github.com/Ekutz/Android-NDK/blob/master/move%20to%20project.png?raw=true)

프로젝트 형식으로 트리 구조를 변경 한 뒤

![create jni folder](https://github.com/Ekutz/Android-NDK/blob/master/create%20jni%20folder.png?raw=true)

app/main/ 위치에 jni 폴더를 생성해 준다. 위치 변경 다이얼로그가 뜨지만 이후 동작들을 고려할 때 그냥 무시하고 끝낸다.

### 3. javah, ndk-build 설정

C/C++을 익힌 사람들은 이미 알고 있는 내용이지만 .c/.cpp 파일에는 .h라는 확장자의 헤더파일이 공존한다. 헤더를 직접 만들 수 있으면 좋겠지만 자바 환경에 맞추어 이를 만드는게 까다롭기 때문에 javah를 이용하여 헤더를 자동 생성한다.

![set external tools](https://github.com/Ekutz/Android-NDK/blob/master/set%20external%20tools.png?raw=true)

![set javah](https://github.com/Ekutz/Android-NDK/blob/master/set%20javah.png?raw=true)

![set ndk-build](https://github.com/Ekutz/Android-NDK/blob/master/set%20ndk-build.png?raw=true)

빨간색으로 강조한 부분들을 꼭 유의해서 작성하도록 하자

### 4. mk 파일 생성

![create mk](https://github.com/Ekutz/Android-NDK/blob/master/create%20mk.png?raw=true)

text 형식으로 mk 파일을 생성한다.

이때 Android.mk 내부에 작성되야 할 코드는 아래와 같다.

```
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := 라이브러리 이름으로 지정될 이름
LOCAL_SRC_FILES := cpp파일 이름.cpp
LOCAL_LDLIBS    := -llog

include $(BUILD_SHARED_LIBRARY)

```

상술된 코드 외에도 ARM_MODE 등 다양한 옵션을 추가할 수 있다.

### 5. mk 파일과 링크하기

![link with gradle](https://github.com/Ekutz/Android-NDK/blob/master/link%20with%20gradle.png?raw=true)

![link with ndk-build](https://github.com/Ekutz/Android-NDK/blob/master/link%20with%20ndk-build.png?raw=true)

![set mk path](https://github.com/Ekutz/Android-NDK/blob/master/set%20mk%20path.png?raw=true)

생성한 mk 파일과 프로젝트를 링크하여 계속 진행합니다.

![modified build.gradle](https://github.com/Ekutz/Android-NDK/blob/master/modified%20build.gradle.png?raw=true)

제대로 연동이 되었다면 build.gradle(app)에 사진과 같은 새로운 내용이 추가됩니다.

### 6. 프로젝트 빌드하기

다양한 옵션을 추가, 수정하였기 때문에 이를 적용하기 위해 프로젝트를 한번 빌드 해줍니다.

![make project](https://github.com/Ekutz/Android-NDK/blob/master/make%20project.png?raw=true)

### 7. 헤더 파일 생성하기

앞서 3번 과정에서 설정한 javah를 이용하여 C/C++ 파일을 운용하기 위한 헤더 파일을 생성합니다.

![create header](https://github.com/Ekutz/Android-NDK/blob/master/create%20header.png?raw=true)

![result header](https://github.com/Ekutz/Android-NDK/blob/master/result%20header.png?raw=true)

### 8. C/C++ 파일 생성하기

mk 파일에 작성한 C/C++ 파일명과 동일한 C/C++ 파일을 생성합니다.

![create cpp](https://github.com/Ekutz/Android-NDK/blob/master/create%20cpp.png?raw=true)

### 9. C/C++ 소스 작성하기

Android Studio IDE를 쓰신다면 C/C++ 소스 내에서도 자동완성이 되는 것을 확인하실 수 있습니다.

**C/C++을 기존에 익히지 않으신 분들을 위한 설명**

```
#include <패키지_이름_프로젝트명_MainActivity.h>

구현하고자 하는 C/C++ 소스를 자바에서 손쉽게 불러올 수 있도록 h 파일에 선언하고 이를 C/C++ 소스에 포함시킵니다.

```

```
JNIEXPORT 리턴값 JNICALL Java_패키지_이름_프로젝트명_MainActivity_함수명
(JNIEnv *env, jobject obj, param1, param2...) {

}

JNI 함수의 기본 형입니다.
JNIEXPORT JINCALL은 반드시 적혀야 하며 입력값 중 JNIEnv와 jobject는 첫번째와 두번째로 적혀야합니다. 실제 자바 코드 상에서 두 입력값은 적히지 않습니다.
```

**완성된 C/C++ 소스**

```
#include <패키지_이름_프로젝트명_MainActivity.h>

JNIEXPORT jstring JNICALL Java_패키지_이름_프로젝트명_MainActivity_getJNIString
(JNIEnv *env, jobject obj) {
	
	return env->NewStringUTF("Hello JNI World!");
}
```

### 10. 헤더 수정하기

C/C++ 소스가 변경되었지만 h 파일은 변경되지 않았기 때문에 수정하여 두 소스를 맞춰줍니다.

![modify header](https://github.com/Ekutz/Android-NDK/blob/master/modify%20header.png?raw=true)

### 11. Java 소스 작성하기

원하는 메소드가 생성되었다면 이를 자바에서 불러오는 작업을 진행합니다.

```
static {
	System.loadLibrary("mk에서 작성한 모듈명");
}

제한자 native 리턴값 함수명();

```

![modify java](https://github.com/Ekutz/Android-NDK/blob/master/modify%20java.png?raw=true)

### 12. 프로젝트 마무리

activity_main.xml에 기본적으로 있는 TextView에 getJNIString()의 값을 넣어 setText 해보겠습니다.

![setText](https://github.com/Ekutz/Android-NDK/blob/master/setText.png?raw=true)

### 13. 프로젝트 결과

JNI 메소드를 이용하여 Java 코드를 사용할 수 있게 되었습니다.

![result](https://github.com/Ekutz/Android-NDK/blob/master/result.png?raw=true)

### 14. 포스트를 마무리하며...

1~13 과정을 적으면서 사실 Java 소스에 먼저 메소드를 적고 javah 작업을 하면 자동으로 헤더에 메소드를 생성해주고 C/C++ 소스에 붙여 넣어도 되는데 굳이 어렵게 돌아간 이유는 C/C++을 사용해보지 않은 초보 개발자들에게 작동 원리를 조금이라도 더 설명해주고픈 마음에서 였습니다.
이 글을 보고 NDK를 시작함에 있어서 조금 불편하더라도 정확한 길로 나아가길 바랍니다.
