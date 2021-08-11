## AI Service > AI Fashion > Maker API 가이드 

* AI Fashion Maker 서비스를 사용하는 데 필요한 API를 설명합니다.

## API 공통 정보 
### 사전 준비 
* API 사용을 위해서는 프로젝트 통합 Appkey 또는 서비스 Appkey가 필요합니다.
    * 프로젝트 통합 Appkey 사용을 권장합니다.
    * 프로젝트 통합 Appkey는 프로젝트 설정 페이지의 API 보안 설정에서 생성해 사용할 수 있습니다.
    * 서비스 Appkey는 콘솔 상단 **URL & Appkey** 메뉴에서 확인이 가능합니다.

### 색인 입력 파일 정보
* utf-8로 인코딩된 jsonl, csv 파일 포맷을 지원합니다.
    * csv파일의 경우 첫줄부터 실제 데이터로 채워야 합니다.
* 파일 크기는 최대 20MB 까지 가능하고, 최대 허용 문서수는 10,000개 입니다.
* 1일 최대 4회까지 업로드가 가능하며 매일 한국 시간 0시에 초기화 됩니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.

[API 도메인]

| 환경 | 도메인 |
| --- | --- |
| Real | https://ai-fashion.cloud.toast.com |

<span id="common-response"></span>
### 응답 공통 정보
- 모든 API 요청에 '200 OK'로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

[응답 본문 헤더]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | boolean | true: 정상<br>false: 오류 |
| header.resultCode | int | 0: 정상<br>0보다 큼: 부분 성공<br>음수: 오류 |
| header.resultMessage | string | "SUCCESS": 정상<br>그 외: 오류 메시지 리턴 |

[성공 응답 본문 예]

```json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "SUCCESS"
	}
}
```

[실패 응답 본문 예]

```json
{
	"header": {
		"isSuccessful": false,
		"resultCode": -40000,
		"resultMessage": "InvalidParam"
	}
}
```

## API 목차

### Index

* 인덱싱할 데이터를 전달합니다.
* 전달된 파일의 첫 번째 줄을 분석하여 포맷 오류 여부를 검사합니다.
* 첫 번째 줄에서 오류가 발견되지 않으면 색인을 위한 대기열에 들어간 뒤 스케쥴에 따라 색인 됩니다.

#### 파일 데이터 포맷

| 이름 | field | value type | 필수 | max length | 비고 |
| -- | -- | -- | -- | -- | -- |
| 상품id | product_id | string | O | 72 | unique key |
| 상태 |  status | string | O | 7 | enable: 추가 또는 업데이트 <br/>disable: 삭제  |
| 상품이름 |  name | string |O |  256 | 상품명 |
| 카테고리 1뎁스 |  category1_id | string | O | 72 | 카테고리 1뎁스 아이디|
| 카테고리 2뎁스 |  category2_id | string | O | 72 | 카테고리 2뎁스 아이디 |
| 카테고리 3뎁스 |  category3_id | string | O | 72 | 카테고리 3뎁스 아이디 |
| 이미지url |  image_url | string |O |  1000 | 접근 가능한 이미지 URL  |

##### 이미지 가이드

* 이미지의 패션 아이템의 너비와 높이가 둘 다 20px 이하인 경우는 인식하지 않습니다.
* 이미지 크기가 커질수록 패션 아이템의 크기도 커져야 더 정확하게 인식이 가능합니다.
* 이미지에서 패션 아이템이 차지하는 비중이 클수록 더 정확하게 인식이 가능합니다.
* 이미지 최대 크기: 최대 5,000,000 bytes
* 지원 이미지 포맷: PNG, JPEG, GIF

##### jsonl 예
` ``
{"product_id": "10001", "status": "enable", "name": "AAA red onepiece", "category_id1": "A001", "category_id2": "A001001", "category_id3": "A001001001", "image_url": "http://aaaaaaa.bbbbb.jpg"}
{"product_id": "10002", "status": "disable", "name": "BBB blue onepiece", "category_id1": "A001", "category_id2": "A001001", "category_id3": "A001001002", "image_url": "http://bbbbbbb.ccccc.jpg"}
...
```

##### csv 예
```
10001,enable,AAA red onepiece,A001,A001001,A001001001,http://aaaaaaa.bbbbb.jpg
10002,disable,BBB blue onepiece,A001,A001001,A001001002,http://bbbbbbb.ccccc.jpg
...
```

#### 요청
* 직접 데이터 파일 전송을 하거나 다운로드 가능한 url 전달하여 데이터 파일을 전달 할 수 있습니다.

[URI]

메서드 | URI
--- | ---
POST | /ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/index

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| service | 해당 Appkey에 소속된 service_id |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| format | string | O | jsonl | jsonl 또는 csv |

[Form Data]

| 이름 | 타입 | 필수 여부 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| link | string |  | "https://cdn.my-domain.com/202106251000_product.jsonl" | 데이터 파일 링크<br>link가 file 보다 우선 순위가 높다.<br>link가 있으면 file은 무시 됩니다 |
| file | file |  | @filename | 데이터 파일 <br>link가 file 보다 우선 순위가 높다.<br>link가 있으면 file은 무시 됩니다 |

<details>
<summary>요청 예 1</summary>

```
curl -X POST "/ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/index?format=jsonl" -H "Content-Type: multipart/form-file" -F "file=@/home/user1/202106251000_product.jsonl"
```

</details>

<details>
<summary>요청 예 2</summary>

```
curl -X POST "/ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/index?format=jsonl -F "link=https://cdn.my-domain.com/202106251000_product.jsonl"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./maker-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.indexID | string | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 인덱스 ID |
| data.docCnt | int | O | 1000 | 문서 갯수 |

<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "indexID": "24bb94b3-8a6b-488e-b038-4f6038da2596",
        "docCnt": 1000
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000| InvalidParam | 파라미터에 오류가 있음 |
| -40xxx| InvalidFile | 업로드된 파일 형식 오류 |
| -40xxx| DownLoadFail | link로 전달된 파일 다운로드 실패 |
| -40xxx| ParsingError | 파일의 첫 번째 줄 분석 실패 |
| -41000| UnauthorizedAppKey | 승인되지 않은 Appkey |
| -50000| InternalServerError | 서버 오류 |

### service info
* 서비스들의 현재 정보를 확인합니다. 
    * 서비스별로 남은 색인 횟수
    * 서비스별 색인된 문서 갯수

#### 요청

[URI]

메서드 | URI
--- | ---
GET | /ai-fashion-maker/v1.0/appkeys/{appKey}/services
[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |

<details>
<summary>요청 예 </summary>

```
curl -X GET "/ai-fashion-maker/v1.0/appkeys/{appKey}/service/info" -H "Content-Type: application/json"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./maker-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.totalService | int | O | 3 | 서비스 갯수 |
| data.items[].documentCnt | int | O | 51128 | 전체 문서 갯수 |
| data.items[].remainInsertCnt | int | O | 3 | 가능한 Index 횟수 |
| data.items[].service | string | O | aaa | 상품에서 등록한 service_id |

<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalService": 3
        "items": [
            {
                "service": "aaa",
                "remainInsertCnt": 3,
                "documentCnt": 51128
            },
            {
                "service": "bbb",
                "remainInsertCnt": 2,
                "documentCnt": 9778
            },
            {
                "service": "ccc",
                "remainInsertCnt": 0,
                "documentCnt": 29841
            }
        ]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000| InvalidParam | 파라미터에 오류가 있음 |
| -41000| UnauthorizedAppKey | 승인되지 않은 Appkey |
| -50000| InternalServerError | 서버 오류 |

### Indexes Status
* Index들의 현재 상태를 확인합니다. 
* index status 최대 보관 기간은 등록 시간 기준 6개월입니다.

#### 요청

[URI]

메서드 | URI
--- | ---
GET | /ai-fashion-maker/v1.0/appkeys/{appKey}/service/{service_id}/indexes

[Path Variable]

| 이름 | 설명 |
| --- | --- |
| appKey | 통합 Appkey 또는 서비스 Appkey |
| service | 해당 Appkey에 소속된 service_id |

[URL Parameter]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| start | int | O | 0 | 시작 index.<br>0부터 시작. |
| limit | int | O | 100 | 최대 100.<br>start:0, limit:100의 경우 1부터~100까지. <br>start:200, limit:100 이면 201부터~300까지. |
| order | string | X | "reservedTime:desc" | 기본 값. 등록 시간 내림 차순. 정렬 조건은 최대 1개 가능 |
| status | string | X | "finished" | 색인의 상태 값 |
| 

#### paging
* start와 limit 파라미터로 페이징이 가능합니다.
    * start: 0 부터 시작합니다.
    * limit: 0 보다 커야 하며 최대 100까지 가능합니다.
* 최대 페이징 가능한 숫자는 1000입니다.
    * 가능: 
        * start: 900
        * end: 100
    * 불가능:
        * start: 901
        * end: 100
        * 최대 가능 페이징 수인 1000을 넘어가기 때문에 불가능합니다.

#### 정렬
* 응답 문서의 정렬 파라미터
* 파라미터 형식. 
    * `정렬 가능 항목`:`정렬 방식`
* 정렬 가능 항목
    * reservedTime: index 등록 시간
    * startTime: 색인 시작 시간
    * finishTime: 색인 종료 시간
    * addCnt: 추가된 문서 수
    * failCnt: 실패 문서 수
    * deleteCnt: 삭제 문서 수
    * updateCnt: 수정 문서 수
    * totalCnt: 전체 문서 수
* 정렬 방식
    * asc: 오름 차순
    * desc: 내림 차순

#### 색인 상태
* 색인 상태 값을 조건으로 검색할 수 있습니다.
    * reserved : 대기
    * running : 진행중 
    * failed : 전체 실패
    * finished: 끝(부분 실패 포함)

<details>
<summary>요청 예 </summary>

```
curl -X GET "/ai-fashion-maker/v1.0/appkeys/{appKey}/indexes?start=0&limit=100&status=running&order=startTime:desc"  -H "Content-Type: application/json"
```

</details>

#### 응답

* [응답 본문 헤더 설명 생략]
    * [응답 공통 정보](./maker-api-guide/#common-response)에서 확인 가능

[응답 본문 데이터]

| 이름 | 타입 | 필수 | 예제 | 설명 |
| --- | --- | --- | --- | --- |
| data.total | int | O | 100 | 검색된 전체 문서 개수 |
| data.items[].service | String | O | testserviceid | 해당 색인요청이 발생한 서비스명 |
| data.items[].id | String | O | 24bb94b3-8a6b-488e-b038-4f6038da2596 | 색인 id |
| data.items[].filename | String | O | 202106251000_product.jsonl | 색인 파일 이름을 나타냅니다. |
| data.items[].status | string | O | reserved | 현재 색인 상태를 나타냅니다<br>reserved : 대기<br>running : 진행중 <br>failed : 전체 실패<br/>finished: 끝(부분 실패 포함) |
| data.items[].reservedTime | unix timestamp | O | 1625098033 | 색인 등록 시간 |
| data.items[].startTime | unix timestamp | O | 1625098033 | 색인 시작 시간 |
| data.items[].finishTime | unix timestamp | O | 1625098033 | 색인이 완료된 시간 |
| data.items[].addCnt | Int | O | 234 | 추가된 문서 갯수입니다. |
| data.items[].failCnt | Int | O | 31 | 실패한 문서 갯수입니다.<br/>Image Download 실패 등이 포함되며 Fashion Item Detect 실패도 포함됩니다. |
| data.items[].deleteCnt | Int | O | 31 | 삭제된 문서 갯수입니다. |
| data.items[].updateCnt | int | O | 592 | 수정된 문서 갯수입니다. |
| data.items[].totalCnt | Int | O | 888 | 색인 총 문서 갯수입니다. |

<details>
<summary>응답 본문 예</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "total": 100,
        "items":[{
            "service": "testserviceid"
            "id": "24bb94b3-8a6b-488e-b038-4f6038da2596",
            "filename": "202106251000_product.jsonl",
            "status": "reserved",
            "reservedTime": 1627018935,
            "startTime": 1627018935,            
            "finishTime": 1627018935,
            "addCnt": 234,
            "failCnt": 31,
            "deleteCnt": 31,        
            "updateCnt": 592,
            "totalCnt": 888      
        }]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | 설명 |
| --- | --- | --- |
| -40000| InvalidParam | 파라미터에 오류가 있음 |
| -41000| UnauthorizedAppKey | 승인되지 않은 Appkey |
| -50000| InternalServerError | 서버 오류 |
