有些网站的使用是需要非常严格的网络配置的，现在遇到的除了是tun模式之外，甚至扩充到了全局扩展的复写配置。这是在微软下使用的copilot的严格限制，所以rules也是针对microsoft的：
```bash
prepend-rules:
  # 强制 Google 登录相关域名走代理
  - DOMAIN-SUFFIX,google.com,PROXY
  - DOMAIN-SUFFIX,googleapis.com,PROXY
  - DOMAIN-SUFFIX,gstatic.com,PROXY
  - DOMAIN-SUFFIX,googleusercontent.com,PROXY
  - DOMAIN-KEYWORD,google,PROXY

  # 强制 Microsoft/Copilot 登录相关域名走代理
  - DOMAIN-SUFFIX,bing.com,PROXY
  - DOMAIN-SUFFIX,microsoft.com,PROXY
  - DOMAIN-SUFFIX,live.com,PROXY
  - DOMAIN-SUFFIX,msauth.net,PROXY
  - DOMAIN-SUFFIX,msftauth.net,PROXY
  - DOMAIN-SUFFIX,microsoftonline.com,PROXY
```
强制走代理之后能够正常使用了,注意,这里的PROXY需要改为自己的对应的代理组,比如我这里使用的就是:
![[Pasted image 20260416083220.png]]
`狗狗加速.com`这个代理组的.所以最终的代理配置就变成了:
```bash
# Profile Enhancement Merge Template for Clash Verge

profile:
  store-selected: true

prepend-rules:
  # 强制微软登录相关域名走“狗狗加速”
  - DOMAIN-SUFFIX,msauth.net,狗狗加速.com
  - DOMAIN-SUFFIX,msftauth.net,狗狗加速.com
  - DOMAIN-SUFFIX,microsoftonline.com,狗狗加速.com
  - DOMAIN-SUFFIX,login.live.com,狗狗加速.com
  - DOMAIN-SUFFIX,login.microsoftonline.com,狗狗加速.com
  
  # Copilot 与 Bing 相关
  - DOMAIN-SUFFIX,bing.com,狗狗加速.com
  - DOMAIN-SUFFIX,sydney.bing.com,狗狗加速.com
  - DOMAIN-SUFFIX,edgeservices.bing.com,狗狗加速.com
  - DOMAIN-SUFFIX,copilot.microsoft.com,狗狗加速.com

  # Google 登录回调环节
  - DOMAIN-SUFFIX,accounts.google.com,狗狗加速.com
```
这样的形式了