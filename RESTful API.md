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
    5. 再次建立新的websocket连接
    
3. 用户登出
    1. url :        /signout
    2. tpye:        GET
   
       
4. 用户注册
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

5. 获取用户信息
    1. url:      /userInfo
    2. type:     /GET
    3. dataType:    json
    4. return:
        ```
        {
            nickname : 昵称
            sex      : t(男)，f(女)
            intro    : 介绍
            avatar   ： 头像
            remark   : 好友印象
                        [{
                            nickname    : 昵称
                            account     : 用户账户名
                            content     : 内容
                            date        : 时间
                            avatar      : 头像       
                        },
                       ...
                       ]
                    
            friends : 好友列表
                 [{
                    nickname    : 昵称
                    account     : 用户账户名
                    online      : true/false
                },
                ...
                ]
            
        }
        ```
6. 用户信息更新
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
7. 申请/回应申请/删除 好友(WS)
    ```
        {
           type     :friend
           action   :add/delete
           feedback :accept/decline
           account  :对方账户名
        }   
    ```
   
## 聊天室信息

1.  websocket连接
    1. url : ws://localhost:8080/ChatChannel
    
2.  消息发送
    1. 方法：onmessage
    2. 用一个ID标记信息的首尾（字符串类型）
    3. 发送类型为JSON的字符串 
    ```
    {
        type : public / private
        account : 账户名称(当type为private时，添加此项)
     }
    ```
    4. 文本信息字符串类型发送
    5. 文件用blob传输，并且先发送一个类型为blob的数据流包含文件的扩展名
    
3.  新消息获取
    ```
        {
            type     : "message"
            msgType  :  private / public 
            account  : 用户账户名
            nickname : 昵称
            content  : 
                [{
                    type    :  text,
                    content :  内容
                },
                {
                    type : img,
                    url  : 图片地址
                },
                
               ...
               ]
            
            avatar   : URL 头像 
            isSelf   : true/false
            date     : 发送时间
        }
    ```

4. 在线用户列表获取
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

5. 在线用户登入/出消息提醒
    ```
    {
        type        : "signInRemind" /"signOutRemind"
        account     : 用户账户名 
    }

    ```
6. 聊天室历史消息获取(HTTP)
    ```
        1. url:     /getHistory
        2. type:    /POST
        3. dataType:  json
        4. data:
        {
            type        : public / private
            account     : 如果是private 这里发送账户名
            method      : first / second 
        }
        5. return
        
            {
                history:[ 内容同发送消息格式 ]
            }
                 
    ```
    一次将返回20条历史记录
## 聊天室其他请求

1. websocket 连接
    1. url： ws://localhost:8080/MultiRequest

2. 申请/回应申请/删除 好友
   ```
       {
           type        : "friendRequest"
           action      : add / delete 
           account     : 对方账户名 
           feedback    : accept / decline
       }
   ```
3. 离线消息提醒
     ```
       {
          type        : "msgRemind"
          account     : 发送者账户名
      }
     ```
  
4. 请求响应结果返回
     ```
    {
         type        : feedback
         status      : 1（成功）
                       0
         message     : ...
         error       : ...
    }
     ```
5. 消息盒子获取(HTTP)
    ```
        1. url      : /notifyBox?account=账户名
        2. type     : GET
        3. dataType : json
        4. return   : [{
                           "applicant"   : 发起者
                           "recipient"   : 接收者
                           "type"        : 请求类型(现在只有这一种friendRequest)
                           "hasresponse" : t / f 是否回应该请求
                           "feedback"    : accept / decline (仅当hasreponse为t时该项不为空)
                           "date"        : 时间 
                       }]
    
    ```

### 通知流程
    1. 有未读消息时给相应的用户设红点通知记号
    2. 当用户读取消息后设为已读
    3. 未处理的请求设为未响应
    4. 当用户查看消息盒子时可以查看所有通知及处理结果

**使用fetch进行GET、POST操作时，记得带上本地的SESSION ID**
