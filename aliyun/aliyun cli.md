# Aliyun Cli

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Aliyun Cli](#aliyun-cli)
  - [install](#install)
    - [in linux](#in-linux)
    - [in docker](#in-docker)
  - [cli](#cli)
    - [configure](#configure)
      - [配置](#配置)
      - [查看](#查看)
    - [sts](#sts)
      - [扮演ram role，获取临时sts token](#扮演ram-role获取临时sts-token)
    - [ram](#ram)
      - [创建User](#创建user)
      - [创建role](#创建role)
      - [创建user access key](#创建user-access-key)
      - [删除ram user access key](#删除ram-user-access-key)
      - [attach policy](#attach-policy)
  - [practice](#practice)
    - [登录](#登录)
  - [concept](#concept)

<!-- /code_chunk_output -->

## install

### in linux

```bash
wget https://aliyuncli.alicdn.com/aliyun-cli-linux-3.0.124-amd64.tgz
tar -xvzf aliyun-cli-linux-3.0.124-amd64.tgz
mv aliyun /usr/local/bin/

```

### in docker

Dockerfile:

```Dockerfile
RUN curl -SLO "https://aliyuncli.alicdn.com/aliyun-cli-linux-3.0.124-amd64.tgz"
RUN tar -xvzf aliyun-cli-linux-3.0.124-amd64.tgz
RUN rm aliyun-cli-linux-3.0.124-amd64.tgz
RUN mv aliyun /usr/local/bin/
# 注意：如果From alpine需要额外创建 lib64 的动态链接库软连接
RUN mkdir /lib64 && ln -s /lib/libc.musl-x86_64.so.1 /lib64/ld-linux-x86-64.so.2
```

## cli

### configure

#### 配置

使用aliyun cli时 首先要进行configure配置
[官方文档](https://www.alibabacloud.com/help/zh/alibaba-cloud-cli/latest/non-interactive-configuration)

- 使用临时token方式：
    可以从阿里云sts颁发处获取credential内容，一般多用于ram role登录例如

    ```txt
    access_key_id=STS.xxxxxxxxxxxxxxxx
    access_key_secret=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    security_token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

    ```bash
    aliyun configure --profile ${profile name}  --region ${region} --mode StsToken --access-key-id ${access_key_id} --access-key-secret ${access_key_secret} --sts-token ${security_token}
    ```

- 使用用户名密码方式，一般用于ram user

    ```bash
    aliyun configure set --profile ${profile name} --mode AK --region ${region} --access-key-id ${access_key_id} --access-key-secret ${access_key_secret}
    ```

经过测试aliyun configure set命令会用最后一次set的profile作为生效的，如果configure list中已经包含了重复profile名称的profile则会覆盖该profile中的内容。同时一个当前最新的配置为sts类型（临时token），且列表中包含ak类型（用户名密码），sts token失效后会无法继续使用aliyun cli，需要重新进行configure set

#### 查看

```bash
aliyun configure list
```

结果：

```txt

Profile         | Credential         | Valid   | Region           | Language
---------       | ------------------ | ------- | ---------------- | --------
default         |                    | Invalid |                  | en
stsTokenProfile | StsToken:***hJ2    | Valid   | cn-beijing       | en
akProfile *     | AK:***TPH          | Valid   | cn-beijing       | en

```

### sts

#### 扮演ram role，获取临时sts token

[官方文档](https://help.aliyun.com/document_detail/371864.html)
`调用者`（ram user 或 ram role）扮演 `被扮演者` （某个具有某些权限ram role），返回用于登录被扮演者的sts credential内容，调用这个接口的前提是`调用者`需要**具备`AliyunSTSAssumeRoleAccess` 权限**，及`被扮演者`需要**信任**`调用者`

```bash
aliyun sts AssumeRole --RoleArn ${准备扮演的role的arn} --RoleSessionName ${起一个session名字} --DurationSeconds ${临时token有效期，单位s minmax=900-3600}
```

### ram

#### 创建User

```bash
aliyun ram CreateUser --UserName ${起个名字}
```

#### 创建role

```bash
aliyun ram CreateRole --RoleName ${RAM_ROLE_NAME} --MaxSessionDuration 3600  --AssumeRolePolicyDocument '{"Statement": [{"Action": "sts:AssumeRole","Effect": "Allow","Principal": {"RAM": ["acs:ram::${ALICLOUD_ACCOUNT_ID}:root"]}}],"Version": "1"}'

```

#### 创建user access key

```bash
aliyun ram CreateAccessKey --UserName ${username}
```

#### 删除ram user access key

同一个ram user下的access key是有数量限制的。有时新增前需要删除旧的

```bash
aliyun ram DeleteAccessKey --UserName ${username} --UserAccessKeyId ${access key id} 
```

#### attach policy

该命令用于为已存在的role或user添加permission

permission的类型和名称可以从官方文档查询

为user增加权限,例如增加扮演权限

```bash
aliyun ram AttachPolicyToUser --UserName $RAM_USER_NAME --PolicyName AliyunSTSAssumeRoleAccess --PolicyType System
```

为role增加权限,例如增加最大权限

```bash
aliyun ram AttachPolicyToRole --RoleName $RAM_ROLE_NAME --PolicyName AdministratorAccess --PolicyType System

```

## practice

### 登录

场景：从阿里云主账号分配等角色获取临时token，创建用于自己登录user和role。
前提是阿里云分配的角色具备较大的权限

**准备阶段**
首先从alicloud主页面获取sts token，进行sts登录

```bash
aliyun configure --profile ${profile name}  --region ${region} --mode StsToken --access-key-id ${access_key_id} --access-key-secret ${access_key_secret} --sts-token ${security_token}
```

创建ram user，为其创建access key和access key secret并仅赋予扮演role的权限

```bash
aliyun ram CreateUser --UserName ${RAM_USER_NAME}

aliyun ram CreateAccessKey --UserName ${RAM_USER_NAME}

aliyun ram AttachPolicyToUser --UserName ${RAM_USER_NAME} --PolicyName AliyunSTSAssumeRoleAccess --PolicyType System

```

创建ram role，用于之后登录创建资源，因此给予最大权限

```bash
# 创建的ram role可以被当前阿里云账号下面的任何user role扮演，即该ram role的受信实体
aliyun ram CreateRole --RoleName ${RAM_ROLE_NAME} --MaxSessionDuration 3600  --AssumeRolePolicyDocument '{"Statement": [{"Action": "sts:AssumeRole","Effect": "Allow","Principal": {"RAM": ["acs:ram::${ALICLOUD_ACCOUNT_ID}:root"]}}],"Version": "1"}'

aliyun ram AttachPolicyToRole --RoleName $RAM_ROLE_NAME --PolicyName AdministratorAccess --PolicyType System
```

**之后的登录阶段**
不再需要从alicloud网页上请求token，而是用我们创建好的ram user 和ram role
首先使用ram user登录

```bash
aliyun configure set --profile ${profile name} --mode AK --region ${region} --access-key-id ${access_key_id} --access-key-secret ${access_key_secret}
```

扮演ram role

```bash
aliyun sts AssumeRole --RoleArn ${RAM_ROLE_NAME} --RoleSessionName ${起一个session名字} --DurationSeconds 3600
```

切换sts模式登录，使用上一步返回的sts token

```bash
aliyun configure --profile ${profile name}  --region ${region} --mode StsToken --access-key-id ${access_key_id} --access-key-secret ${access_key_secret} --sts-token ${security_token}
```

## concept
