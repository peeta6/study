# 线上巡检代理邮箱发送邮件说明

## 代码

``

```
from exchangelib import Credentials, Account, DELEGATE, Configuration, NTLM, Message, Mailbox, HTMLBody
from exchangelib.protocol import BaseProtocol, NoVerifyHTTPAdapter
import urllib3

# 屏蔽https证书警告
urllib3.disable_warnings() 
# 取消SSL安全连接警告
BaseProtocol.HTTP_ADAPTER_CLS = NoVerifyHTTPAdapter
cred = Credentials('qa', '+:=h-!Gw=!z.')

configuration = Configuration(server='smtp-notice.baijia.com', credentials=cred, auth_type=NTLM)
a = Account(
	primary_smtp_address='qa@notice.baijia.com', config=configuration, autodiscover=False, access_type=DELEGATE
)

m = Message(
	account=a,
	folder=a.sent,
	subject=u'这就是一个沟通',
	body=HTMLBody('沟通的测试邮件'),
	to_recipients=[Mailbox(email_address='jishixin@baijia.com')])
m.send_and_save
```

## 目的

此函数的修改是为了优化之前使用私人邮箱发送邮件账号密码暴露的问题，修改后使用qa@notice.baijia.com共有邮箱发送测试报告，有效的保护了个人隐私。

从需求的角度讲，就是登陆qa@notice.baijia.com邮箱，然后遍历邮件接收人列表，向所有接收人发送测试报告。

## 连接邮箱

exchangelib模块很多，其中Account，Credentials是用来连接邮箱的。``

```text
credentials = Credentials('域名\用户名', '密码')
configuration = Configuration(server='smtp-notice.baijia.com', credentials=cred, auth_type=NTLM)
account = Account('邮箱', credentials=credentials, autodiscover=True)
```

在我们的需求中需要将在Account()中设置autodiscover=False, access_type=DELEGATE，其中autodiscover表示是否使用自动发现协议自动查找EWS端点，(默认值= False)。

access_type表示授予此帐户的“credentials”的访问类型。有效的选项是“delegate”和“impersonation”。如果设置了'credentials'，则'delegate'为默认值。否则，默认为“impersonation”。

通过代理邮箱发送邮件，需要通过Configuration()重新加入代理邮箱的域名，并添加auth_type=NTLM告诉服务器使用NTLM身份验证方式，NTLM：问询/应答[身份验证](https://baike.baidu.com/item/身份验证/2193233)协议，百科链接：https://baike.baidu.com/item/NTLM/6371298?fr=aladdin。

## 发送邮件

Message模块是用来发送邮件的。``

```text
m = Message(
        account=account,#之前申明的账户
        subject='#标题',
        body='#邮件内容',
        to_recipients = [Mailbox(email_address='#收件人')]
    )
m.send()
```

## 屏蔽警告

在运行的代码前需要导入屏蔽警告的模块

````
from exchangelib.protocol import BaseProtocol, NoVerifyHTTPAdapter
import urllib3

# 屏蔽https证书警告
urllib3.disable_warnings() 
# 取消SSL安全连接警告
BaseProtocol.HTTP_ADAPTER_CLS = NoVerifyHTTPAdapter
````

## 巡检utils.py修改后的代码

``

```
def send_email(subject, body, attachments, receivers):
    """发送邮件"""
    username, password = EMAIL_USER, EMAIL_PASSWORD
    receivers = [f'{user}@baijia.com' for user in receivers]
    try:
        urllib3.disable_warnings()
        BaseProtocol.HTTP_ADAPTER_CLS = NoVerifyHTTPAdapter
        cred = Credentials('qa', password)

        configuration = Configuration(server='smtp-notice.baijia.com', credentials=cred, auth_type=NTLM)
        account = Account(
            primary_smtp_address=username, config=configuration, autodiscover=False, access_type=DELEGATE
        )
        m = Message(
            account=account,
            subject=subject,
            body=HTMLBody(body),
            to_recipients=[Mailbox(email_address=user) for user in receivers]
        )
        [m.attach(FileAttachment(name=os.path.basename(item), content=open(item, 'rb').read())) for item in attachments]

        m.send()
    except Exception as ex:
        logging.error(f'邮件发送失败:{ex}')
    logging.info(f'邮件成功发送至:{",".join(receivers)}')
```