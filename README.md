基于springboot+redis模拟热搜排行榜(利用nginx实现交换负载)

1.解压并启动redis服务
打开redis-windows-7.2.3目录并双击redis-server-start.bat程序，会打开6个不同端口的服务器。
<img width="865" height="533" alt="image" src="https://github.com/user-attachments/assets/83788711-d2ea-40b0-a5ca-c42b50e612b6" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/c80a3261-dce4-4ae5-bfd8-f48ca3576011" />

2. 设定主从结构（3主3从）
在地址栏输入cmd，会打开一个命令窗口并输入：redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 --cluster-replicas 1
<img width="864" height="532" alt="image" src="https://github.com/user-attachments/assets/fefa06fb-5f8a-4773-874b-2f7f46cb7ce9" />
<img width="865" height="445" alt="image" src="https://github.com/user-attachments/assets/9e01cb12-a476-41a6-851f-f4993be7296a" />
 
如果显示如下，说明已经分配过主从了，不用在分配
<img width="865" height="160" alt="image" src="https://github.com/user-attachments/assets/ac39d009-c57a-41f7-9425-d5894ec712fc" />

然后会有3个主节点和3个从节点。之后输入yes
<img width="865" height="445" alt="image" src="https://github.com/user-attachments/assets/9cedaa9a-26c5-4c76-890f-58fbff82cb44" />

显示ok即可
<img width="865" height="445" alt="image" src="https://github.com/user-attachments/assets/80e443c7-5de5-4973-8bcf-ca4c0656f9d2" />


3.启动nginx
在nginx-1.24.0里面目录双击启动nginx程序（会闪一下）
<img width="865" height="533" alt="image" src="https://github.com/user-attachments/assets/128e665c-12f1-4db9-a38b-8ace2c0734e4" />

可在任务管理器查看是否在运行中
<img width="865" height="659" alt="image" src="https://github.com/user-attachments/assets/a60bae99-4bcd-4219-90f6-6719470aa7cb" />

（可选）4.启动一个redis客户端
在cmd里面输入（-c是以集群方式启动，方面后面查看与编辑关键词时不会提示在另一个端口的客户端）：redis-cli -p 7000 -c
启动一个端口为7000的客户端
<img width="865" height="138" alt="image" src="https://github.com/user-attachments/assets/8009e9f4-a2f5-4c0d-b1a7-e43b1cc518b5" />

5.启动项目
因为需要服务器轮换负载，所以解压两个文件hotsearch和hotsearch-copy后，并用idea打开，打开后两个项目同时启动。
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/7e9a37e2-d9ec-463b-af2d-c1a49e5b8145" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/36d2e21e-de03-43d5-83f4-d9b13095d7cf" />

6.展示页面与增删改查
在浏览器输入：localhost/index.html，会显示主页面。
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/a4b6fac7-bfc0-48db-8909-cc64d3fdfbdc" />

增加功能：投票框随便输入关键词，点击投票，对应的关键词热度就会+1
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/883c9d5e-1bbd-4594-b6b4-70e4a1526bcf" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/c2d73afa-3689-4dd0-8f1b-2b194399efad" />
 
并且redis客户端也会对应进行变化
<img width="865" height="445" alt="image" src="https://github.com/user-attachments/assets/751ab083-24a2-4ccf-9830-5dc85616234f" />

删除功能：点击查看完整排行，进入后每个关键词后面都有删除按钮，点击后会问你是否删除，点击确定后关键词会被删除
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/5f77e9a7-dd31-45b2-9db3-bef5663ec126" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/65f4fb32-e283-4b77-a1a9-cd5862e180c7" />

redis客户端也会对应进行变化，关键词c++已经删除。
<img width="865" height="358" alt="image" src="https://github.com/user-attachments/assets/72bbbff3-6247-4b6b-aefa-e5cbe1c03788" />

查询功能：在搜索框输入关键词，会进行模糊查找，查找结果会直接显示在下面，搜索框为空时，默认显示所有。
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/760d3c12-b8a9-4036-bf07-2984ea91c792" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/734b78ac-e825-4655-914c-7249a8f74968" />

修改功能：每个关键词后面都有修改按钮，点击后会进如修改页面
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/feea25e5-2651-4389-b1ad-47217ebd578f" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/0c4203d3-41e0-44b7-b0bb-390717054770" />
 
里面可以修改关键词对应的热度值
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/9ccf9504-1e16-4f0d-b6bd-7d7559844457" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/ec7a2670-25f9-4d01-b7ba-382843b5200a" />
 
redis客户端也会对应进行变化，这里redis的热度成功修改成了160
<img width="865" height="445" alt="image" src="https://github.com/user-attachments/assets/af821fc8-8ada-4077-9d4d-568b05449145" />

Ps：本项目实现了轮换负载
这时候在项目hotsearch
<img width="736" height="391" alt="image" src="https://github.com/user-attachments/assets/183720c7-4ae4-4fb1-aa04-cfc7e7a86c4b" />
<img width="757" height="402" alt="image" src="https://github.com/user-attachments/assets/144d3458-f256-4780-9d0a-64ccfb81743b" />

这时候在hotsearch-copy
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/84ee7208-9ec1-4666-a3ca-ae03bf6fcaff" />
<img width="865" height="460" alt="image" src="https://github.com/user-attachments/assets/ec32e258-c479-4502-98f4-b94bc7008c3d" />
