# ngrok

## 官网

[ngrok | API Gateway, Kubernetes Networking + Secure Tunnels](https://ngrok.com/)

## 使用

文档：[Overview | ngrok documentation](https://ngrok.com/docs)

1. 配置认证密钥
   
   配置文件位置：
   
   - Linux: `~/.config/ngrok/ngrok.yml`
   - MacOS (Darwin): `~/Library/Application Support/ngrok/ngrok.yml`
   - Windows: `"%HOMEPATH%\AppData\Local\ngrok\ngrok.yml"`
   
   ```yaml
   version: 3
   
   agent:
     authtoken: 4nq9771bPxe8ctg7LKr_2ClH7Y15Zqe4bWLWF9p
     api_key: 24yRd5U3DestCQapJrrVHLOqiAC_7RviwRqpd3wc9dKLujQZN
   ```
   
    将自己的认证密钥填到authtoken字段。

2. 启用代理
   
   启用代理有两种方式，直接在命令行输入要代理的端口，或者在ngrok.yml配置文件中配置。
   
   * 直接在命令行指定代理信息，如：
     
     http代理本地11434端口
     
     ```batch
     ngrok http 11434
     ```
     
     指定域名，免费账户只有一个，在账户的Universal Gateway > Domains中查看
     
     ```batch
     ngrok --url terrier-enabled-unduly.ngrok-free.app http 11434
     ```
   
   * 在配置文件中配置
     
     ```yaml
     version: 3
     
     agent:
       authtoken: 4nq9771bPxe8ctg7LKr_2ClH7Y15Zqe4bWLWF9p
       api_key: 24yRd5U3DestCQapJrrVHLOqiAC_7RviwRqpd3wc9dKLujQZN
     
     tunnels:
       basic:
         proto: http
         addr: 80
       ollama:
         url: terrier-enabled-unduly.ngrok-free.app
         proto: http
         addr: 11434
     ```
     
     其中，basic和ollama是代理的名字。
     
     启用方法：
     
     ```batch
     :: 启用一个
     ngrok start basic
     :: 启用多个
     ngrok start basic ollama
     :: 启用所有
     ngrok start --all
     ```

3. 使用api获取代理信息
   
   api有单独的密钥，需要在Identity & Access > API Keys中获取
   
   ```batch
   curl \
   -X GET \
   -H "Authorization: Bearer {API_KEY}" \
   -H "Ngrok-Version: 2" \
   https://api.ngrok.com/endpoints
   ```
   
   注意：这里的Ngrok-Version是api版本，和yaml配置文件里的version不一样。
   
   其他api参阅文档。

# FreeDns

官网：[FreeDNS - Free DNS - Dynamic DNS - Static DNS subdomain and domain hosting](https://freedns.afraid.org/)

## 域名注册和动态DNS解析

* 注册子域名
  
  1. 注册、登陆
  
  2. 左边For Members > Registry
  
  3. 选一个自己喜欢的域名，点进去
  
  4. 在新的界面填入Subdomain和验证码，保存即可
  
  5. 在For Members > Subdomains中可以看到自己的二级域名

* 开启动态DNS解析
  
  1. 左边For Members > Dynamic DNS
  
  2. 开头Check out: [dynamic update interface] (version 2)!，点击
  
  3. 勾选需要动态dns的域名，下拉框选Action:Enable Dynamic DNS，点Apply

* 使用ddns-updater进行ip更新
  
  ddns-update仓库地址：[qdm12/ddns-updater: Container to update DNS records periodically with WebUI for many DNS providers](https://github.com/qdm12/ddns-updater)
  
  配置文件格式见README，选择自己的域名提供商，我们选择FreeDns，格式如下：
  
  ```json
  {
    "settings": [
      {
        "provider": "freedns",
        "domain": "sub.domain.com",
        "token": "token",
        "ip_version": "ipv4",
        "ipv6_suffix": ""
      }
    ]
  }
  ```
  
  token在For Members > Dynamic DNS > dynamic update interface中获取，最下面表格中，最后一列有一个链接，如：
  
  http://v6.sync.afraid.org/u/z7VVZNYpysLXQf59cSgzLA2c/
  
  其中z7VVZNYpysLXQf59cSgzLA2c就是token。

* 除用于ip外，域名还可以重定向到其他域名
  
  1. For Members > Subdomains > 点击要改的域名
  
  2. 点击Destination后面的<u>Forward to a Url</u>，填入要重定向的网址和验证码，确认即可。勾选cloak，可以伪装域名，即在地址栏显示自己的域名，而不是重定向之后的域名。
  
  重定向到网页可以正常使用，但是ollama重定向后无法调用接口，且速度超级慢，可能是open webui的问题。
