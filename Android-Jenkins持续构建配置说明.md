## Jenkins持续构建配置说明

### 一、Jenkins安装

1. 登录jenkins官网，下载jenkins-2.89-4版本，如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/1.png)

2. 将压缩包解压，然后启动jenkins.msi程序进行安装，按照提示一直next即可。期间填写账户密码项根据提供的路径打开文件即可查看密码，输入密码后，点击Continue。如

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/2.png)

3. 插件安装有Install suggestd plugins和Select plugins to install两个选项，建议选择第二个自定义下载插件，需要下载的插件包括Android Lint Plugin、Gradle Plugin、Git server Plugin。
如图
 
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/3.png)

4. 安装完成后根据提示创建第一个用户名。如图
 
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/4.png)
5. 安装完毕，开始使用。

***

### 二、Jenkins配置

1. 进入Jenkins主界面，点击系统管理，然后点击右侧系统配置。找到全局属性，勾选环境变量，键值对列表，键输入：ANDROID_HOME,值输入：sdk路径，然后保存。如图
 
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/5.png)

2. 主界面点击系统管理，然后点击右侧全局工具配置，此界面配置jdk、git和gradle，如图
 
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/6.png)
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/7.png)
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/8.png)
 
 
上图中git路径为github下cmd文件夹中的git.exe文件（本文中路径为: C:\Users\hp\AppData\Local\GitHub\PortableGit_f02737a78695063deace08e96d5042710d3e32db\cmd\git.exe）。也可下载Git文件，填写cmd文件夹中的git.exe文件。
配置完毕后点击Save保存。

3、主界面点击创建一个新任务，填写任务名称，选项中选择构建一个自由风格的软件项目，然后点击确定按钮。如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/9.png)

4. 点击项目中左侧栏中的配置按钮。如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/10.png)

5. 添加项目描述（可选项），点选GitHub project，Project url处填写项目在github中的clone地址。如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/11.png)
 

6. 源码管理选项中点选git，Repositories中RepositoryURL填写github的clone地址，Credentials添加一个凭证，github的账号密码。如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/12.png)

7. 构建触发器，点选Poll SCM，此项配置构建时间。我的设置为H/60 * * * *，为每小时构建一次。如图：

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/13.png)

8. 构建，点击增加构建步骤，选择Invoke Gradle script。
点选Invoke Gradle，Gradle Version选择gradle。Tasks中填写 clean build。然后点击高级，如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/14.png)
点击高级后，点选Pass all job parameters as Project properties以及Force GRADLE_USER_HOME to use workspace。Root Build script处输入${workspace}/android。如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/15.png)

9. 构建后操作，点击增加构建后操作步骤，选择Archive the artifacts。
用于存档的文件输入：**/*.apk
点开高级，点选Archive artifacts only if build is successful。如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/16.png)
配置完毕后点击保存。

10. 点击立即构建，如图

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/17.png)
如图为进度提示

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/18.png)
上图中的球带进度条的表示正在构建，点击球可查看控制台打印的log
蓝色球：代表构建成功的
灰色球：代表没有构建完成被取消构建的
红色球：代表构建失败的

11. 构建完成后点击查看结果，如图：

 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/19.png)
 ![image](https://github.com/tian321go/IMG/raw/master/jenkins/20.png)
 
构建完成后生产apk所在目录为：D:\Program Files (x86)\Jenkins\jobs\ax_android\lastSuccessful\archive\android\app\build\outputs\apk\release
