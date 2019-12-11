## gitlab-ci入门

### 一、.gitlab-ci.yml介绍

> **.gitlab-ci.yml**放在项目的**根目录**，当push代码到gitlab上时，会根据此脚本文件的配置执行相应的命令。
>
> 参考官方文档：https://forge.etsi.org/rep/help/ci/yaml/README.md

#### 1.1 常用的关键字

* variables 

  配置变量供多个job使用，相当于class中的变量而且是**final**类型的变量，是无法在**job**的**script**重新赋值的

  ```yaml
  variables:
    url: "final"
  
  stages:
    - test
    - build
  
  job1:
    stage: test
    tags:
      - shared-146-tag
    script:
      - url="job1"
      - echo 11111111$url
  job2:
    stage: build
    tags:
      - shared-146-tag
    script:
      - echo 22222$url
      - url="job2"
      - echo 33333$url
  ```

  打印结果为：

  ```yaml
  11111111job1
  22222final
  33333job2
  ```

* cache

  缓存后续job执行过程中需要用到的文件列表，不然当新job执行时会删除所有文件后再执行脚本

  ```yaml
  cache:
    paths:
      - "./a.txt"
  
  stages:
    - test
    - build
  
  job1:
    stage: test
    tags:
      - shared-146-tag
    script:
      - echo "test" >> "./a.txt"
  job2:
    stage: build
    tags:
      - shared-146-tag
    script:
      - cat "./a.txt"
  ```

  打印结果为：`test`

  当不使用cache时：

  ```yaml
  #cache:
  #  paths:
  #    - "./a.txt"
  
  stages:
    - test
    - build
  
  job1:
    stage: test
    tags:
      - shared-146-tag
    script:
      - echo "test" >> "./a.txt"
  job2:
    stage: build
    tags:
      - shared-146-tag
    script:
      - cat "./a.txt"
  ```

  打印结果为：`cat: ./a.txt: No such file or directory`

* before_script

  在job中使用时，是在job的script执行之前执行该方法中命令；在非job中使用，表示在**每一个**job执行之前执行该方法中的命令。

  ```yaml
  stages:
    - test
  
  before_script:
    - echo 'before job1'
  
  job1:
    stage: test
    tags:
      - shared-146-tag
    script:
      - echo "job1"
  ```

  打印结果：

  ```yaml
  before job1
  job1
  ```

* stages

  用于根节点，表示要执行的job的**顺序**，在job中使用**stage**标记，与job的名称无关，**stage**定义的相当于别名。

  ```yaml
  stages:
    - test1
    - test2
  
  job2:
    stage: test2
    tags:
      - shared-146-tag
    script:
      - echo "test2"
  
  job1:
    stage: test1
    tags:
      - shared-146-tag
    script:
      - echo "test1"
  ```

  打印结果为：

  ```yaml
  test1
  test2
  ```

  *说明job定义的顺序和执行的顺序无关，job执行的顺序由**stages**控制*

* after_sctipt

  在job中使用时，表示在job的**script**执行完后，再执行该方法中的命令；在根节点定义时，会在**每一个**job执行完后，再执行该方法中的命令。

  ```yaml
  stages:
    - test1
  
  after_script:
    - echo "after job1"
  
  job1:
    stage: test1
    tags:
      - shared-146-tag
    script:
      - echo "test1"
  ```

  打印结果为：

  ```yaml
  test1
  after job1
  ```

  

#### 1.2 job中常用的关键字

* stage

  表示每个job执行的阶段，相当于别名，对应根节点的**stages**定义的名称

* tags

  用来指定用哪个**runner**来执行命令

* variables

  当前job中使用的变量定义

  ```yaml
  variables:
  	branch_url: $CI_PROJECT_URL/tree/$CI_COMMIT_REF_NAME
  	commit_url: $CI_PROJECT_URL/commit/$CI_COMMIT_SHA
  script:
  	- echo $branch_url
  ```

* script

  要执行的命令，每一个 “-” 都是一条命令

* when

  当前job执行触发的条件，有以下默认值：

  `on_success`: 当前面的`stage`成功时才执行

  `on_failure`: 当前面的`stage`中任意一个job失败时执行

  `always`: 无论前面的`stage`成功或者失败都执行

  `manual`: 需要手动执行

* only

  定义一列需要执行当前job的git分支，或者merge_requests

  ```yaml
  # master分支且merge_requests时
  only:
  	- master
  	- merge_requests
  ```

* expect

  定义一列不需要执行当前job的git分支，或者用merge_requests

#### 1.3 gitlab-ci中已定义的系统变量

`$CI_PROJECT_URL`: 访问项目的HTTP地址

`$CI_COMMIT_REF_NAME`: 构建项目的 branch 或 tag 名称

`$CI_COMMIT_SHA`: 构建项目的 commit SHA 值

`$CI_JOB_ID`: 当前 job 在 GitLab CI 内部的唯一 ID

更多系统变量请参考：https://docs.gitlab.com/ee/ci/variables/predefined_variables.html

应用事例：

Git库的url：`$CI_PROJECT_URL/tree/$CI_COMMIT_REF_NAME`

Git最后一次commit的url：`$CI_PROJECT_URL/commit/$CI_COMMIT_SHA`

gitlab-ci执行job产生的log的url：`$CI_PROJECT_URL/-/jobs/$CI_JOB_ID`

#### 1.4 gitlab-ci常识

* **stages** 默认有`test`、`build`、`deploy`，可以自定义各种名称；**stage**的默认值是`test`

* 相同`stage`的job可以同时执行

* 下一个`stage`的job会在前一个`stage`的job成功后开始执行

* 只有一个job且根节点和job中都定义了`before_script`时，只执行job中的`before_script`；同理`after_script`也是同样的情况

  ```yaml
  before_script:
     - echo "a"
  
  job:
    tags:
      - shared-146-tag
    before_script:
      - echo "a1"
    script:
      - echo "aa"
  ```

  打印结果：

  ```yaml
  a1
  aa
  ```

### 二、场景使用

#### 2.1 push代码到个人库后，执行ci脚本lint和build代码的操作

​	2.1.1 先判断是否安装Android SDK，主要用到`before_script`、`cache`、`variables`

```yaml
variables:
  SDK_ROOT: "./sdk" #定义Android SDK路径
cache:
  paths:
    - ${SDK_ROOT} #缓存Android SDK路径不删除
before_script:
	- if [[ ! -d "${SDK_ROOT}" ]]; then #判断Android SDK文件夹是否存在
	- mkdir "${SDK_ROOT}" #不存在时创建文件夹
	- wget --quiet --output-document=android-sdk.zip https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip  #下载Android SDK
	- unzip -o android-sdk.zip -d ${SDK_ROOT}  #解压文件到对应目录
	- mkdir -p ${SDK_ROOT}/licenses  #创建licenses文件夹
	- echo -e "\n24333f8a63b6825ea9c5514f83c2829b004d1fee" > ${SDK_ROOT}/licenses/android-sdk-license  #license的key加入到文件中，参考自己电脑上SDK上的对应文件
	- echo -e "\n84831b9409646a918e30573bab4c9c91346d8abd" > ${SDK_ROOT}/licenses/android-sdk-preview-license  #license的key加入到文件中，参考自己电脑上SDK上的对应文件
	- fi  # if结束符号
	- export ANDROID_HOME="${SDK_ROOT}"   #创建环境变量
  - export PATH="$PATH:${SDK_ROOT}/platform-tools/" #创建环境变量
  - touch local.properties  #创建Android工程中的SDK配置文件
  - echo "sdk.dir=${ANDROID_HOME}" >> local.properties  #文件中配置sdk路径
  - chmod +x ./gradlew #提升gradlew文件的权限
	
```

​	2.1.2 创建lint和build代码的job，lint失败和build失败时都会发送企业微信提醒

```yaml
lintAndBuildJob:  #名字随便定义
	tags:
    - shared-146-tag
  variables:
    branch_url: $CI_PROJECT_URL/tree/$CI_COMMIT_REF_NAME  #定义提交代码库的url
    commit_url: $CI_PROJECT_URL/commit/$CI_COMMIT_SHA  #定义最后一次commit代码的url
  script:
    - ./gradlew --no-daemon clean lintProdRelease
    - echo "0" > .job_status #lint通过
    - ./gradlew --no-daemon resguardProdRelease
    - echo "1" > .job_status #build通过
  after_script:
    - job_status=$(cat .job_status)
    - if [ $job_status -eq -1 ];then #lint失败
    -   python getcommitlog.py $CI_PROJECT_URL $branch_url $CI_PROJECT_NAME $commit_url $CI_COMMIT_REF_NAME $CI_PROJECT_URL/-/jobs/$CI_JOB_ID "lint失败，请检查后重新提交"
    - elif [ $job_status -eq 0 ];then #build失败
    -   python getcommitlog.py $CI_PROJECT_URL $branch_url $CI_PROJECT_NAME $commit_url $CI_COMMIT_REF_NAME $CI_PROJECT_URL/-/jobs/$CI_JOB_ID "build失败，请检查后重新提交"
    - else #流程走通
    -   python getcommitlog.py $CI_PROJECT_URL $branch_url $CI_PROJECT_NAME $commit_url $CI_COMMIT_REF_NAME $CI_PROJECT_URL/-/jobs/$CI_JOB_ID "提交个人库成功，请及时合并到主库"
    - fi
  except:
  	- merge_requests  #在merge_requests时不执行该job
```

#### 2.2 lint和build失败时发送信息到企业微信

​	2.2.1 从git log中获取最后一次提交者姓名、提交的信息、提交的hashcode（[参考文档]([https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2](https://git-scm.com/book/zh/v2/Git-基础-查看提交历史))）

```python
(os.popen("git log --pretty=format:\"%an-&%h-&%s\" -1 ").read()).split("-&")
```

​	`os.popen`：获取控制台输出的内容

​	`%an`: 作者（author）的名字

​	`%h`: 提交对象的简短哈希字串

​	`%s`: 提交说明

​	2.2.2 构建发送企业微信信息的数据结构

​	参考企业微信API文档：https://work.weixin.qq.com/api/doc#90000/90136/91770

​	2.2.3 python发送企业微信信息

```python
import requests
requests.post(i,headers=headers,data=stringTextMsg)
```

完整python脚本：

```python
import re
import os
import requests
import json
import sys


def getContent():
    #获取提交记录信息
    content = (os.popen("git log --pretty=format:\"%an-&%h-&%s\" -1 ").read()).split("-&")
    return content

mobile_list = {"潘珉":"18701069823","王照鑫":"18513582509","胡申典":"18518097950","田兴龙":"13426012317","刘意":"13717833151"}

def postWinxin(content):
    #print(sys.argv)
    project_url = sys.argv[1]
    branch_url = sys.argv[2]
    project_name = sys.argv[3]
    commit_url = sys.argv[4]
    branch_name = sys.argv[5]
    name,shortcodenum,explain = content
    weixinUrl = ["https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=e0a6f29f-efab-413d-aa15-c15ef6d6d04f"]
    headers = {'Content-Type': 'application/json'}
    stringTextMsg = {
        "msgtype": "markdown",
        "markdown": {
            "content":   "#### "+name+" 推送了["+project_name+"]("+project_url+")"+"的["+branch_name+"]("+branch_url+")分支到个人库\n>"
            +"提交信息：["+explain+"]("+commit_url+")\n>"
            +"["+sys.argv[7]+"]("+sys.argv[6]+")"
        }
    }
    requests.packages.urllib3.disable_warnings()
    stringTextMsg = json.dumps(stringTextMsg)

    mentioned_mobile_list = [mobile_list[name]]
    stringTextMsg2 = {
        "msgtype": "text",
        "text": {
            "content":sys.argv[7],
            "mentioned_mobile_list":mentioned_mobile_list
        }
    }
    stringTextMsg2 = json.dumps(stringTextMsg2)
    print(name,mobile_list[name],sys.argv[7],mentioned_mobile_list)


    for i in weixinUrl:
        requests.post(i,headers=headers,data=stringTextMsg)
        requests.post(i,headers=headers,data=stringTextMsg2)

if __name__ == "__main__":
    content = getContent()
    postWinxin(content)

```



### 三、遇到的问题汇总

* 本机安装的runner不能执行

  gitlab部署在外网的服务器上，runner安装在本地时，由于外网不能连接内网，所以不能使用。

* Java JDK的安装和Android SDK的配置

  由于当前的服务器的runner不在Docker上，所以不能通过**image**标签来安装JDK和SDK，需要像本机上的安装流程一样去安装，下载 --> 解压 --> 配置环境变量，这些操作都需要通过脚本实现。

* CI脚本不能执行的原因

  1. .gitlab-ci.yml文件必须放在根目录上
  2. .gitlab-ci.yml文件中有语法错误，可以在gitlab上的**CI/CD --> 流水线 -->  右上角的`CI配置检查(CI Lint)`**验证脚本是否正确
  3. 查看job的执行条件，比如`only`、`except`等标签的配置

* gradle进程被kill

  默认的gradle启动后会开启一个守护进程，代码中也配置了`org.gradle.daemon=true`，长时间的进程启动可能导致服务器把进程kill掉，所以这里用了`./gradlew --no-daemon resguardProdRelease`添加`--no-daemon`可以一定程度上解决此问题。[参考文档](https://www.cnblogs.com/liushengchieh/p/9729786.html)

* on_failure不能指定失败阶段

  首先说明这个确实是不能指定，gitlab官方有提了两年还是open状态的issue：https://gitlab.com/gitlab-org/gitlab/issues/19400

  ```yaml
  stages:
   - build
   - test
   - deploy
  
  #If this one fails
  building:
      stage:build
      script: 
        - false
  
  #This one is ran
  on_build_failure:
      stage: test
      script: <Stuff on build failure>
      when: on_failure
  
  #This one is not ran
  testing:
      stage: test
      script: <run testing>
  
  #This one is ran (AND SHOULD NOT, as i haven't ran my tests)
  on_test_failure:
      stage: deploy
      script: <Stuff on test failure>
      when: on_failure
  
  #This one is not ran (Good)
  deploying:
      stage: deploy
      script: <deploy>
  ```

  所以解决方法是使用`文件存储变量`的方法来传递值
  
  ```yaml
  cache:
    paths:
      - ./test.txt
  
  stages:
    - s1
    - s2
  job:
    tags:
      - shared-146-tag
    stage: s1
    script:
      - echo 1 > ./test.txt
  
  job2:
    stage: s2
    tags:
      - shared-146-tag
    script:
      - cat ./test.txt
  ```
  
  