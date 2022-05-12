## Provider 发布

### 构建 Provider
- 以 terraform-provider-http 演示：https://github.com/hashicorp/terraform-provider-http
- clone 代码
- 说明开发环境依赖，提示用户安装
- 编译 Provider

### 打包 Provider
- 生成 GPG 密钥
- 将密钥添加到 namespace
- 打包 zip 文件
- 生成 SHA256SUMS 文件并签名
- 打包 docs(可选)

### 发布 Provider
- 将打包好的 Provider 上传到 http 服务器
    - 演示使用 scp 进行文件上传，并通过 curl 可以访问文件
- 在 reigstry 上执行发布
- 发布后查看 provider 详情及文档


1、生成GPG密钥

此步骤参考[GPG签名密钥获取](../registry/#gpg)

2、添加GPG密钥到namespace

添加GPG密钥步骤参考[添加签名密钥](../registry/#_4)

3、编译打包 provider zip 包，并生成SHA256SUMS文件，这部分请参考[terraform文档](https://www.terraform.io/docs/registry/providers/publishing.html#manually-preparing-a-release)

4、使用上面生成的GPG密钥对SHA256SUMS文件进行签名:

```bash
gpg -u "$GPG_EMAIL" --detach-sign terraform-provider-${TYPE}_${VERSION}_SHA256SUMS
```

5、打包 docs (可选):

[terraform docs 规范文档](https://www.terraform.io/docs/registry/providers/docs.html)

docs 编写完成后打为 zip 包：zip terraform-provider-${TYPE}_${VERSION}_docs.zip -r docs

此时目录中的文件列表如下(cloudiac_1.0.0 为示例):

```bash
terraform-provider-cloudiac_1.0.0_docs.zip
terraform-provider-cloudiac_1.0.0_linux_amd64.zip
terraform-provider-cloudiac_1.0.0_linux_386.zip
terraform-provider-cloudiac_1.0.0_SHA256SUMS
terraform-provider-cloudiac_1.0.0_SHA256SUMS.sig
```

6、将上一步生成的所有 zip 文件、SHA256SUMS 文件和 SHA256SUMS.sig 文件上传到任意 http 服务器，保证上传后我们可以通过类型下面的 url 下载文件:  

https://static.example.org/providers/cloudiac/1.0.0/terraform-provider-cloudiac_1.0.0_linux_386.zip

7、登录 registry 执行发布:

![img](../images/registry-provider-publish1.png){.img-fluid}

![img](../images/registry-provider-publish2.png){.img-fluid}

- 下载路径可以是 zip 包或者是 SHA256SUMS 文件，如果下载路径是 SHA256SUMS 文件则会发布文件中包含的所有 zip 包
- 如果下载路径同目录下有 terraform-provider-${TYPE}_${VERSION}_docs.zip 文件，则在发布时也会同时下载 docs 并解析生成 provider 文档，生成的文档可以在 registry 平台查看