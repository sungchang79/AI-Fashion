## AI Service > AI Fashion > Service API Guide

- This guide describes APIs required to use the AI Fashion Search service.

## API Common Information
### Prerequisites
- To use the APIs, you need an integrated project Appkey or a service Appkey.
    - We recommend you use an integrated project Appkey.
    - You can use an integrated project Appkey after creating it from the API security settings in the project settings page.
    - You can find a service Appkey in the **URL & Appkey** menu at the top of the Console.

### Request Common Information
- To use the APIs, security key authentication is required.

[API Domain]

| Environment | Domain |
| --- | --- |
| Real | https://ai-fashion.cloud.toast.com |

<span id="input-image-guide"></span>
### Input Image Guide

* If it takes longer than 3 seconds to download an input image, it is regarded as a failure.
* If the width and height of a fashion item in the input image are both 20px or below, the item is not recognized.
* For more accurate recognition, the size of a fashion item needs to increase as the image size increases.
* For more accurate recognition, a fashion item needs to take up a larger portion in the image.
* Maximum image size: 5,000,000 bytes
* Supported image formats: PNG, JPEG, GIF

<span id="common-response"></span>
### Response Common Information

- The APIs responds with "200 OK" to all API requests. For more information on the response results, see Response Body Header.

[Response Body Header]

| Name | Type | Description |
| --- | --- | --- |
| header.isSuccessful | boolean | true: Normal<br>false: Error |
| header.resultCode | int | 0: Normal<br>Positive number: Partial success<br>Negative number: Error |
| header.resultMessage | string | "SUCCESS": Normal<br>Otherwise: Return an error message |

[Example of Success Response Body]

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

[Example of Failure Response Body]

```json
{
    "header": {
        "isSuccessful": false,
        "resultCode": -40000,
        "resultMessage": "InvalidParam"
    }
}
```

## API Contents

### Register Service ID

* API to register service ID.
* Up to 5 service IDs can be registered.
* Allowed characters
    * Lowercase letters, numbers, '-', '\_' are allowed.
    * 2-32 characters are allowed.
    * Only lowercase letter is allowed for the first character.

#### Request

[URI]

| Method | URI |
| --- | --- |
| POST | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

<details><summary>Request Example</summary>

```
curl -X POST "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

<details><summary>Response Body Example</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42010 | DuplicateServiceID | Duplicated service ID. |
| -42030 | ServiceIDQuotaExceededException | Allowed number of Service IDs has been exceeded. |
| -50000 | InternalServerError | Server error. |

### Delete Service ID

* API to delete registered service ID.
* If an indexing request is in progress for a service ID, the service ID cannot be deleted.

#### Request

[URI]

| Method | URI |
| --- | --- |
| DELETE | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

<details><summary>Request Example</summary>

```
curl -X DELETE "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

<details><summary>Response Body Example</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -42020 | CannotDeletedOnIndexRunning | Cannot be deleted while indexing is running. |
| -50000 | InternalServerError | Server error. |

### Service ID List

* API to check the list of registered service IDs.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/services |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/services"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 5 | Total number of search results |
| data.items[].serviceID | String | O | my-service | Service ID |

<details><summary>Response Body Example</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 2,
        "items": [{
                "serviceID": "my-service"
            },
            {
                "serviceID": "second_service"
            }
        ]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -50000 | InternalServerError | Server error. |

### Search By ProductID

* API to search for products including similar fashion items based on product ID.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID} |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |
| productID | Product ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | Max size<br>Can be set in value from 1 to 200 |



<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/product/{productID}?limit=100"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | Total count of search results |
| data.query | String | O | productID=10234455&limit=100 | Search query |
| data.items[].similarity | Number | O | 0.91234 | Search similarity score |
| data.items[].productID | String | O | 8980335 | Product ID |

<details><summary>Response Body Example</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 100,
        "query": "productID=10234455&limit=100",
        "items": [{
                "similarity": 0.91234,
                "productID": "8980335"
            },
            {
                "similarity": 0.81234,
                "productID": "7980335"
            }
        ]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -40050 | NotFoundProductIDError | Product ID not found. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -50000 | InternalServerError | Server error. |

### Detect

* API to detect fashion items in the input image.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| path | String | O | `https://imagecdn.co.kr/sample_image.jpg` | URL of the URL-encoded image |

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/detect?path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg"
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | Total count of search results |
| data.query | String | O | `path=https://imagecdn.co.kr/sample_image.jpg` | Search query |
| data.items[].link | String | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | A link to be used in search by image API. |
| data.items[].center | float64 array | O | [0.825047801147227, 0.330948979591837] | Center x, y coordinate % of a detected item |
| data.items[].b0 | float64 array | O | [0.676864247418738, 0.219377551020408] | x0, y0 coordinate % of a detected item |
| data.items[].b1 | float64 array | O | [0.973231355525813, 0.4426204081632654] | x1, y1 coordinate % of a detected item |
| data.items[].score | float32 | O | 0.9732 | Confidence score of a detected item |

<details><summary>Response Body Example</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 2,
        "query": "path=https%3A%2F%2Fimagecdn.co.kr%2Fsample_image.jpg",
        "items": [{
                "link": "eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWFnZS1zZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3VjLm9jdGV0LXN0cmVbsInR5cGUiOiJBTEwiLCJpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MTcyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVfV0sImNvbmZpZ3MiOnsiY2FtZXJjp0cnVlfX0=",
                "center": [0.825047801172275, 0.330998979591837],
                "b0": [0.676864244718738, 0.219387751020408],
                "b1": [0.973231357555813, 0.4426020401632654],
                "score": 0.97323
            },
            {

                "link": "eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0==",
                "center": [0.3929254301032506, 0.572066265306123],
                "b0": [0.3288718929253023, 0.506377551204082],
                "b1": [0.456978967952199, 0.637751020408163],
                "score": 0.97123

            }
        ]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -45020 | ImageTooLargeException | The size of the image file is too large.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45040 | InvalidImageFormatException | Unsupported image file format.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45050 | InvalidImageURLException | The URL is not accessible. |
| -45060 | ImageTimeoutError | Image download timeout occurred. |
| -50000 | InternalServerError | Server error. |

##### Response Code

* Always 200.

| Status | Description |
| --- | --- |
| 200 | OK |

### Search By Image

* API to search for products including similar fashion items based on the link received as the response from detect API.

#### Request

[URI]

| Method | URI |
| --- | --- |
| GET | /nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image |

[Path Variable]

| Name | Description |
| --- | --- |
| appKey | Integrated Appkey or service Appkey |
| serviceID | Service ID |

[URL Parameter]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| limit | int | O | 100 | Max size<br>Can be set in value from 1 to 200 |
| link | string | O | eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0= | A link received from detect API |

<details><summary>Request Example</summary>

```
curl -X GET "${domain}/nhn-ai-fashion/v1.0/appkeys/{appKey}/service/{serviceID}/image?limit=100&amp;link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9u1XdzLmNvbS9mZy1pbWFnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUi0iJBTEwiLpbnB1dHMiOlt7ImJveCI6eyJsZWZ0IjozNQaInRvcCI6MyLCJ3aWR0aCI6MTU1LCJoZWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0=="
```

</details>

#### Response

* [Response Body Header description omitted]
    * This information is available in [Response Common Information](./service-api-guide/#common-response).

[Response Body Data]

| Name | Type | Required | Example | Description |
| --- | --- | --- | --- | --- |
| data.totalCount | Number | O | 100 | Total count of search results |
| data.query | String | O | link=eyJwYXRoIjoHR0cHM6Ly9zMy11cy13ZXN0LTIuW...VlfX0=&limit=100 | Search query |
| data.items[].similarity | Number | O | 0.91234 | Search similarity score |
| data.items[].productID | String | O | 8980335 | Product ID |

<details><summary>Response Body Example</summary>

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "totalCount": 100,
        "query": "link=eyJwYXRoIjoiaHR0cHM6Ly9zMy11cy13ZXN0LTIuW1hem9uYXdzLmNvbS9mZy1pbWfnZSZWFyY2gvMjAxOTEyMDIvNDIyMDZmWYtYWI0Ni00Zjk2LThkYWItZGRkZjllMTI3OWVm9jdGV0LXN0cmSIsInR5cGUiOiJBTEwiLpbnB1dHMi0lt7ImJveCI6eyJsZWZ0IjozNQsInRvcCI6MyLCJ3aWa0aCI6MTU1LCJozWlnaHQiOjE3NX0sInNjb3JlIjowLjg4NjAyODcwNzAyNzQzNTMsInR5cGUiOiJKQUNLRVQifV0sImNvbmZpZiOnsiY2FtZXJhIjp0cnVlfX0==&limit=100",
        "items": [{
                "similarity": 0.91234,
                "productID": "8980335"
            },
            {
                "similarity": 0.81234,
                "productID": "7980335"
            }
        ]
    }
}
```

</details>

#### Error Codes

| resultCode | resultMessage | Description |
| --- | --- | --- |
| -40000 | InvalidParam | There is an error in the parameter. |
| -41000 | UnauthorizedAppKey | Unauthorized Appkey. |
| -42000 | NotExistServiceID | Unregistered service ID. |
| -45020 | ImageTooLargeException | The size of the image file is too large.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45040 | InvalidImageFormatException | Unsupported image file format.<br>See [Input Image Guide](./service-api-guide/#input-image-guide). |
| -45050 | InvalidImageURLException | The URL is not accessible. |
| -45060 | ImageTimeoutError | Image download timeout occurred. |
| -50000 | InternalServerError | Server error. |
