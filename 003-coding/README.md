# Coding Convention

TWTL Doc 003

TWTL 프로젝트에서 작성하는 프로그램 소스 코드에 대한 규약을 기술합니다.

## Overview

* Engine

 기본적으로 윈도우 API 규약을 따른다.
 
* GUI

 기본적으로 C# Standard 규약을 따른다.


##  Communal Coding convention elements

1. 코딩 스타일

 Visual studio의 기본인 BSD를 따른다.

1. 들여쓰기

 절대 space와 tab을 섞어쓰지 않는다. 들여쓰기는 tab으로 통일한다.
 
1. 문장의 종료

 문장의 종료는 세미콜론(;)을 사용한다.
 
1. 명명규칙

 * 공백을 허용하지 않는다.
 
 * 상수는 대문자와 언더스코어(\_)를 사용한다.
 
   ex. SYMBOLIC\_CONSTANT;
  
 * 변수명이나 함수명은 카멜 표기법을, 기타는 파스칼 표기법을 따른다.
 
   ex. int exampleOfVariable;

       void exampleOfFuncttion;
       
       struct ExampleOfStruct;
 
 * 배열은 복수형으로 표기한다.
 
   ex. bags;

1. 블럭의 줄바꿈

 if/for/while 등 블럭구문을 사용할 때에는 한줄로 빼곡하게 쓰지 않는다. 괄호로 블럭의 시작과 끝을 명확히 정의한다. 단, 한줄짜리 블럭은 괄호 생략을 기본으로 한다.
 
1. 반환하기

 특정 값을 반환해야 하는 경우, 함수의 맨 마지막에서 한 번만 return 한다. 단, 예외 처리로 빠져나가기 위해 사용하는 return false;는 제외한다.
 
1. Return 위치

 Retrun문을 여러곳에서 사용하지 않는다.
 
  * 예외처리 조건문
  
  * 결과 값
