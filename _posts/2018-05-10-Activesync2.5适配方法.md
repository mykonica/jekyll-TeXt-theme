---
layout: post
title:  "Activesync 2.5适配方法"
date:   2018-05-04 9:46:40 +0800
categories: 技术
---

* url
    
    deviceid只能包含数字和字母，长度只能是32字节；devicetype只能包含**数字**和**字母**。url格式错误的话可能会收到http 400错误。
    
* 登录
    
    Provision命令中，PolicyType应该设置为MS-WAP-Provisioning-XML。

    ``` 
    <?xml version="1.0" encoding="utf-8"?>
    <Provision xmlns="Provision" xmlns:settings="Settings">
        <Policies>
            <Policy>
                <PolicyType>MS-WAP-Provisioning-XML</PolicyType>
            </Policy>
        </Policies>
    </Provision>
    ```
    返回status = 2时，意味着不需要Policy，应视为登录成功。
    
    |  Value | Meaning |
    | --- | --- |
    | 1 | Success. |
    | 2 | There is no policy for this client. |
    | 3 | Unknown PolicyType value. |
    | 4 | The policy data on the server is corrupted (possibly tampered with). |
    | 5 | The client is acknowledging the wrong policy key. |

* 邮件操作
    - Sync命令（拉邮件列表，读邮件，标记邮件，删除邮件）
        - 所有Sync命令都需要带`<Class>Email</Class>`标签。
        - Sync命令不支持`BodyPreference`标签。
        - `GetChanges`应设置为1。
    - 查找GAL不支持`RebuildResults`和`DeepTraversal`标签。
    - 不支持服务器查找邮件。
    - 不支持`ItemOperations`下载附件。

        >读邮件时设置MIMESupport=2，服务器一次将邮件内容（包括附件）以mime结构返回。客户端再从mime结构里解析出附件。
        
        |  Value | Meaning |
        | --- | --- |
        | 0 | Never send MIME data. |
        | 1 | Send MIME data for S/MIME messages only. Send regular body for all other messages. |
        | 2 | Send MIME data for all messages. This flag could be used by clients to build a more rich and complete Inbox solution. |

    - 发邮件还是用`SendMail`命令，区别在于请求内容是mime编码后的邮件主体，`Content-Type`设置为`message/rfc822`。


