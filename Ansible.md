# 自动化运维

## 介绍

1. 随着信息时代的持续发展，IT运维已经成为IT服务内涵中重要的组成部分。面对越来越复杂的业务，面对越来越多样化的用户需求，不断扩展的IT应用，需要越来越合理的模式来保障IT服务能灵活便捷、安全稳定地持续保障，这种模式中的保障因素就是IT运维（其他因素是更加优越的IT架构等）。从初期的几台服务器发展到庞大的数据中心，单靠人工已经无法满足在技术、业务、管理等方面的要求，那么标准化、自动化、架构优化、过程优化等降低IT服务成本的因素越来越被人们所重视。其中，自动化最开始作为代替人工操作为出发点的诉求被广泛研究和应用。
2. IT运维从诞生发展至今，自动化作为其重要属性之一，已经不仅仅只是代替人工操作，更重要的是深层探知和全局分析，关注的是在当前条件下如何实现性能与服务最优化，同时保障投资收益最大化。自动化对IT运维的影响，已经不仅仅是人与设备之间的关系，已经发展到了面向客户服务、驱动IT运维决策的层面，IT运维团队的构成，也从各级技术人员占大多数发展到业务人员甚至用户占大多数的局面。
3. 因此，IT运维自动化是一组将静态的设备结构转化为根据IT服务需求动态弹性响应的策略，目的就是实现IT运维的质量，降低成本。可以说自动化一定是IT运维最高层面的重要属性之一，但不是全部。

## 常用工具

1. Puppet（www.puppetlabs.com）是早期的Linux自动化运维工具，是一种LINUX、WINDOWS、UNIX平台的集中配置管理系统，到现在已经非常成熟，可以批量管理远程服务器，模块丰富、配置复杂、基于Ruby语言编写。是最典型的C/S结构，需要安装服务端和客户端。

   Puppet采用C/S星状的结构，所有的客户端和一个或者多个服务器交互，每个客户端周期地（默认半个小时）向服务器发送请求，获得最新的配置信息，保证和配置信息同步。

   每个Puppet客户端周期地连接一次服务器，下载最新的配置文件，并且严格按照配置文件来配置客户端。配置完成后，Puppet客户端可以反馈给服务器端一个消息，如果出错也会给服务器端反馈一个消息。

   Puppet适用于服务器管理的整个过程，比如初始安装、配置、更新等。

2. SaltStack（官网 https://saltstack.com  文档 docs.saltstack.com）和Puppet一样，也是C/S模式，需要安装服务端和客户端，基于Python编写，加入了MQ消息同步，可以使执行命令和执行结果高效返回，但其执行过程需要等待客户端全部返回，如果客户端没有及时返回或者没有响应的话，可能会导致部分机器没有执行结果。

3. **Ansible**（www.ansible.com）

4. Ansible基于Python开发的，Ansible不需要在客户端服务器上安装客户端。因为Ansible基于SSH远程管理，而Linux服务器大部分都离不开SSH，所以Ansible不需要为配置添加额外的支持。

   Ansible安装使用都很简单，而且基于上千个插件和模块，实现各种软件、平台、版本的管理，支持虚拟容器多层级的部署。



## Ansible相关配置文件

### 配置文件

```sh
# ansible主配置文件，配置ansible工作特性
/etc/ansible/ansible.cfg
# 主机清单文件
/etc/ansible/hosts
# 角色存放目录
/etc/ansible/roles
```

### 主配置文件

```sh
# Ansible的配置文件可以存放在多个不同地方，优先级从高到低
ANSIBLE_CONFIG            # 环境变量
./ansible.cfg             # 当前目录下的ansible.cfg
~/ansible.cfg             # 当前用户家目录下的asible.cfg
/etc/ansible/ansibe.cfg   # 系统默认配置文件


```

配置文件解析

```sh
[defaults]
#inventory = /etc/ansible/hosts               # 主机列表配置文件
#library = /usr/share/my_modules/             # 库文件存放目录
#remote_tmp = ~/.ansible/tmp                  # 临时py命令文件存放在远程主机的目录路径
#local_tmp = ~/.ansible/tmp                   # 本机的临时命令执行目录
#forks = 5                                    # 默认并发数
#sudo_user = root                             # 默认sudo用户
#ask_sudo_pass = True                         # 每次执行ansible命令是否询问SSH密码
#ask_pass = True
#remote_port = 22                             # 默认ssh远程端口
#roles_path = /etc/ansible/roles              # 默认角色存放路径
#host_key_checking = False                    # 检查对应服务器的host_key，推荐取消注释，实现第一次连接自动信任目标主机
#log_path = /var/log/ansible.log              # ansible执行日志文件，推荐开启
#module_name = command                        # 默认使用的模块，可以修改为
shell等
#private_key_file = /path/to/file             # 配置远程ssh私钥存放路径
[privilege_escalation]                        # 普通用户提权配置
#become=True
#become_method=sudo
#become_user=root
#become_ask_pass=False



# 注意：
1. 绝大多数配置项无需修改
```



### inventory主机配置清单

1. ansible主要功能用在批量主机操作，为了便携的使用其中部分主机，可以在inventory_file中将其分组命令
2. 默认inventory_file为**/etc/ansible/hosts**
3. inventory_file可以有多个，也可以通过Dynamic Inventory动态生成
4. 建议生成环境中，在每个项目的目录下创建独立的hosts文件

配置文件解析

```sh
ansible_ssh_host             # 将要连接的远程主机名，与想要设定的主机别名不同时，可以通过此变量设置
ansible_ssh_port             # 远程主机ssh端口，如果不是默认端口号，可以通过此变量设置，如：ip:port(xxx.xxx.xxx.xxx:2222)
ansible_ssh_user             # 默认ssh用户名
ansible_ssh_pass             # ssh密码(不推荐，此方法不安全，可以使用命令行 -ask-pass 参数或 ssh密钥形式)
ansible_sudo_pass            # sudo密码(不推荐, 建议使用命令行 -asksudo-pass 参数)
ansible_sudo_exe             # sudo命令路径(适用于1.8及以上版本)
ansible_connection           # 与主机连接类型，如: local,ssh 或paramiko.
ansible_ssh_provate_key_file # ssh使用的私钥，适用于多个密钥
ansible_shell_type           # 目标系统shell类型，默认使用 sh ，可以设置为 csh 或 fish
ansible_python_interpreter   # 目标主机python路径，适用情况：系统中存在多个python环境，或路径不是/usr/bin/python情况
```

示例

```sh
192.168.0.1
[webservers]
192.168.0.10:2222
192.168.0.11
[dbservers]
192.168.0.21
192.168.0.22
192.168.0.23
# [dbservers]
# 192.168.0.[21:23]




# 组嵌套
[webservers]
192.168.0.10:2222
192.168.0.11
[dbservers]
192.168.0.[21:23]
[appservers]
192.168.0.[100:200]
# 定义testsrvs组中包含两个其他分组，实现组嵌套
[testsrvs:children]
webservers
dbservers


# 参数定义
[test]
192.168.0.20 ansible_connection=local # 指定本地连接，无需ssh配置，ansible_connection=ssh 需要StrictHostKeyChecking no
192.168.0.30 ansible_connection=ssh ansible_ssh_port=2222 ansible_ssh_user=admin ansible_ssh_password=admin123
# 显示别名
[websrvs]
web01 ansible_ssh_host=192.168.0.31 ansible_ssh_password=123456
web02 ansible_ssh_host=192.168.0.32
[websrvs:vars]
ansible_ssh_password=admin123
some_host ansible_ssh_port=2222 ansible_ssh_user=admin
aws_host ansible_ssh_private_key_file=/path/to/file
freebsd_host ansible_python_interpreter=/path/to/file
```



### Ansible相关工具

```sh
# 主程序，命令行执行工具
/usr/bin/ansible
# 查看配置文档，模块功能查看工具，相当于man
/usr/bin/ansible-doc
# 定制自动化任务，编排剧本工具，相当于脚本
/usr/bin/ansible-playbook
# 文件加密工具
/usr/bin/ansible-vault
# 基于Console界面与用户交互的执行工具
/usr/bin/ansible-console
```



# Ansible

## 介绍

1. Ansible不需要安装客户端，需要sshd去通信
2. Ansible基于模块工作，模块可以由任何语言开发1
3. Ansible不仅支持命令行使用模块，也支持编写yaml格式的playbook，易于编写和阅读
4. Ansible安装十分简单，可直接yum安装
5. Ansible有提供UI(浏览器图形化)www.ansible.com/tower,收费的官方文档 http://docs.ansible.com/ansible/latest/index.html
6. Ansible已经被RedHat公司收购，它在Github（https://github.com/ansible/ansible）上是一个非常受欢迎的开源软件，一本不错的入门电子书 https://ansible-book.gitbooks.io/ansible-first-book/ 





## Ansible安装

1. 关闭所有的防火墙规则

2. 修改主机名

   ```sh
   [root@192 ~]# hostnamectl set-hostname ansible1
   [root@192 ~]# bash
   
   [root@192 ~]# hostnamectl set-hostname ansible2
   [root@192 ~]# bash
   ```

3. 建立互信（本机也需配置免密）

   ```sh
   [root@ansible1 ~]# ssh-keygen
   [root@ansible1 ~]# ssh-copy-id 192.168.6.100
   [root@ansible1 ~]# ssh-copy-id 192.168.6.11
   
   
   
   # 利用sshpass批量实现基于key验证脚本
   Host *
   	GSSAPIAuthentication yes
   	ForwardX11Trusted yes
   	SendEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
   	SendEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
   	SendEnv LC_IDENTIFICATION LC_ALL LANGUAGE
   	SendEnv XMODIFIERS
   	StrictHostKeyChecking no
   $ vim hosts.list
   192.168.0.21
   192.168.0.22
   
   $ vim push_ssh_key.sh
   #!/bin/bash
   rpm -q sshpass &> /dev/null || yum install -y sshpass
   [ -f /root/.ssh/id_rsa ] || ssh-keygen -f /root/.ssh/id_rsa -P ''
   
   export SSHPASS=admin123
   while read IP;do
   	sshpass -e ssh-copy-id -o StrictHostKeyChecking=no $IP
   done < hosts.list
   
   
   
   
   # 实现基于key验证的脚本
   $ vim ssh_key.sh
   #!/bin/bash
   IPLIST="
   192.168.0.21
   192.168.0.22
   192.168.0.23
   192.168.0.24
   192.168.0.25
   192.168.0.26"
   
   rpm -q sshpass &> /dev/null || yum install -y sshpass
   [ -f /root/.ssh/id_rsa ] || ssh-keygen -f /root/.ssh/id_rsa -P ''
   
   export SSHPASS=admin123
   for IP in $IPLIST;do
   	{sshpass -e ssh-copy-id -o StrictHostKeyChecking=no $IP;} &
   done
   wait
   ```

4. 修改域名文件

   ```sh
   [root@ansible1 ~]# cat /etc/hosts
   127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
   ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
   192.168.6.11 ansible1
   192.168.6.100 ansible2
   
   # 传输
   [root@ansible1 ~]# scp /etc/hosts ansible2:/etc/hosts 
   ```

5. 安装ansible

   ```sh
   [root@ansible1 ~]# yum install -y epel-release
   [root@ansible1 ~]# yum install -y ansible
   ```

6. 配置主机组

   ```sh
   [root@ansible1 ~]# vim /etc/ansible/hosts
   # 最后一行添加
   [testhost]
   192.168.6.11
   192.168.6.100
   ```






## Ansible-doc帮助工具

```sh
# 此工具用来显示模块的帮助信息
格式：
# ansible-doc [option] [module...]
-l,--list     # 列出所有可用模块
-s,--snippet  # 显示指定模块的playbook片段



# 列出所有模块
ansible-doc -l
# 查看指定模块帮助
ansible-doc ping
ansible-doc -s ping

# 查看指定插件
ansible-doc -t connection -l
ansible-doc -t lookup -l
```





## Ansible主执行工具

```sh
# 此工具为ansible ad-hoc的主要执行工具
格式：
# ansible <host-pattern> [-m module_name] [-a args]
选项说明：
--version                           # 显示版本
-m module 							# 指定使用的模块名称
-list-hosts 						# 显示主机列表，可简写为 -list
-v 									# 显示详细的执行过程， -vv -vvv 可显示的更加详细
-C, -check 							# 检查，不具体执行
-T, -timeout=TIMEOUT 				# 执行超时时间，默认10s
-k, -ask-pass 						# 提示输入ssh密码
-u, -user=REMOTE_USER 				# 远程执行的用户，默认为root
-b, -become 						# 代替旧版sudo切换
-become-user=USERNAME 				# 指定sudo的用户，默认为root
-K, -ask-become-pass 				# 提示输入sudo时口令
-f FORKS, -forks FORKS 				# 指定并发同时执行ansible任务的主机数
```



## Host pattern

```sh
# 用于匹配被控制的主机列表

# ALL: 表示匹配所有主机
ansible all -m ping

# *: 通配符
ansible "*" -m ping
ansible 192.168.0.* -m ping
ansible "srvs" -m ping
ansible "192.168.0.21 192.168.0.22" -m ping

# 或
ansible "websrvs:appsrvs" -m ping
ansible "192.168.0.21:192.168.0.22" -m ping

# 与
# 既在websrvs组中，又在dbsrvs组中的主机
ansible "websrvs:&dbsrvs" -m ping

# 非
# 在websrvs组中，并且不再dbsrvs组中的主机
# 注意：此处需要使用单引号
ansible 'websrvs:!dbsrvs' -m ping

# 综合
ansible 'websrvs:dbsrvs:&appsrvs:!ftpsrvs' -m ping

# 正则表达式
ansible "websrvs:dbsrvs" -m ping
ansible "~(web|app).*" -m ping
```



## 执行状态

```sh
# grep -A 14 '\[colors\]' /etc/ansible/ansible.cfg
[colors]
#highlight = white
#verbose = blue
#warn = bright purple
#error = red
#debug = dark gray
#deprecate = purple
#skip = cyan
#unreachable = red
#ok = green
#changed = yellow
#diff_add = green
#diff_remove = red
#diff_lines = cyan


绿色： 执行成功并且不需要做改变的操作
黄色：执行成功并且对目标主机进行变更操作
红色：执行失败

```



## Ansible-playbook剧本工具

### 简介

1. playbook是ansible用于配置，部署，和管理被节点的剧本
2. 通过playbook的详细描述，执行其中的一些列tasks，可以让远端的主机达到预期的状态。playbook就像ansible控制器给被控节点列出的一系列to-do-list，而且被控节点必须要完成
3. playbook顾名思义，即剧本，现实生活中演员按照剧本表演，在ansible中，这次**由被控计算机表演，进行安装，部署应用，提供对外的服务等，以及组织计算机处理各种各样的事情**



### 数据格式

1. **XML**：Extensible Markup Language，可扩展标记语言，可用于数据交换和配置
2. **JSON**：JavaScript Object Notation，JavaScript对象标记法，主要用来数据交换或 配置，不支持注释
3. **YAML**：YAML Ain't Markup Language，YAML不是一种标记语言，主要用来配 置，大小写敏感，不支持tab
4. 这些格式可以使用工具相互转换，参考网站（https: /www.json2yaml.com/      、 https: /www.bejson.com/json/json2yaml/）



### Playbook核心组件

1. Hosts：执行的远程主机列表
2. Tasks：任务及，由多个task远程组成的列表，每个task都是一个字典，一个完整的代码块功能需最少元素包括name和task，一个name只能有一个task
3. Variables：内置变量或自定义变量在playbook中调用
4. Templates：模板，可替换模板文件中的变量并实现一些简单逻辑文件
5. Handlers（hosts下）和notify（tasks下）是和tasks结合使用，由特定条件触发的操作，满足条件放可执行
6. Tags：标签，指定某条任务执行，用于选择playbook中的部分代码，ansible 具有幂等性，因此会自动跳过没有变化的部分，即便如此，有些代码为测试器 确实没有发生变化的时候，依然会非常的长，此时，如果确信其没有变化，就 可以通过tags跳过这些代码片段



#### Hosts组件

1. playbook中的每个play的目的都是为了让特定主机以某个指定用户身份执行任务
2. hosts主要用于指定要执行的任务主机，需事先定义在主机清单中
3. 示例

   ```sh
   - hosts: websrvs
   - hosts: 192.168.223.200
   - hosts: websrvs:appsrvs  # 两个组的并集
   - hosts: websrvs:&appsrvs # 两个组的交集
   - hosts: websrvs:!appsrvs # 在websrvs组中，但不在appsrvs中
   ```



#### remote_user组件

1. 可用于host和task中，也可以通过指定其通过sudo方式在远程主机上执行任务，其可用于play全局或某个任务中

2. 可以在sudo时使用sudo_user指定sudo时切换的用户，默认为root

3. 示例

   ```yaml
   ---
   - hosts: forhost
     remote_user: root
     tasks:
     - name: test connection
       ping:
       remote_user: root
       become: yes
       become_user: root
   
   
   
   
   
   # 注意
   1. 2.9版本sudo已经替换为become，之前的版本都是sudo：、sudo_user：
   ```





#### task列表和action组件

1. play主体部分是task list，task list有一个或多个task，每个task按次序逐个在hosts 中指定的所有主机上执行，即在所有主机上执行完一个task后，再执行下一个 task

2. task的目的是使用指定的参数执行模块，再模块参数中可以使用变量。模块执行 为幂等的，意味着多次执行是安全的，其结果一致

3. 每个task都应该有其name，用于playbook执行结果输出，建议其内容能够清晰描 述任务执行步骤，如果没有提供name，则action的结果将用于输出

4. task两种格式

   ```sh
   action: module arguments    # action: shell wall hello
   module: arguments           # shell: wall hello
   ```

5. 示例1：

   ```yaml
   ---
   - hosts: websrvs
     remote_user: root
     gather_facts: no # 不收集系统信息，提高执行效率
     tasks:
     - name: test network connection
       ping:
     - name: excute command
       command: wall "hello world!"
   ```

6. 示例2：

   ```yaml
   ---
   - hosts: websrvs
     remote_user: root
     gather_facts: no
     tasks:
     - name: install httpd
       yum: name=httpd
     - name: start httpd
       service: name=httpd state=started enabled=yes
   ```

   

#### tags和handlers组件

1. 某任务状态在运行后为changed时，可通过notify通知给相应的handlers任务

2. 可以通过tags给task打标签，在ansible-playbook命令上**使用-t指定调用**

3. 示例：

   ```yaml
   - hosts: httpd
     remote_user: root
     gather_facts: no
     tasks:
     - name: install httpd
       yum: name=httpd
     - name: copy config file to httpd server
       copy: src=/etc/httpd/conf/httpd.conf
       dest=/etc/httpd/conf/httpd.conf
       notify: restart service
     tags:
       - change config
     - name: start httpd
       service: name=httpd state=started enabled=yes
       tags:
         - start service
     handlers:
     - name: restart service
       service: name=httpd state=restarted
   ```





### Playbook命令

```sh
ansible-playbook <被执行yml文件名> ...[选项]
常见选项：
--syntax-check                    语法检查，可缩写为--syntax，相当于bash -n
-C, --check                       模拟执行，只检测可能会发生的变化，但不真正执行操作，dry run
--list-hosts                      列出运行任务的主机
--list-tags                       列出tag
--list-tasks                      列出task
--limit                           主机列表 只针对主机列表中的特定主机执行
-i INVENTORY                      指定主机清单文件，通常一个项对应一个主机清单文件
--start-at-task START_AT_TASK     从指定的task开始执行任务，而不是从开头开始，START_AT_TASK 为任务名称
-v, -vv, -vvv                     显示具体执行过程，详细程度根据-v数量变化
```



## Template模板

### 简介

1. 模板是一个文本文件，可以作为生成文件的模板，并且模板文件中还可以嵌套jinja2语法

### jinja2语法

jinja2是一个现代的，设计者友好的，仿照django模板的python模板语言，速度快，被广泛使用，并且提供了可选的沙箱模板执行环境以保证安全

#### 特性

```sh
1. 沙箱中执行
2. 强大的HTML自动转义系统保护系统免受XSS
3. 模板继承
4. 及时编译最优的python代码
5. 可选提前编译模板的时间
6. 易于调试，异常的行数直接指向模板中的对应行·
7. 可配置的语法
```

#### 官方地址

```sh
https: /jinja.palletsprojects.com/en/3.0.x/

# 中文文档
https: /www.w3cschool.cn/yshfid/
```

#### 数据类型和操作

```sh
字面量：如字符串，使用单引号或双引号；数字，整数，浮点数
列表：[item1, item2, ...]
元组：(item1, item2, ...)
字典：{key1:value1, key2:value2, ...}
布尔型：true/false
算术运算：+、-、*、/、//、%、**
比较操作符：==、!=、>、<、>=、<=
逻辑运算：and、or、not
流表达式：for、if、when
```









# Ansible基础模块测试

### Command模块

```sh
（远程执行命令行命令）
[root@ansible1 ~]# ansible testhost -m command -a "hostname"
192.168.6.11 | CHANGED | rc=0 >>
ansible1
192.168.6.100 | CHANGED | rc=0 >>
ansible2
```

### Copy模块

```sh
（拷贝本机文件或目录到目标主机）
# 使用copy模块拷贝本机目录下的/etc/passwd到192.168.6.100主机上的
[root@ansible1 ~]# ansible 192.168.6.100 -m copy -a "src=/etc/passwd dest=/tmp/123"
192.168.6.100 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "checksum": "c8dad90bd71ae3f015d7bfc561d81b7db8feb059", 
    "dest": "/tmp/123", 
    "gid": 0, 
    "group": "root", 
    "md5sum": "8f95a1f97be811466b187732311d8924", 
    "mode": "0644", 
    "owner": "root", 
    "size": 936, 
    "src": "/root/.ansible/tmp/ansible-tmp-1683600950.06-3407-32096548076796/source", 
    "state": "file", 
    "uid": 0
}



# 在测试机上查看
[root@ansible2 tmp]# cat 123 
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
fdy:x:1000:1000::/home/fdy:/bin/bash
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
```

### Shell模块

```sh
（远程执行脚本）
# 先写一个脚本
[root@ansible1 tmp]# cat test.sh 
#!/bin/bash
echo `date` > /tmp/ansible_test.txt
# 然后将脚本分发下去
[root@ansible1 tmp]# ansible testhost -m copy -a "src=/tmp/test.sh dest=/tmp/test.sh mode=0755"



# 远程执行脚本
[root@ansible1 tmp]# ansible testhost -m shell -a "/tmp/test.sh"
192.168.6.11 | CHANGED | rc=0 >>

192.168.6.100 | CHANGED | rc=0 >>


# 在测试机上查看
[root@ansible2 tmp]# cat ansible_test.txt
#!/bin/bash
echo `date` > /tmp/ansible_test.txt
```

### Cron模块

```sh
（任务计划）
# 使用ansible的cron模块创建一个名为test cron的cron模块，并在每周六执行test.sh
[root@ansible1 tmp]# ansible testhost -m cron -a "name='test cron' job='/bin/bash/tmp/test.sh'weekday=6 state=present"
192.168.6.100 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "envs": [], 
    "jobs": [
        "test cron"
    ]
}
192.168.6.11 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "envs": [], 
    "jobs": [
        "test cron"
    ]
}




# 删除cron模块
[root@ansible1 tmp]# ansible testhost -m cron -a "name='test cron' state=absent"
192.168.6.11 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "envs": [], 
    "jobs": []
}
192.168.6.100 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "envs": [], 
    "jobs": []
}
```

### yum模块

```sh
# 安装软件包
# state=present表示软件包的状态，这里表示安装
ansible websrvs -m yum -a 'name=httpd state=present'

# 启用epel源安装软件包
ansible websrvs -m yum -a 'name=nginx state=present enablerepo=epel'
# 升级除kernel和foo开头外的所有包
ansible websrvs -m yum -a 'name=* state=lastest exclude=kernel*,foo*'
# 删除(卸载)
ansible websrvs -m yum -a 'name=httpd state=absent'
# 查看包
ansible websrvs -m yum -a 'list=tree




（安装apache服务）
[root@ansible1 tmp]# ansible testhost -m yum -a "name=httpd"
192.168.6.100 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "changes": {
        "installed": [
            "httpd"
        ]
    }, 
    "msg": "", 
    "rc": 0, 
    "results": [
        "Loaded plugins: fastestmirror\nLoading mirror speeds from cached hostfile\n * base: mirrors.aliyun.com\n * extras: mirrors.bfsu.edu.cn\n * updates: mirrors.bfsu.edu.cn\nResolving Dependencies\n--> Running transaction check\n---> Package httpd.x86_64 0:2.4.6-98.el7.centos.7 will be installed\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package      Arch          Version                        Repository      Size\n================================================================================\nInstalling:\n httpd        x86_64        2.4.6-98.el7.centos.7          updates        2.7 M\n\nTransaction Summary\n================================================================================\nInstall  1 Package\n\nTotal download size: 2.7 M\nInstalled size: 9.4 M\nDownloading packages:\nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Installing : httpd-2.4.6-98.el7.centos.7.x86_64                           1/1 \n  Verifying  : httpd-2.4.6-98.el7.centos.7.x86_64                           1/1 \n\nInstalled:\n  httpd.x86_64 0:2.4.6-98.el7.centos.7                                          \n\nComplete!\n"
    ]
}
192.168.6.11 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "changes": {
        "installed": [
            "httpd"
        ]
    }, 
    "msg": "", 
    "rc": 0, 
    "results": [
        "Loaded plugins: fastestmirror\nLoading mirror speeds from cached hostfile\n * base: mirrors.aliyun.com\n * epel: mirrors.bfsu.edu.cn\n * extras: mirrors.aliyun.com\n * updates: mirrors.aliyun.com\nResolving Dependencies\n--> Running transaction check\n---> Package httpd.x86_64 0:2.4.6-98.el7.centos.7 will be installed\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package      Arch          Version                        Repository      Size\n================================================================================\nInstalling:\n httpd        x86_64        2.4.6-98.el7.centos.7          updates        2.7 M\n\nTransaction Summary\n================================================================================\nInstall  1 Package\n\nTotal download size: 2.7 M\nInstalled size: 9.4 M\nDownloading packages:\nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Installing : httpd-2.4.6-98.el7.centos.7.x86_64                           1/1 \n  Verifying  : httpd-2.4.6-98.el7.centos.7.x86_64                           1/1 \n\nInstalled:\n  httpd.x86_64 0:2.4.6-98.el7.centos.7                                          \n\nComplete!\n"
    ]
}



# 测试
[root@ansible2 tmp]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)

May 09 09:48:17 ansible2 systemd[1]: Starting The Apache HTTP Server...
May 09 09:48:17 ansible2 httpd[12588]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 192.168.6.100...s message
May 09 09:48:17 ansible2 systemd[1]: Started The Apache HTTP Server.
May 09 14:24:10 ansible2 systemd[1]: Stopping The Apache HTTP Server...
May 09 14:24:11 ansible2 systemd[1]: Stopped The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.

[root@ansible1 tmp]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)

May 09 09:48:17 ansible1 systemd[1]: Starting The Apache HTTP Server...
May 09 09:48:17 ansible1 httpd[3208]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 192.168.6.11. ...s message
May 09 09:48:17 ansible1 systemd[1]: Started The Apache HTTP Server.
May 09 14:24:16 ansible1 systemd[1]: Stopping The Apache HTTP Server...
May 09 14:24:17 ansible1 systemd[1]: Stopped The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
```

### Service模块

```sh
# 启动服务，并设置开机启动
ansible all -m service -a 'name=httpd state=started enabled=yes'
# 停止服务
ansible all -m service -a 'name=httpd state=stopped'
# 重载服务
ansible all -m service -a 'name=httpd state=reloaded'
# 重启服务
ansible all -m service -a 'name=httpd state=restarted'








（开启httpd服务，并设置自启）
[root@ansible1 tmp]# ansible testhost -m service -a "name=httpd state=started enabled=yes"
192.168.6.100 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": true, 
    "enabled": true, 
    "name": "httpd", 
    "state": "started", 
    "status": {
 ...
    }
}
192.168.6.11 | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
 ...
    }
}



# 测试
[root@ansible2 tmp]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2023-05-09 14:27:40 CST; 1min 17s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 13530 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─13530 /usr/sbin/httpd -DFOREGROUND
           ├─13532 /usr/sbin/httpd -DFOREGROUND
           ├─13533 /usr/sbin/httpd -DFOREGROUND
           ├─13534 /usr/sbin/httpd -DFOREGROUND
           ├─13535 /usr/sbin/httpd -DFOREGROUND
           └─13536 /usr/sbin/httpd -DFOREGROUND

May 09 14:27:40 ansible2 systemd[1]: Starting The Apache HTTP Server...
May 09 14:27:40 ansible2 httpd[13530]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 192.168.6.100...s message
May 09 14:27:40 ansible2 systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.

[root@ansible1 tmp]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2023-05-09 14:27:40 CST; 1min 30s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 4185 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─4185 /usr/sbin/httpd -DFOREGROUND
           ├─4186 /usr/sbin/httpd -DFOREGROUND
           ├─4187 /usr/sbin/httpd -DFOREGROUND
           ├─4190 /usr/sbin/httpd -DFOREGROUND
           ├─4191 /usr/sbin/httpd -DFOREGROUND
           └─4192 /usr/sbin/httpd -DFOREGROUND

May 09 14:27:40 ansible1 systemd[1]: Starting The Apache HTTP Server...
May 09 14:27:40 ansible1 httpd[4185]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 192.168.6.11. ...s message
May 09 14:27:40 ansible1 systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.


# 查看是否开启自启
[root@ansible1 tmp]# systemctl is-enabled httpd
enabled
[root@ansible2 tmp]# systemctl is-enabled httpd
enabled

```

### Script模块

1.  功能：再远程主机上运行ansible服务器上的脚本（无需执行权限）
2. 与shell模块不同的是，shell模块需要目标主机上有脚本才能执行，而scripts模块只需要在在本地写一个脚本，就可以在远程服务器上执行

```sh
（在远程主机上运行本地脚本）


# 执行
[root@ansible1 tmp]# ansible testhost -m script -a /tmp/test.sh 
192.168.6.100 | CHANGED => {
    "changed": true, 
    "rc": 0, 
    "stderr": "Shared connection to 192.168.6.100 closed.\r\n", 
    "stderr_lines": [
        "Shared connection to 192.168.6.100 closed."
    ], 
    "stdout": "", 
    "stdout_lines": []
}
192.168.6.11 | CHANGED => {
    "changed": true, 
    "rc": 0, 
    "stderr": "Shared connection to 192.168.6.11 closed.\r\n", 
    "stderr_lines": [
        "Shared connection to 192.168.6.11 closed."
    ], 
    "stdout": "", 
    "stdout_lines": []
}

# 在测试机上查看
[root@ansible2 tmp]# cat ansible_test.txt 
Wed May 10 16:18:48 CST 2023

```

### Fetch模块

```sh
（从远程主机提取文件到ansible服务器）

# 执行
# 将目标主机/root/1.sh文件提取到本机的/data/scripts/目标主机ip下
# /data/scripts会自动创建
[root@ansible1 ~]# ansible 192.168.6.100 -m fetch -a "src=/root/1.sh dest=/data/scripts"
192.168.6.100 | CHANGED => {
    "changed": true, 
    "checksum": "7357709b5c7dc0059cafb8af2665aa00f46f3740", 
    "dest": "/data/scripts/192.168.6.100/root/1.sh", 
    "md5sum": "aec5fb6ec5a916da1a9f8db5e8c9ad3a", 
    "remote_checksum": "7357709b5c7dc0059cafb8af2665aa00f46f3740", 
    "remote_md5sum": null
}


# 测试
[root@ansible1 root]# ls
1.sh
[root@ansible1 root]# pwd
/data/scripts/192.168.6.100/root
[root@ansible1 root]# cat 1.sh 
#!/bin/bash
rm -f /etc/localtime && ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
yum list
yum install -y vim net-tools
yum remove -y NetworkManager firewalld
yum install -y iptables-services
iptables -F
iptables -X
iptables -Z
service iptables save
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
sleep 3
shutdown now
```

### File模块

```sh
# 该模块主要用于设置文件的属性，比如创建文件、创建链接文件、删除文件等。
# 下面是一些常见的命令：
force　　  # 需要在两种情况下强制创建软链接，一种是源文件不存在，但之后会建立的情况下；另一种是目标软链接已存在，需要先取消之前的软链，然后创建新的软链，有两个选项：yes|no
group　　  # 定义文件/目录的属组。后面可以加上mode：定义文件/目录的权限
owner　　  # 定义文件/目录的属主。后面必须跟上path：定义文件/目录的路径
recurse　　# 递归设置文件的属性，只对目录有效，后面跟上src：被链接的源文件路径，只应用于state=link的情况
dest　　   # 被链接到的路径，只应用于state=link的情况
state　  　# 状态，有以下选项：
  directory：如果目录不存在，就创建目录
  file：即使文件不存在，也不会被创建
  link：创建软链接
  hard：创建硬链接
  touch：如果文件不存在，则会创建一个新的文件，如果文件或目录已存在，则更新其最后修改时间
  absent：删除目录、文件或者取消链接文件







# 提前创建/data目录，file模块不支持自动创建目录
# all参数代表在/etc/ansible/hosts下的所有主机组

# 创建空文件
ansible all -m file a 'path=/data/test.txt state=touch'
# 删除空文件
ansible all -m file -a 'path=/data/test.txt state=absent'
# 为目标文件，赋予755权限并设置所属者为admin(目标主机必须有admin用户和文件test.txt)
ansible 192.168.6.100 -m file -a 'path=/data/test.txt owner=admin mode=755'


# 创建目录
ansible all -m file -a 'path=/data/mysql state=directory'

# 创建软链接
ansible all -m file -a 'src=/data/testfile dest=/data/testfile-link state=link'

# 递归修改目录属性，但不递归至子目录
ansible 192.168.6.100 -m file -a 'path=/data/mysql/2222/ state=directory owner=mysql group=mysql'
# 递归修改目录属性并递归至子目录
ansible 192.168.6.100 -m file -a 'path=/data/mysql/2222/ state=directory owner=mysql group=mysql recurse=yes'
```

### User模块

```sh
# 创建用户
ansible all -m user -a 'name=user1 comment="test user" uid=2000 home=/app/user1 group=root'

ansible all -m user -a 'name=nginx comment=nginx uid=1000 group=nginx groups="root,daemon" shell=/sbin/nologin system=yes create_home=no home=/data/nginx non_unique=yes'

# 删除用户及其家目录
ansible all -m user -a 'name=nginx state=absent remove=yes'
```

### Group模块

```sh
# 创建组
ansible websrvs -m group -a 'name=nginx gid=1000 system=yes'
# 删除组
ansible websrvs -m group -a 'name=nginx state=absent
```

### Setup模块

功能：

1. setup模块用来收集主机系统信息，这些facts信息可以直接以变量形式使 用，
2. 但如果主机较多，会影响执行速度，可以使用gather_facts: no禁止ansible收集信息

```sh
ansible all -m setup
ansible all -m setup -a "filter=ansible_nodename"
ansible all -m setup -a "filter=ansible_hostname"
ansible all -m setup -a "filter=ansible_domain"
ansible all -m setup -a "filter=ansible_memtotal_mb"
ansible all -m setup -a "filter=ansible_memory_mb"
ansible all -m setup -a "filter=ansible_memfree_mb"
ansible all -m setup -a "filter=ansible_os_family"
ansible all -m setup -a "filter=ansible_distribution_major_version"
ansible all -m setup -a "filter=ansible_distribution_version"
ansible all -m setup -a "filter=ansible_processor_vcpus"
# 获取所有IP地址
ansible all -m setup -a "filter=ansible_all_ipv4_addtrsses"
ansible all -m setup -a "filter=ansible_architecture"
ansible all -m setup -a "filter=ansible_uptime_seconds"
ansible all -m setup -a "filter=ansible_processor*"
ansible all -m setup -a "filter=ansible_env"
```



# Ansible playbook测试

## **创建文件**

```yaml
# 创建yml文件
[root@ansible1 ~]# cat /etc/ansible/test.yml 
---
- hosts: 192.168.6.100
  remote_user: root
  tasks:
  - name: test_playbook
    shell: touch /tmp/playbook_test.txt
    
    
    
    
# 参数详解
1. host参数指定了对哪些主机进程操作，如果多态主机可用逗号作为分隔，也可以使用主机组，主机组在/etc/ansible/hosts里定义
2. remote_user参数指定使用什么用户登录远程主机操作
3. tasks参数指定路由一个任务
4. tasks参数下的name是对任务的描述，在执行过程中会打印出来
5. tasks参数下的shell是一个模块，指定使用shell模块去执行touch命令







# 执行
[root@ansible1 ~]# ansible-playbook /etc/ansible/test.yml

PLAY [192.168.6.100] ***********************************************************

TASK [Gathering Facts] *********************************************************
ok: [192.168.6.100]

TASK [test_playbook] ***********************************************************
[WARNING]: Consider using the file module with state=touch rather than running
'touch'.  If you need to use command because file is insufficient you can add
'warn: false' to this command task or set 'command_warnings=False' in
ansible.cfg to get rid of this message.
changed: [192.168.6.100]

PLAY RECAP *********************************************************************
192.168.6.100              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 

# 测试
[root@ansible2 tmp]# ll
total 16
-rw-r--r--  1 root root 936 May  9 10:55 123
-rw-r--r--  1 root root  28 May  9 14:08 ansible_test.txt
-rwx------. 1 root root 836 May  8 12:00 ks-script-Oqh8jU
-rw-r--r--  1 root root   0 May 10 08:28 playbook_test.txt
drwx------  3 root root  17 May 10 08:13 systemd-private-dabeb86043344ce39378b87cfbb16d4a-chronyd.service-zprDZH
drwx------  3 root root  17 May 10 08:13 systemd-private-dabeb86043344ce39378b87cfbb16d4a-httpd.service-sJwku6
-rwxr-xr-x  1 root root  48 May  9 09:24 test.sh
drwx------  2 root root   6 May  8 12:10 vmware-root_639-3988031840
drwx------  2 root root   6 May  9 08:59 vmware-root_647-3988163046
drwx------  2 root root   6 May  8 12:11 vmware-root_648-2688619569
drwx------  2 root root   6 May  8 12:09 vmware-root_652-2697074100
drwx------  2 root root   6 May 10 08:13 vmware-root_657-4022112241
drwx------. 2 root root   6 May  8 12:05 vmware-root_660-2697467306
drwx------. 2 root root   6 May  8 12:01 vmware-root_674-2731152261
-rw-------. 1 root root   0 May  8 11:56 yum.log
```

## **创建用户**

```yml
# 创建yml文件
[root@ansible1 ~]# cat  create_user.yml 
---
- name: create_user
  hosts: 192.168.6.100
  user: root
  gather_facts: false
  vars:
    - user: "test"
  tasks:
    - name: create user
      user: name="{{ user }}"




参数详解：
1. name参数是对plaaybook实现的功能做一个概述，可省略
2. gather_facts参数指定了在以下任务部分执行前，是否先执行setup模块来获取主机相关信息，在这后面的task会使用到setup获取的信息时使用到
3. vars参数指定了变量，这里指一个user变量，变量值为test
4. tasks参数下的name说明此任务名为create user
5. tasks参数下的user指定创建一个名为user变量(test)的用户


# 执行
[root@ansible1 ~]# ansible-playbook create_user.yml 

PLAY [create_user] *************************************************************

TASK [create user] *************************************************************
changed: [192.168.6.100]

PLAY RECAP *********************************************************************
192.168.6.100              : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

# 测试
[root@ansible2 tmp]# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
test:x:1000:1000::/home/test:/bin/bash


# 创建mysql用户
[root@ansible1 ~]# cat 2.yml 
---
- hosts: ansible2
  remote_user: root
  gather_facts: no
  tasks:
  - {name: create group, group: name=mysql system=yes gid=1002}
  - name: create user
    user: name=mysql system=yes uid=1002 shell=/sbin/nologin group=mysql home=/data/mysql create_home=no

```

## 循环

```sh
# 先在testhost主机组中的所有主机创建/tmp/1.txt /tmp/2.txt /tmp/3.txt三个文件
# 创建yml文件
[root@ansible1 ~]# cat while.yml 
---
- hosts: testhost
  user: root
  tasks:
  - name: change mode for files
    file: path=/tmp/{{ item }} mode=600
    with_items:
      - 1.txt
      - 2.txt
      - 3.txt




# 执行
[root@ansible1 ~]# ansible-playbook while.yml

PLAY [testhost] *********************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.11]
ok: [192.168.6.100]

TASK [change mode for files] ********************************************************************************************************************************
changed: [192.168.6.100] => (item=1.txt)
ok: [192.168.6.11] => (item=1.txt)
changed: [192.168.6.100] => (item=2.txt)
ok: [192.168.6.11] => (item=2.txt)
changed: [192.168.6.100] => (item=3.txt)
ok: [192.168.6.11] => (item=3.txt)

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
192.168.6.11               : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

## 条件判断

```sh
# 创建yml文件
[root@ansible1 tmp]# cat /root/when.yml 
---
- hosts: testhost
  user: root
  gather_facts: True
  tasks:
    - name: use when
      shell: touch /tmp/when.txt
      when: ansible_ens33.ipv4.address == "192.168.6.100"


# 参数详解
这是一个 Ansible playbook 文件，其含义是：

- `hosts`: 指定要操作的主机或主机组名。在这里，指定要操作的主机为 `testhost`。
- `user`: 指定连接主机的用户名。在这里，指定连接的用户为 `root`。
- `gather_facts`: 指定是否在任务之前收集主机信息。在这里，设置为 `True`，表示在任务之前将会收集主机信息。
- `tasks`: 指定具体要执行的任务。在这里，指定执行一个名为 `use when` 的任务。
- `name`: 指定任务的名称。在这里，设置任务名称为 `use when`。
- `shell`: 指定要执行的命令。在这里，执行 `touch /tmp/when.txt` 命令，用于创建一个名为 `when.txt` 的文件。
- `when`: 指定任务的条件。在这里，当主机的 `ansible_ens33` 网卡的 IPv4 地址为 `192.168.6.100` 时，才会执行该任务。

# 执行
[root@ansible1 tmp]# ansible-playbook /root/when.yml

PLAY [testhost] *********************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]
ok: [192.168.6.11]

TASK [use when] *********************************************************************************************************************************************
skipping: [192.168.6.11]
[WARNING]: Consider using the file module with state=touch rather than running 'touch'.  If you need to use command because file is insufficient you can add
'warn: false' to this command task or set 'command_warnings=False' in ansible.cfg to get rid of this message.
changed: [192.168.6.100]

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
192.168.6.11               : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   



# 测试
[root@ansible2 tmp]# ll when.txt 
-rw-r--r-- 1 root root 0 May 10 09:54 when.txt
```

## handlers

执行task之后，服务器发生变化之后要执行的一些操作，比如我们修改了配置文件后，需要重启一些服务

```sh
# 创建yml文件
[root@ansible1 ~]# cat handlers.yml 
---
- name: handlers test
  hosts: 192.168.6.100
  user: root
  tasks:
    - name: copy file
      copy: src=/etc/passwd dest=/tmp/aaa.txt
      notify: test handlers
  handlers:
    - name: test handlers
      shell: echo "111" >> /tmp/aaa.txt

# 参数详解
1. 只有copy模块执行后，才会调用下面的handlers操作，比较适合配hi文件发生更改后，重启服务的操作
2. handlers参数，指明在tasks的操作完成之后才执行的操作



# 执行
[root@ansible1 ~]# ansible-playbook handlers.yml

PLAY [handlers test] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [copy file] ********************************************************************************************************************************************
changed: [192.168.6.100]

RUNNING HANDLER [test handlers] *****************************************************************************************************************************
changed: [192.168.6.100]

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 


# 测试
[root@ansible2 tmp]# cat aaa.txt 
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
fdy:x:1000:1000::/home/fdy:/bin/bash
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
111
```

## notify

```yaml
# notify和handlers的区别
1. 执行时机不同：notify参数是在某些任务执行成功后才会触发，而handlers参数则是在整个playbook运行完毕后才会被执行
2. 使用方式不同：notify参数实在任务中使用changed_when或者failed_when来条件化触发


# 这里的notify参数在service模块执行成功并触发了状态变更（changed）后，触发了名为“reload nginx”的操作。
 - name: restart nginx
   service:
     name: nginx
     state: restarted
   notify: reload nginx

 handlers:
   - name: reload nginx
     service:
       name: nginx
       state: reloaded


# 这里的handlers参数定义了一个名为“restart apache”的操作，该操作会在整个Playbook运行结束时调用。
 handlers:
   - name: restart apache
     service:
       name: apache2
       state: restarted

# 总的来说，notify参数适用于只有特定情况下才需要执行的任务，而handlers参数适用于所有任务都完成后需要执行的操作。
```

## 安装nginx

```yaml
---
- hosts: nginx
  remote_user: root
  gather_facts: no
  tasks:
  - name: add group nginx
    group: name=nginx state=present
  - name: add user nginx
    user: name=nginx state=present group=nginx
  - name: Install Nginx
    yum: name=nginx state=present
  - name: web page
    copy: src=files/index.html dest=/usr/share/nginx/html/index.html
  - name: Start Nginx
    service: name=nginx state=started enabled=yes
  - name: wait nginx started
    wait_for: port=80 state=started delay=10
  - name: curl http
    uri: url="http: /localhost" return_content=yes
    register: curl
  - name: echo http content
    debug: msg={{ curl.content }}
```

## 忽略错误

```yaml
# 如果一个task出错，默认将不会继续执行后续其他task
# 利用

---
- hosts: websrvs
  tasks:
  - name: error
    command: /bin/false
    ignore_errors: yes
  - name: continue
    command: wall continue
    
    
    
# 执行
[root@ansible1 ~]# ansible-playbook cuowu.yml

PLAY [192.168.6.100] ************************************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************************************
ok: [192.168.6.100]

TASK [error] ********************************************************************************************************************************************************************************
fatal: [192.168.6.100]: FAILED! => {"changed": true, "cmd": ["/bin/false"], "delta": "0:00:00.003943", "end": "2023-05-16 08:32:32.588081", "msg": "non-zero return code", "rc": 1, "start": "2023-05-16 08:32:32.584138", "stderr": "", "stderr_lines": [], "stdout": "", "stdout_lines": []}
...ignoring

TASK [continue] *****************************************************************************************************************************************************************************
changed: [192.168.6.100]

PLAY RECAP **********************************************************************************************************************************************************************************
192.168.6.100              : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=1   
```

## 变量

```sh
# 变量名：仅能由字母，数字和下划线组成，且只能以字母开头
# 变量定义：variable=value variable:value
# 变量调用
  通过{{ variable_name }} 调用变量，切变量名前后加空格
# 变量来源
  1. ansible的setup facts远程主机的所有变量都可以直接调用
  2. 通过命令行指定变量，优先级最高
     ansible-playbook -e varname=value test.yml
  3. 在playbook文件中定义
     vars:
       var1: value1
       var2: value2
  4. 在独立的yaml文件中定义
     - hosts: all
       vars_files:
         - vars_yml
  5. 在主机清单中定义
     主机（普通）变量：主机组中主机单独定义，优先级高于公共变量
     组（公共）变量：针对主机组中所有主机定义统一变量
  6. 在项目中针对主机和主机组定义
     在项目目录中创建host_vars和group_vars目录
  7. 在role中定义
  
  
# 变量优先级
-e 选项定义变量 -> playbook中vars_files -> playbook中vars变量定义 --> host_vars/主机名文件 -> 主机清单中主机变量 -> group_vars/all文件 --> 主机清单组变量

```

### 命令行定义变量

```sh
# 编写yml文件
[root@ansible1 ~]# cat var2.yml 
---
- hosts: 192.168.6.100
  remote_user: root
  tasks:
  - name: install package
    yum: name={{ package }} state=present


# 执行
[root@ansible1 ~]# ansible-playbook -e package=httpd var2.yml

PLAY [192.168.6.100] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [install package] **************************************************************************************************************************************
changed: [192.168.6.100]

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

### playbook文件内定义变量

```sh
# 编写yml文件
[root@ansible1 ~]# cat var1.yml 
---
- hosts: 192.168.6.100
  remote_user: root
  vars:
    username: fdy
    groupname: fdy

  tasks:
  - name: create group
    group: name={{ groupname }} state=present
  - name: create user
    user: name={{ username }} group={{ groupname }} state=present



# 执行
[root@ansible1 ~]# ansible-playbook var1.yml

PLAY [192.168.6.100] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [create group] *****************************************************************************************************************************************
changed: [192.168.6.100]

TASK [create user] ******************************************************************************************************************************************
changed: [192.168.6.100]

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   




# 测试
[root@ansible2 home]# ll
total 0
drwx------ 2 admin admin 62 May 10 17:02 admin
drwx------ 2 fdy   fdy   62 May 16 09:06 fdy
drwx------ 2 mysql mysql 62 May 10 17:33 mysql
drwx------ 2 test  test  62 May 10 08:40 test





# 变量相互调用
[root@ansible1 ~]# cat var3.yml 
---
- hosts: 192.168.6.100
  remote_user: root
  vars:
    subfix: "txt"
    file: "{{ ansible_nodename }}.{{ subfix }}"
  tasks:
  - name: test var
    file: path="/data/{{ file }}" state=touch

```

### 在独立的yaml文件中定义

```sh
# 创建yml文件
[root@ansible1 ~]# cat vars1.yml
---
- hosts: 192.168.6.100
  remote_user: root
  vars_files:
    - vars.yml
  tasks:
  - name: install package
    yum: name={{ package_name }} state=present
    tags: install
  - name: start service
    service: name={{ service_name }} state=started enabled=yes

# 创建独立的变量文件
[root@ansible1 ~]# cat vars.yml
---
package_name: mariadb-server
service_name: mariadb


# 执行
[root@ansible1 ~]# ansible-playbook vars1.yml

PLAY [192.168.6.100] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [install package] **************************************************************************************************************************************
changed: [192.168.6.100]

TASK [start service] ****************************************************************************************************************************************
changed: [192.168.6.100]

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

### 针对主机和主机组的变量

```sh
# 添加主机清单文件
[root@ansible1 ~]# vim /etc/ansible/hosts 
[testhost]
192.168.6.100
[websrvs]
ansible1 http_port=80 maxRequestsPerChild=808
ansible2 http_prot=8080 maxRequestsPerChild=900

# 创建yml执行文件
[root@ansible1 ~]# cat web1.yml 
---
- hosts: websrvs
  remote_user: root
  tasks:
  - name: Install Apache
    yum:
      name: httpd
      state: present
  - name: Start apache service
    service:
      name: httpd
      state: started
      enabled: yes
      
      
# 主机组变量
[root@ansible1 ~]# vim /etc/ansible/hosts
[testhost]
192.168.6.100
[websrvs:vars]
http_port=80
ntp_server=ntp.aliyun.com
nfs_server=nfs.aliyun.com


[root@ansible1 ~]# cat web2.yml
---
- hosts: websrvs
  remote_user: root
  tasks:
    - name: Configure NTP service
      template:
        src: /path/to/ntp.conf.j2
        dest: /etc/ntp.conf
      vars:
        ntp_server: "{{ ntp_server }}"
```

### register注册变量

#### msg相关参数

```sh
# 输出register注册的name变量全部信息
# msg: "{{ name.cmd }}" # 显示命令
# msg: "{{ name.rc }}" # 显示命令成功与否
# msg: "{{ name.stdout }}" # 显示命令的输出结果为字符串形式
# msg: "{{ name.stdout_lines }}" # 显示命令的输出结果为列表形式
# msg: "{{ name.stdout_lines[0] }}" # 显示命令的输出结果的列表中第一个元素
# msg: "{{ name[stdout_lines] }}" # 显示命令的输出结果为列表形式
```

#### 临时捕获变量

```sh
# 创建yml文件
[root@ansible1 ~]# cat register.yml 
---
- hosts: 192.168.6.100
  tasks:
  - name: get variable
    shell: hostname
    register: name  # 捕获上方的结果信息并填入name变量
  - name: print variable
    debug:
      msg: "{{ name }}"  # 输出register捕获的name变量的全部信息

# 执行
[root@ansible1 ~]# ansible-playbook register.yml 

PLAY [192.168.6.100] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [get variable] *****************************************************************************************************************************************
changed: [192.168.6.100]

TASK [print variable] ***************************************************************************************************************************************
ok: [192.168.6.100] => {
    "msg": {
        "changed": true, 
        "cmd": "hostname", 
        "delta": "0:00:00.003020", 
        "end": "2023-05-16 10:20:25.096642", 
        "failed": false, 
        "rc": 0, 
        "start": "2023-05-16 10:20:25.093622", 
        "stderr": "", 
        "stderr_lines": [], 
        "stdout": "ansible2", 
        "stdout_lines": [
            "ansible2"
        ]
    }
}

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### 使用注册变量创建文件

```yaml
# 创建yml文件
[root@ansible1 ~]# cat register1.yml 
- hosts: 192.168.6.100
  tasks:
  - name: get variable
    shell: hostname
    register: name
  - name: create file
    file: dest=/tmp/{{ name.stdout }}.log state=touch


# 执行
[root@ansible1 ~]# ansible-playbook register1.yml 

PLAY [192.168.6.100] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [get variable] *****************************************************************************************************************************************
changed: [192.168.6.100]

TASK [create file] ******************************************************************************************************************************************
changed: [192.168.6.100]

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### 启动服务并检查

```yaml
# 创建yml文件
[root@ansible1 ~]# cat register2.yml 
---
- hosts: 192.168.6.100
  vars:
    package_name: httpd
    service_name: httpd
  tasks:
  - name: install {{ package_name }}
    yum: name={{ package_name }}
  - name: start {{ service_name }}
    service: name={{ service_name }} state=started enabled=yes
  - name: check
    shell: ps aux | grep {{ service_name }} | grep -v grep
    register: check_service
  - name: debug
    debug:
      msg: "{{ check_service.stdout_lines }}"







# 执行
[root@ansible1 ~]# ansible-playbook register2.yml 

PLAY [192.168.6.100] ****************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [192.168.6.100]

TASK [install httpd] ****************************************************************************************************************************************
changed: [192.168.6.100]

TASK [start httpd] ******************************************************************************************************************************************
changed: [192.168.6.100]

TASK [check] ************************************************************************************************************************************************
changed: [192.168.6.100]

TASK [debug] ************************************************************************************************************************************************
ok: [192.168.6.100] => {
    "msg": [
        "root       6280  0.0  0.5 224084  5040 ?        Ss   12:57   0:00 /usr/sbin/httpd -DFOREGROUND", 
        "apache     6281  0.0  0.2 224084  2932 ?        S    12:57   0:00 /usr/sbin/httpd -DFOREGROUND", 
        "apache     6282  0.0  0.2 224084  2932 ?        S    12:57   0:00 /usr/sbin/httpd -DFOREGROUND", 
        "apache     6283  0.0  0.2 224084  2932 ?        S    12:57   0:00 /usr/sbin/httpd -DFOREGROUND", 
        "apache     6285  0.0  0.2 224084  2932 ?        S    12:57   0:00 /usr/sbin/httpd -DFOREGROUND", 
        "apache     6286  0.0  0.2 224084  2932 ?        S    12:57   0:00 /usr/sbin/httpd -DFOREGROUND"
    ]
}

PLAY RECAP **************************************************************************************************************************************************
192.168.6.100              : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

