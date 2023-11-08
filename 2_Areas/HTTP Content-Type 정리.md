#contenttype #http #request #multipart/form-data #image #json #text 

> [!note]
> 다음 블로그 내용을 정리한 문서입니다. https://jw910911.tistory.com/117

HTTP Request는 아래와 같은 구조로 되어 있다.
- Request Line
- Headers
- Empty Line
- Body

그리고, 실제 리퀘스트는 아래와 같은 모습이다.

```sh
GET /api/hello HTTP/2
Host: api.example.com 
Content-Type: application/json 
Authorization: Bearer your-access-token 

{ "key1": "value1", "key2": "value2" }
```

#### application/json

RestFul API를 사용하게 되며 request를 날릴 때 대부분 json을 많이 사용하게 됨에 따라 자연스럽게 사용이 많이 늘게 되었다.

#### application/x-www-form-urlencoded

html의 form의 기본 Content-Type으로 요즘은 자주 사용하지 않지만 여전히 사용하는 경우가 종종 존재한다.


> [!tip]
> 차이점은 application/json은 =={key: value}==의 형태로 전송되지만 application/x-www-form-urlencoded는 ==key=value&key=value==의 형태로 전달된다.

#### multipart/form-data

Content-Type은 한 종류만 명시할 수 있다. 예를 들어 text이면 text/plain, xml이면 text/xml, png이미지이면 image/png 이런 식으로 명시하여 데이터를 서버로 전송한다. 

하지만 파일 업로드의 상황을 보면 사진에 대한 설명을 위한 input과 파일을 위한 input, 총 2개가 필요하다. 두 input에 대한 Content-Type은 전혀 다른데, 이미지의 설명에 대한 Content-Type은 _application/x-www-form-urlencoded_ 일 것이고, 사진 파일에 대한 Content-Type은 _image/png_ 일 것이다.

두 종류에 대한 Content-Type이 하나의 HTTP Request Body에 들어가야 하는데 ==두 종류의 데이터를 구분해서 넣어주는 방법이 필요==해서 multipart라는 개념이 들어가게 되었다.