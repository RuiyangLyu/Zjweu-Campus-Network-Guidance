# 浙江水利水电学院南浔校区校园网路由器设置不靠谱指北

**本文最新编辑时间：2022/9/19**



联系邮箱:lyuruiyang@outlook.com



本文设置参考自：https://jakting.com/archives/drcom-autologin-padavan-tgbot.html

**本文同样采用 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh) 协议进行许可，在您遵循此协议的情况下，可以自由共享与演绎本文章。**

**禁止借此盈利**



## 1.首先你需要确定的事情

- **确定你的路由器可以刷固件 确定你的路由器可以刷固件 确定你的路由器可以刷固件** 重要的事情说三遍
- 建议使用openwrt或者hiboy版的老毛子固件[本人使用hiboy版老毛子固件]
- 如果你尚未接触过路由器刷固件请勿轻易尝试



## 2.以下示例均使用老毛子固件（其他固件请谨慎参考）

1. ### 进入校园网登录界面

   http://10.10.10.126/

   输入你的校园网账号（学号）密码（身份证后六位）并选择运营商

   **然后 不 要 点 登录**

2. ### 首先需要获取curl指令

   请打开F12-完成步骤①②之后点击登录按钮

   在登录完后找到③

   ![](https://raw.githubusercontent.com/RuiyangLyu/PicInventory/master/image-20220919094100374.png)

   

   选择复制-以curl（bash）格式复制

   

   ![](https://raw.githubusercontent.com/RuiyangLyu/PicInventory/master/20220919100357.png)

   你将会得到一个类似于这个的curl链接

   ```bash
   curl 'http://10.10.10.126:801/eportal/portal/login?callback=dr1003&login_method=1&user_account=%2C0%2C你的用户名%40运营商标示&user_password=你的密码&wlan_user_ip=10.192.217.127&wlan_user_ipv6=&wlan_user_mac=000000000000&wlan_ac_ip=&wlan_ac_name=&jsVersion=4.1.3&terminal_type=1&lang=zh-cn&v=6246&lang=zh' \
     -H 'Accept: */*' \
     -H 'Accept-Language: zh,zh-CN;q=0.9,zh-TW;q=0.8,en;q=0.7' \
     -H 'Connection: keep-alive' \
     -H 'Referer: http://10.10.10.126/' \
     -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36' \
     --compressed \
     --insecure
   ```

   先对这个curl链接进行处理，为避免上级路由分配ip地址冲突
   请自己决定是否修改`wlan_user_ip=10.192.217.127`为

   `wlan_user_ip=` （此处删除ip地址即可）

   <!--需要了解更多关于curl指令的信息请自行查阅-->

   （理论上可以把链接精简到 `http://10.10.10.126:801/eportal/portal/login?callback=dr1003&login_method=1&user_account=%2C0%2C你的用户名%40运营商标示&user_password=你的密码` ）

   

3. ### 关于如何使用这个curl进行登录

   1. #### 进入路由器管理界面：http://192.168.123.1 

      默认登录名/密码均为 admin

      在 高级设置-系统管理-终端服务 处选择:

      **启用 SSH 服务:是** 

   2. #### 选择一个符合你使用习惯的终端ssh到路由

      ```bash
      ssh admin@192.168.123.1
      admin@192.168.123.1's password:<输入登录密码，这里的密码是不会显示出来的，输入完成回车>
      
      登录成功后你应该看到：
      BusyBox v1.29.3 (2022-03-03 20:24:39 CST) built-in shell (ash)
      Enter 'help' for a list of built-in commands.
      
      [RM2100 3.4.3.9-099_22-04-1]# 2022-09-19 10:28:36
      [RM2100 /home/root]#
      ```

   3. #### 执行以下指令

      **此处设置完全引用于：https://jakting.com/archives/drcom-autologin-padavan-tgbot.html**

      ```shell
      cd /etc/storage
      vi drcom_cron.sh
      ```

      `vi`后的内容可以随你定义，毕竟什么名字随你。**接着按下`{i}`会进入编辑模式。**

      

      #### 注意：如果你从未接触过vi，请花几分钟去了解一下基本操作

      

      选择合适的复制粘贴方式

      如果是`Xshell`的话右键会有粘贴的选项，如果是`putty`的话右键会直接粘贴，cmd用户请使用ctrl+shift+v粘贴，随意使用你觉得方便的方法。

      

      无论如何，根据你使用的软件把内容粘贴进去即可。

      

      按下 ESC 退出编辑模式，直接键盘输入`{:wq}`后按下回车保存（注意，此处有一个英文冒号）

      再执行以下指令用于获得执行权限：

      ```bash
      chmod +x drcom_cron.sh
      ```

      现在，你的路由器数据文件中应该包含我们需要执行的东西了。

      你可以试试退出上网登陆窗登录，然后执行指令，看看是否处于登录状态了。

      ```bash
      ./drcom_cron.sh
      ```

      如果没有，有可能是哪个步骤出错了，请给我发邮件或者issue。

      无论如何，如果你成功登录了，接着看吧。

      

   4. #### 使用计划任务保证处于登录状态

      ### 将 sh 文件保存防止重启丢失

      进入：高级设置 → 系统管理 → 恢复/导出/上传设置 → 路由器内部存储[脚本文件] (/etc/storage) → **点击「\*保存 /etc/storage/ 内容到闪存\*」的「提交」按钮**。

      虽然不知道这步是否必要，但是用了总没错。

      [![img](https://jakting.com/wp-content/uploads/2019/08/2019082613485868.png)](https://jakting.com/wp-content/uploads/2019/08/2019082613485868.png)

      ### 路由器重启后执行登录操作

      进入：高级设置 → 自定义设置 → 脚本 → （以下两处）

      **「在路由器启动后执行」**，**「在 WAN 上行/下行启动后执行」**在代码末尾添加：

      ```bash
      /etc/storage/drcom_cron.sh
      ```

      保存。

      

   5. #### 鉴于我校晚上断网断电，计划任务只需要定时检测登录情况即可

      进入：高级设置 → 系统管理 → 服务 → 其他服务 → 保持「Cron守护程序 (计划任务)」处于开启状态

      然后在「计划任务 (Crontab)」中输入：

      ```bash
      #每周一到周日的每天6点到23点的每60分钟，执行一次检测登录指令
      */60 6-23 * * 1-7 /etc/storage/drcom_cron.sh
      ```

      此处设置请根据需要自行修改

## 到这里就完成了

这个教程面向对象应当是有对路由器固件了解的同学，后续若有更多人需要更详细的教程，可能在我有空的时候再整一篇（？），但是理当是要咕咕咕的，主要是没给这个登录脚本写输出到logs的信息以便于判断是否处于登录成功/失败，因为用下来感觉好像没啥问题（主要是因为我懒 [又不是不能用.jpeg]），真的有需要的朋友可以自己写写看。

