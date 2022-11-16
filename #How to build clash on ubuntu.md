# How to build clash on ubuntu
## OS
Ubuntu 20.04
## Clash
xxxxxxxxxx import numpy as npimport matplotlib.pyplot as plt# 数据准备x=np.linspace(0,10,num=30)y=np.sin(x)z=np.cos(x) # 设置画布大小plt.figure(figsize=(6, 3)) # plot 画x与y和x与z的关系图plt.plot(x,y,label='sin(x)',color='red', linewidth=1,marker='o',markersize=3)plt.plot(x,z,label='cos(x)',color='blue',linewidth=1,linestyle='dashed',marker='<',markersize=3) # 设置x轴标签、坐标轴范围，坐标轴刻度，坐标轴刻度旋转角度plt.xlabel('x')plt.xlim(0,10)plt.xticks([0,5,7,10],rotation=50) # # 设置y轴标签、坐标轴范围，坐标轴刻度，坐标轴刻度旋转角度plt.ylabel('y')plt.ylim(-1,1)plt.yticks([-1,-0.5,0,1],rotation=0) #plt.title('Exaple of plt.plot') # 紧凑布局：自动调整图形、坐标轴、标签之间的距离，对于多个子图时尤其有用。plt.tight_layout() # 设置显示图例，要在plt.plot 时设置 label='xxx'才能显示图例plt.legend() # 保存图像，可以是任意后缀名，dpi设置图像清晰度plt.savefig('./fig1.pdf', dpi=600)  #要放在plt.show()之前，否作保存的图像为空白 # 显示图像plt.show()python
> Decompress
>>1. gunzip clash-linux-amd64-v1.7.1.gz
>>2. mv clash-linux-amd64-v1.7.1 clash
>>3. mkdir ~/Clash
>>4. mv clash ~/Clash
>>5. cd ~/Clash

> Configuring
>>6. wget -O config.yaml [订阅链接]
>>7. wget -O Country.mmdb https://www.sub-speeder.com/client-download/Country.mmdb
>>8. chmod +x clash 
>>9. ./clash -d .

>>http://clash.razord.top/#/proxies
>>10. open config.yaml
>>11. compare and change
    external-controller: '127.0.0.1:9090'
    secret: '123456'
>>12. 打开系统设置，选择网络，点击网络代理右边的 ⚙ 按钮，选择手动，填写 HTTP 和 HTTPS 代理为 127.0.0.1:7890，填写 Socks 主机为 127.0.0.1:7891，即可启用系统代理。
>>13. the website above: change the setting as the same to the system

> 开机自启动
>>14. 创建service文件
        sudo touch /etc/systemd/system/clash.service
>>15. 编辑service文件 
        sudo vi /etc/systemd/system/clash.service 
>>16. 编辑如下文本： 
[Unit] 
Description=clash daemon  
[Service] 
Type=simple 
User=root 
ExecStart=/home/luo/Clash/clash -d /home/luo/Clash/ 
Restart=on-failure  
[Install] 
WantedBy=multi-user.target
>>17. sudo systemctl daemon-reload 
sudo systemctl enable clash 
sudo systemctl start clash 
sudo systemctl status clash
>>18. #成功为如下： 
clash.service - clash daemon      
  Loaded: loaded (/etc/systemd/system/clash.service; enabled; vendor preset: enabled)      
  Active: active (running) since Sat 2021-11-06 00:16:45 CST; 5s ago    
 Main PID: 6848 (clash)       
  Tasks: 8 (limit: 14171)      
  Memory: 3.2M      
  CGroup: /system.slice/clash.service              
      └─6848 /home/username/下载/Clash/clash -d /home/username/下载/Clash/  
11月 06 00:16:45 ym-X550JX systemd[1]: Started clash daemon. 
11月 06 00:16:45 ym-X550JX clash[6848]: time="2021-11-06T00:16:45+08:00" level=info msg="Start > 11月 06 00:16:45 ym-X550JX clash[6848]: time="2021-11-06T00:16:45+08:00" level=info msg="Start > 
11月 06 00:16:45 ym-X550JX clash[6848]: time="2021-11-06T00:16:45+08:00" level=info msg="Start > 
11月 06 00:16:45 ym-X550JX clash[6848]: time="2021-11-06T00:16:45+08:00" ...
...............