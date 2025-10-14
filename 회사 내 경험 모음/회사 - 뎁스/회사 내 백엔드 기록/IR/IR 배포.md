
[IR 배포 과정]
1. 클린 후 빌드
2. IR 배포 파일에 war파일 복사 (ROOT)로 이름 변경
 - 빌드 결과는 wisdomir/target/libs/wisdomir-1.0.0-SNAPSHOT.war 파일이 있을 거임

3. 톰캣 서비스 종료
4. 원격 접속 후, C:\apache-tomcat-9.0.74\webapps 위치에 있는 파일 2개를 old 폴더 내에 20240511같이 현재날짜 폴더 만들어두고 안에 이동하기

5. 비어있는 C:\apache-tomcat-9.0.74\webapps 위치에 새 ROOT.war 저장
6. 톰캣 서비스 시작 

7. 이 시점에 아래와 같이 파일이 있어야 한다.
 - ir배포 폴더에는 새로 빌드한 ROOT.war가 있다
 - old 폴더 내 현재날짜에는 기존에 톰캣에서 돌아가던 ROOT 폴더와 ROOT.WAR이 있어야 한다.
 -  C:\apache-tomcat-9.0.74\webapps 새로 서비스 돌린 톰캣 내 새 war파일은 root.war, root 폴더가 만들어져 있어야 한다.