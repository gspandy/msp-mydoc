[TOP]

<!--FORMAT 与 HOST 请勿修改-->

FORMAT: 1A

HOST: https://msaas.unifiedcloud.lenovo.com/

<!--定义API的名称-->

# User-Mgmt

<!--定义API的描述-->

This is my first API. Demo for user CURD.

<!--Polls is a simple API allowing consumers to view polls and vote in them.-->

<!--定义API的URL，格式： 描述 [URL后缀]-->
## User Collection [/your-service-name/your-api-name/users]

<!--定义API的一个GET方法，格式： 描述 [GET]-->
### List All Users [GET]


<!--定义此GET方法的描述-->

You may get user list by this action.


<!--定义API的返回状态及值，格式： + Response 状态码 (格式)-->

+ Response 200 (application/json)

             [
                {
                   "username": "chenxl6",
                    "nickname": "mujiang",
                    "created_at": "2017-07-19T08:40:51.620Z",
                    "modify_at": "2017-07-19T08:40:51.620Z"
                },
                  {
                    "username": "chenxl8",
                    "nickname": "mujiang",
                    "created_at": "2017-07-19T08:40:51.620Z",
                    "modify_at": "2017-07-19T08:40:51.620Z"
                },
                  {
                    "username": "chenxl9",
                    "nickname": "mujiang",
                    "created_at": "2017-07-19T08:40:51.620Z",
                    "modify_at": "2017-07-19T08:40:51.620Z"
                }
             ]


<!--定义API的一个POST方法，，格式： 描述 [POST]-->
### Create a New User [POST]


<!--定义此POST方法的描述-->

You may create a user using this action. 


<!--定义此POST方法需要传入的 参数(格式)，包括Headers（如有）和Body，格式： + Request 状态码 (格式) -->

+ Request (application/json)

    + Headers

            MSP-AuthKey: EKum6zvLoCRXUOTbzqlbP7Gg.1472543098

    + Body

            {
                "username": "chenxl18",
                "nickname": "mujiang"
            }


<!--定义此POST方法的返回状态及值，格式： + Response 状态码 (格式)-->

+ Response 201 (application/json)

    + Body

            {
                "username": "chenxl7",
                "nickname": "mujiang",
                "created_at": "2017-07-19T08:40:51.620Z",
                "modify_at": "2017-07-19T08:40:51.620Z"
            }

## User Entity [/your-service-name/your-api-name/users/{username}]

+ Parameters

    + username: chenxl7 (string)  - An unique identifier of the user.

### Get User Info [GET]


+ Request (application/json)

    + Headers

             MSP-AuthKey: EKum6zvLoCRXUOTbzqlbP7Gg.1472543098

+ Response 200 (application/json)

    + Body

            {
                "username": "chenxl7",
                "nickname": "mujiang",
                "created_at": "2017-07-19T08:40:51.620Z",
                "modify_at": "2017-07-19T08:40:51.620Z"
            }


### Modify User Info [PUT]

<!--定义此PUT方法需要传入的 参数(格式)，包括Headers（如有）和Body，格式： + Request 状态码 (格式) -->

<!--此处使用了模板，这样前端开发者调用你的API时，不用再严格匹配传入参数了，他们只需要知道参数的类型就行了。

以下的Schema部分相当于

    + Body

            {
                "nickname": "Any Words"
            }
            
-->

+ Request (application/json)

    + Headers

            MSP-AuthKey: EKum6zvLoCRXUOTbzqlbP7Gg.1472543098
            
    + Schema
    
            {
                "type": "object",
                "properties": {
                    "nickname": { "type": "string" },
                    "age": { "type": "number" }
                }
            }

+ Response 201 (application/json)

    + Body

            {
                "username": "chenxl18",
                "nickname": "xiaolei",
                "created_at": "2017-07-19T08:40:51.620Z",
                "modify_at": "2017-07-19T08:40:51.620Z"
            }

### Delete User [DELETE]

+ Request

    + Headers
    
            MSP-AuthKey: EKum6zvLoCRXUOTbzqlbP7Gg.1472543098


+ Response 200 (application/json)

    + Body
            {
                "description": "success"
            }

+ 
[END]