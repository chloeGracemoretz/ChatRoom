# 聊天室 RESTful API

## 用户信息
1. 获取用户登入信息
    1. url:         /signin
    2. type:        GET
    3. dataType:    json
    4. return：
        ```
        {
            status : 1 (成功)
                     0 (失败) 
            error  : "...." 
            nickname: 用户的昵称
            token  : 用来连接websocket的用户认证口令
        }
        ```
2. 用户登入
    1. url:         /sigin
    2. type:        /POST
    3. dataType:    json
    4. data: 
        ```
        {
            account : 账户名称
            pwd     : 密码
        }
        ```
    5. return：
        ```
        {
            status : 1 (成功)
                     0
            error  : "..."
        }
        ```
    4. 关闭当前的websocket连接
    5. 以登入用户的身份再次建立新的websocket连接
    
3. 用户注册
    1. url:         /signup
    2. type:        /POST
    3. dataType:    json
    4. data:
        ```
        {
            account : 用户账户名
            nickname: 昵称
            pwd     : 密码
        }
        ```
    5. return：
        ```
        {
            status : 1
                     0
            error  : "..."
        }
        ```
    6. 成功后跳转回公共聊天页面

4. 获取用户信息
    1. url:      /userInfo?account=用户账户名
    2. type:     /GET
    3. dataType:    json
    4. return:
        ```
        {
            nickname : 昵称
            sex      : t(男)，f(女)
            intro    : 介绍
            remark   : 好友印象
                    {
                        [{
                            nickname    : 昵称
                            account     : 用户账户名
                            content     : 内容
                            date        : 时间
                            url         : 头像       
                        }],
                       ...
                    }
            friends : 好友列表
            {
                [{
                    nickname    : 昵称
                    account     : 用户账户名
                    online      : true/false
                }],
                ...
            }
        }
        ```
5. 用户信息更新
    1. url:     /userInfo
    2. type:    /POST
    3. dataType:  json
    4. data:    FormData
        ```
            {
                avatar : 头像图片
                info   : JSON.stringify({
                    nickname : 昵称
                    pwd      : 密码
                    sex      : true(男)，false(女)
                    intro    : 简介
                })
            }
        ```
    5. 若某项没有修改则不添加该项

## 聊天室信息

1.  websocket连接
    1. url : ws://localhost:8080/websocket
  
2.  消息发送
    1. 方法：onmessage
    2. 用一个ID标记信息的首尾（字符串类型）
    3. 文本信息字符串类型发送
    4. 文件用blob传输，并且先发送一个类型为blob的数据流包含文件的扩展名
    
3.  新消息获取
    ```
        {
            type     : "message"
            account  : 用户账户名
            nickname : 昵称
            content  : {
                [{
                    type    :  text,
                    content :  内容
                }],
                [{
                    type : img,
                    url  : 图片地址
                }],
                
               ...
            }
            avatar   : URL 头像 
            isSelf   : true/false
            date     : 发送时间
        }
    ```

4. 在线用户列表获取(WS)
    ```
        {
            type     : "userList"
            userList :
                [
                    {
                        account  :账户名称
                        nickname : 昵称,
                        avatar   : url
                    },
                    ...
                ]
        }
    ```
5. 公共聊天室历史消息获取(HTTP)
    ```
        1. url:     /getHistory?type=first (二次获取type=second,一次加载20条)
        2. type:    /EGT
        3. dataType:  json
        4. return
        ```
            {
                history:[ 内容同发送消息格式 ]
            }
        ```
    ```