- [필요기능](#%ED%95%84%EC%9A%94%EA%B8%B0%EB%8A%A5)
- [앱(App)](#%EC%95%B1App)
- [모델(Model)](#%EB%AA%A8%EB%8D%B8Model)
  - [TimeStampModel](#TimeStampModel)
  - [User(AbstractUser)](#UserAbstractUser)
  - [Like](#Like)
  - [Image](#Image)
  - [Review](#Review)
  - [Comment](#Comment)
  - [class 봉사대상](#class-%EB%B4%89%EC%82%AC%EB%8C%80%EC%83%81)
  - [class 봉사활동](#class-%EB%B4%89%EC%82%AC%ED%99%9C%EB%8F%99)
  - [Volunteer](#Volunteer)
- [Page에 필요한 api](#Page%EC%97%90-%ED%95%84%EC%9A%94%ED%95%9C-api)
  - [1. 리뷰 검색하기](#1-%EB%A6%AC%EB%B7%B0-%EA%B2%80%EC%83%89%ED%95%98%EA%B8%B0)
    - [PARAMS](#PARAMS)
    - [ex)](#ex)
  - [2. 봉사 검색하기](#2-%EB%B4%89%EC%82%AC-%EA%B2%80%EC%83%89%ED%95%98%EA%B8%B0)
    - [PARAMS](#PARAMS-1)
    - [ex)](#ex-1)
  - [3. Review 쓰기](#3-Review-%EC%93%B0%EA%B8%B0)
    - [BODY](#BODY)
  - [4. 좋아요 하기](#4-%EC%A2%8B%EC%95%84%EC%9A%94-%ED%95%98%EA%B8%B0)
  - [5. 프로필 보기](#5-%ED%94%84%EB%A1%9C%ED%95%84-%EB%B3%B4%EA%B8%B0)
    - [RESULT](#RESULT)
  - [6. 프로필 게시물 보기](#6-%ED%94%84%EB%A1%9C%ED%95%84-%EA%B2%8C%EC%8B%9C%EB%AC%BC-%EB%B3%B4%EA%B8%B0)
    - [RESULT](#RESULT-1)
  - [7. 좋아요 게시물 보기](#7-%EC%A2%8B%EC%95%84%EC%9A%94-%EA%B2%8C%EC%8B%9C%EB%AC%BC-%EB%B3%B4%EA%B8%B0)
    - [RESULT](#RESULT-2)
  - [이 아래로 회원가입을 위한 post도 짜야됨](#%EC%9D%B4-%EC%95%84%EB%9E%98%EB%A1%9C-%ED%9A%8C%EC%9B%90%EA%B0%80%EC%9E%85%EC%9D%84-%EC%9C%84%ED%95%9C-post%EB%8F%84-%EC%A7%9C%EC%95%BC%EB%90%A8)
# 필요기능
리뷰쓰기
회원가입
회원관리
리뷰보기
검색

# 앱(App)
* abstract
* Profile
* Activity
* Review

# 모델(Model)
## TimeStampModel
* created_at = datetime
* updated_at = datetime
* meta: abstract

## User(AbstractUser)
* profile_image = image
* name = char(blanck=True)
* 봉사대상 = 외부키(봉사대상)
* 봉사활동 = 외부키(봉사활동)
* 시/도 = char
* 시/군/구 = char
* @property
* def review_count(self) : return self.reviews.all().count()

## Like
* creator = ForeinKey(profile)
* review = ForeinKey(review, releated_name="reviews")

## Image
* file = image
* caption = char

## Review
* 제목 = char
* 본문 = char
* 사진 = foreinKey(Image,null=True,releated_name='reviws')
* 봉사대상 = foreinKey(봉사대상,releated_name="reviews)
* 봉사활동 = foreinKey(봉사활동,releated_name="reviews)
* creator = foreinKey(user,releated_name="reviews)
  
## Comment
* creator = ForeinKey(user, releated_name="comments")
* message = Text
* review = foreinkey(Review,releated_name='comments')

## class 봉사대상 
* name = char

## class 봉사활동 
* name = char

## Volunteer
* 제목 = char 
* 시작기간 = date
* 종료기간 = date
* 봉사활동 = foreinKey(봉사활동,releated_name="volunteers")
* 봉사대상 = foreinKey(봉사대상,releated_name="volunteers")
* 시/도 = char
* 시/군/구 = char
* isRecurting = booleen
* link = url
* class meta: ordering = ['-종료기간']

# Page에 필요한 api
activity나 subject는 문자열이 아니고 데이터베이스의 키를 참조하게 해도 됨. 그게 효율적이긴 함. 또한 다른 값들도 키를 참조하게 해도 됨.
## 1. 리뷰 검색하기

> GET /search/reviews/

### PARAMS  
| key      |      type       |     설명     |  필수   |
| :------- | :-------------: | :--------: | :---: |
| page     |       int       |   페이지 넘버   | 기본 1  |
| activity | string, many(,) |  봉사활동 필터   |  1+   |
| subject  | string, many(,) |  봉사대상 필터   |  1+   |
| city     |     string      |  봉사지역 시/도  | False |
| town     |     string      | 봉사지역 시/군구/ | False |
### ex)
> search/reviews/?activity=코딩,수영,청소&subject=노인,장애인&city=서울특별시&town=도봉구

**RESULT**  
```
HTTP 200 OK
Allow: GET, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

data : [
    {
      제목
      본문
      봉사대상
      봉사활동
      creator : {
        profile_image
        name
      }
      사진들 : [
        {
          file
          caption
        }
      ]
      comments : [
        {
          id
          create__name
          message
        }
      ]
    },
    ...
  ]
```
## 2. 봉사 검색하기

> GET /search/volunteers/

### PARAMS  
| key      |      type       |     설명     |  필수   |
| :------- | :-------------: | :--------: | :---: |
| page     |       int       |   페이지 넘버   | 기본 1  |
| activity | string, many(,) |  봉사활동 필터   |  1+   |
| subject  | string, many(,) |  봉사대상 필터   |  1+   |
| city     |     string      |  봉사지역 시/도  | False |
| town     |     string      | 봉사지역 시/군구/ | False |
### ex)
> search/volunteers/?activity=코딩,수영,청소&subject=노인,장애인&city=서울특별시&town=도봉구

**RESULT**  
```
HTTP 200 OK
Allow: GET, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

data : [
    {
      제목
      시작기간
      종료기간
      isRecurting
      link
    },
    ...
  ]
```

## 3. Review 쓰기

> POST /reviews/
### BODY  
| key   |  type  |  설명   |  필수   |
| :---- | :----: | :---: | :---: |
| head  | string |  제목   | True  |
| body  | string |  본문   | True  |
| image |  file  |  이미지  | False |

## 4. 좋아요 하기

> POST /reviews/\<reviews:id>/like

## 5. 프로필 보기

> GET /profiles/\<profile:id>/


### RESULT
```
data : {
  profile_image
  name
  activity : [
    { name}
  ]
  review_count
}
```

## 6. 프로필 게시물 보기

> GET /profiles/\<profile:id>/review

| key  | type  |  설명   |  필수   |
| :--- | :---: | :---: | :---: |
| page |  int  | 페이지수  | 기본 1  |

### RESULT
```
알아서해라
```

## 7. 좋아요 게시물 보기

> GET /profiles/\<profile:id>/like


| key  | type  |  설명   |  필수   |
| :--- | :---: | :---: | :---: |
| page |  int  | 페이지수  | 기본 1  |

### RESULT
```
알아서해라
```


## 이 아래로 회원가입을 위한 post도 짜야됨