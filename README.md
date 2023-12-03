# 애플리케이션 코드가 들어있는 파일
# app/app.py Flask 
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql://user:password@mysql_db/db_name'
db = SQLAlchemy(app)

@app.route('/')
def hello():
    return 'Hello, Flask and MySQL!'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')



# MySQL 데이터베이스와 관련된 설정을 Dockerfile에 추가
# Dockerfile Flask 애플리케이션을 위한 Dockerfile
# 베이스 이미지로부터 시작
FROM python:3.8

# 작업 디렉토리 설정
WORKDIR /app

# 앱 종속성 설치
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# 앱 소스 추가
COPY app app

# 환경 변수 설정
ENV FLASK_APP=app/app.py
ENV FLASK_RUN_HOST=0.0.0.0

# Flask 애플리케이션 실행
CMD ["flask", "run"]



# 필요한 Python 패키지를 명시
# requirements.txt
Flask==2.0.1
Flask-SQLAlchemy==3.0
mysql-connector-python==8.0.23



# MySQL 컨테이너를 위한 Dockerfile을 작성
# MySQL Dockerfile

# 베이스 이미지로부터 시작
FROM mysql:latest

# 환경 변수 설정
ENV MYSQL_DATABASE=db_name
ENV MYSQL_USER=user
ENV MYSQL_PASSWORD=password
ENV MYSQL_ROOT_PASSWORD=root_password

# 포트 열기
EXPOSE 3306



# Docker Compose를 사용하여 Flask 애플리케이션과 MySQL 컨테이너를 함께 실행하는 docker-compose.yml 파일 작성
# docker-compose.yml 

version: '3'

services:
  flask-app:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - mysql-db

  mysql-db:
    build:
      context: ./path-to-mysql-dockerfile-directory
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: db_name
      MYSQL_USER: user
      MYSQL_PASSWORD: password
