# _51job
51job.com的战争记录。

## 几个域名
* login.51job.com:用户登录验证的域名
* search.51job.com:搜索职位的域名
* i.51job.com:登录之后的域名，投递职位接口域名

## 投递接口
* 投递接口是允许跨域访问的
* 投递简历接口封IP(i.51job.com)，另外(m.51job.com)与投递简历的接口在同一台机器上
* 投递接口支持单个投递和批量投递，使用批量投递更安全，不容易让管理员发现

## 登录接口
* 是单点登录的
* 多次登录失败会出现验证码
* 登录的验证码是多种字体类型的，训练数据很难

## 运作流程
1. 访问主页
2. 登录
3. 搜索职位
4. 勾选职位，批量投递
5. 退出登录

## 分布式单个投递运作流程
1. 生产者自动登录，自动搜索，然后将职位job_id获取，获取时候过滤职位名称，公司名称
2. 将_51job爬虫对象与单个job_id发送到delivery队列
3. 消费者，从队列中获取数据，反序列化_51job爬虫对象，然后调用对象的单个职位投递接口并传递job_id参数，发送请求

注意：这里只是说明了大致的思路，具体的参数还要看源码。

## rabbitmq任务队列安装
1. 安装eralng虚拟机
2. 安装rabbitmq-server并启动
3. 安装之后默认的账号密码均为guest

## 分布式多个投递运作流程
1. 与上面不同的是，一次性获取搜索页面所有的job_id（如果过滤参数过滤，则不是所有）
2. 与上面不同的是，将多个job_id发送到delivery_many队列
3. 与上面不同的是，调用对象的多个职位投递接口

## 验证码的验证
1. 假设登录失败，并知道失败的原因是要验证码
2. 访问验证码接口，下载验证码
3. 打开验证码图片，人工识别
4. 发送验证码鉴定接口请求，如果校验成功，则登录成功

注意：以上均为通过测试。

## 全局变量的解释
1. _51_ACCOUNT:前程无忧的账号
2. _51_PASSWD:前程无忧密码
3. FILTER_COMPANY:过滤公司名称数组
4. FILTER_JOB_NAME:过滤职位名称数组

## 代理IP的解决
* 免费代理
* ADSL动态拨号
* 将爬虫移植到手机端，中国移动，联通，电信，断开流量，重新打开流量来切换IP
* 拉电信固定IP的线路（据说一根线大概有10多个固定IP可用）
* 降低访问频率

## 代理IP的验证
* 以i.51job.com来验证，如果访问能正常返回数据，而不是被服务端直接丢弃请求，则认为IP有效

## 验证码神经网络模型的训练
由于相关数学知识欠缺，所以不能简单的去实现这些。