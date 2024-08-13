
- 자세한 내용은 무중단배포 매뉴얼 참고
	- `deps\20.문서\40.매뉴얼`
```shell
기본적인 명령어는 아래와 같음

create database 데이터베이스명;
CREATE USER 'new_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON database_name.* TO 'new_user'@'localhost';
flush privileges;
SHOW GRANTS FOR 'new_user'@'localhost';

백업mysqldump -u [사용자명] -p [데이터베이스명] > [백업파일명].sql
복원 mysql -u [사용자명] -p [데이터베이스명] < [백업파일명].sql


=================

1. mobaexterm 접속
2. deps / deps@admin

Last login: Fri Jun 14 14:43:59 2024 from 10.10.10.13
>> [deps@localhost ~]$ docker ps -a
CONTAINER ID   IMAGE                             COMMAND                  CREATED              STATUS              PORTS                                           NAMES
e2c5f6ce74c9   console-client-img-202406141500   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3011->3000/tcp, :::3011->3000/tcp       console-client-3011
e0843ab74d61   gncar-backend:631                 "java -Dspring.profi…"   3 hours ago          Up 3 hours          0.0.0.0:9013->9013/tcp, :::9013->9013/tcp       gncar-production-set2
023ee09d5fc7   console-backend:84                "java -Dspring.profi…"   21 hours ago         Up 21 hours         0.0.0.0:9011->9011/tcp, :::9011->9011/tcp       console-production-set2
a76144fdaa69   gncar-beta-client-img             "docker-entrypoint.s…"   23 hours ago         Up 23 hours         0.0.0.0:3093->3000/tcp, :::3093->3000/tcp       gncar-beta-client-3093
68ca7ae25f07   wisdom-client-img                 "docker-entrypoint.s…"   23 hours ago         Up 23 hours         0.0.0.0:3031->3000/tcp, :::3031->3000/tcp       wisdom-client-3031
1bf4e44364a0   cca-client-img-202406131113       "docker-entrypoint.s…"   28 hours ago         Up 28 hours         0.0.0.0:3021->3000/tcp, :::3021->3000/tcp       cca-client-3021
18b45e309d6c   gncar-client-img-202406111704     "docker-entrypoint.s…"   2 days ago           Up 2 days           0.0.0.0:3012->3000/tcp, :::3012->3000/tcp       gncar-client-3012
5b5c46020236   cwict-call-center-img             "python3 -u CallCent…"   2 days ago           Up 2 days           0.0.0.0:18099->18099/tcp, :::18099->18099/tcp   cwict-call-center
df3044f8b94b   gnwp-client-img-202406111525      "docker-entrypoint.s…"   3 days ago           Up 3 days           0.0.0.0:3004->3000/tcp, :::3004->3000/tcp       gnwp-client-3004
7b8303ed8165   wisdom-backend:259                "java -Dspring.profi…"   4 days ago           Up 4 days           0.0.0.0:9031->9031/tcp, :::9031->9031/tcp       wisdom-production-set1
6f11c39910c4   cca-backend:29                    "java -Dspring.profi…"   4 days ago           Up 4 days           0.0.0.0:9020->9020/tcp, :::9020->9020/tcp       cca-production-set1
a098e85f4f06   gnwp-back:22                      "java -Dspring.profi…"   4 days ago           Up 4 days           0.0.0.0:9001->9001/tcp, :::9001->9001/tcp       gnwp-production-set1
5463733aa58e   dflow:12                          "java -Dspring.profi…"   4 days ago           Up 4 days           0.0.0.0:9061->9061/tcp, :::9061->9061/tcp       dflow-production-set1
343c9550dcd6   gapt-client-img                   "docker-entrypoint.s…"   9 days ago           Up 4 days           0.0.0.0:3000->3000/tcp, :::3000->3000/tcp       gapt-client
b8518d375a4d   gapt_back:dev                     "java -jar sht_webap…"   9 days ago           Up 4 days           0.0.0.0:9000->8080/tcp, :::9000->8080/tcp       gapt_back_dev
45743522e5ba   deps-homepage:1.0                 "java -Dspring.profi…"   2 months ago         Up 4 days           0.0.0.0:18090->8080/tcp, :::18090->8080/tcp     deps-homepage
dff99f4dd36f   gncar-interface-dev:0.1           "java -Dspring.profi…"   2 months ago         Up 4 days           0.0.0.0:19013->8080/tcp, :::19013->8080/tcp     gncar-interface-dev
51c688c111ae   wisdom-batch-dev:1.0              "java -Dspring.profi…"   3 months ago         Up 4 days           0.0.0.0:18180->8180/tcp, :::18180->8180/tcp     wisdom-batch-dev
fd73fb5c0e0e   wisdomir-tomcat                   "catalina.sh run"        3 months ago         Up 4 days           0.0.0.0:20022->8080/tcp, :::20022->8080/tcp     wisdomir
a32bb22fd8ba   gncar-batch-dev:0.1               "java -Dspring.profi…"   4 months ago         Up 4 days           0.0.0.0:19012->8080/tcp, :::19012->8080/tcp     gncar-batch-dev
95ad73e37df3   tomcat:8.5.72                     "catalina.sh run"        4 months ago         Up 4 days           0.0.0.0:18081->8080/tcp, :::18081->8080/tcp     gntp_dev
b871284ca06a   tomcat:8.5.72                     "catalina.sh run"        7 months ago         Up 4 days           0.0.0.0:18080->8080/tcp, :::18080->8080/tcp     fm3_gnepa_dev
9eaf8425ab4b   python:3.8                        "python3"                10 months ago        Up 4 days           0.0.0.0:10880->8888/tcp, :::10880->8888/tcp     crawling_python
93d2db86b215   mariadb:latest                    "docker-entrypoint.s…"   12 months ago        Up 4 days           0.0.0.0:3306->3306/tcp, :::3306->3306/tcp       mariadb
>> [deps@localhost ~]$ ^C
>> [deps@localhost ~]$ docker exec -it 93d2db86b215 /bin/bash
>> root@93d2db86b215:/# mariadb -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 84206
Server version: 10.10.2-MariaDB-1:10.10.2+maria~ubu2204 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

>> MariaDB [(none)]> show databases
    -> ;
+--------------------+
| Database           |
+--------------------+
| base_common        |
| blog_study_a       |
| blog_study_b       |
| carina_kyungnam    |
| cca                |
| dflow              |
| flask_test         |
| gangso_web         |
| gapt               |
| gapt_bk            |
| gapt_gaon          |
| gapt_test          |
| gncar              |
| gncar_beta         |
| gntp               |
| gnwp               |
| homepage           |
| information_schema |
| ir                 |
| ir_live_20240422   |
| ir_temp            |
| linc               |
| mysql              |
| performance_schema |
| smartic            |
| sys                |
| wisdom             |
| wisdomir           |
+--------------------+
28 rows in set (0.003 sec)

>> MariaDB [(none)]> create database deps_groupware;
Query OK, 1 row affected (0.001 sec)

>> MariaDB [(none)]> show databases
    -> ;
+--------------------+
| Database           |
+--------------------+
| base_common        |
| blog_study_a       |
| blog_study_b       |
| carina_kyungnam    |
| cca                |
| deps_groupware     |
| dflow              |
| flask_test         |
| gangso_web         |
| gapt               |
| gapt_bk            |
| gapt_gaon          |
| gapt_test          |
| gncar              |
| gncar_beta         |
| gntp               |
| gnwp               |
| homepage           |
| information_schema |
| ir                 |
| ir_live_20240422   |
| ir_temp            |
| linc               |
| mysql              |
| performance_schema |
| smartic            |
| sys                |
| wisdom             |
| wisdomir           |
+--------------------+
29 rows in set (0.000 sec)

>> MariaDB [(none)]> CREATE USER 'groupware'@'%' IDENTIFIED BY 'deps@admin';
Query OK, 0 rows affected (0.002 sec)

>> MariaDB [(none)]> GRANT ALL PRIVILEGES ON deps_groupware.* TO 'groupware'@'%';
Query OK, 0 rows affected (0.002 sec)

>> MariaDB [(none)]> flush privileages;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'privileages' at line 1
MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.001 sec)

MariaDB [(none)]>

```