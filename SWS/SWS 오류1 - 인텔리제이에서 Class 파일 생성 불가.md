## 오류 1
프로젝트에 자바 파일을 추가하려고 하는데, 실수로 텍스트 파일 타입으로 추가해서 삭제하고 다시 자바 파일로 생성하려고 했지만 [템플릿 "Class"을(를) 구문 분석할 수 없습니다. 오류 메시지: Selected class file name 'QueryController.java' mapped to not java file type '파일 형식이 파일 내용을 기반으로 자동 탐지됩니다']라는 오류만 발생하고 자바 파일로 생성되지 않았다!

## 해결 1

File > Setting > 에디터 > 파일 타입에 접근하여서
'파일 형식이 파일 내용을 기반으로 자동 탐지됩니다' 에 들어가면
파일 이름 패턴에 텍스트 파일로 저장해놓은 것이 있을 것이다!
그걸 삭제하고 다시 자바 파일을 생성하면 잘 생성된다!

![](https://velog.velcdn.com/images/chhaewxn/post/504d8f07-3f77-43d1-b418-a8847372fa22/image.png)


