 
#  1. charles不能抓包  

在mac上面，一般使用charles进行抓包，方便开发iOS进行debug和调试。近期，charles不能抓取mac上面的网络请求，这让笔者的开发很麻烦。
#  2. charles proxy设置  

遇到mac的网络请求不能抓包，首先确认charles的proxy选项设置，Proxy -> macOS Proxy，勾选上macOS Proxy，再试一试能否抓取mac的网络请求包。
#  3. 信任Charles根证书  

有时候不能抓包是charles的根证书没有被开发者信任，通过如下方式信任根证书，选择charles菜单，help -> SSL Proxying -> Install Charles Root Certificate，此时会打开mac的钥匙串访问程序，右键选择证书列表中的charles根证书，将该证书选择永久信任。需要注意的是，永久信任的选项隐藏比较深，找的时候注意点。

再试一试能否抓包。  
#  4. 代理冲突导致不能抓包

这是笔者遇到的问题，因为笔者使用的是代理上网方式，这可能根charles的代理有所冲突，解决方法是，设置 -> 网络 -> Wifi -> 高级 -> 代理，在左侧的配置协议列表中取消勾选"自动发现代理"和“自动代理配置”。

重启charles，再尝试一下，看能否charles抓取mac的网络请求包。

链接：https://www.jianshu.com/p/2cfef11edebb
来源：简书
