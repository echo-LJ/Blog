---
title: 阿里云分片上传oss
date: 2023-04-25 14:55:51
tags: oss
---


<meta name="referrer" content="no-referrer"/>

### 阿里云分片上传流程

![截屏2023-04-27 上午10.02.02.png](https://upload-images.jianshu.io/upload_images/11846892-9f27ada9813746a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 阿里云分片上传的原理

* 1、客户端(配置分割大小)将文件分割成多个小文件(OSS SDK执行分割)，通常每个小文件大小为1MB-10MB,并上传到阿里云oss中。
* 2、阿里云收到小文件，进行MD5校验，确保上传的数据准确无误。
* 3、当所有的小文件完成后，客户端(OSS SDK)向阿里云发送一个合并请求(OSS SDK 调用completeMultipartUpload)。
* 4、阿里云oss收到请求后，会将所有小文件按照指定顺序合并成一个完成的文件，并存储到指定的Bucket中。

### 分块上传如何如何保证中间不丢数据：


使用阿里云OSS SDK的分片上传功能，`无需手动进行块编号和排序操作`，SDK会自动处理这些问题

**预防几种情况：**

* `块上传失败`: 当一个小块上传失败时，需要重新上传，成功之后需要进行MD5检验，确保是正确的数据。
* `块上传顺序混乱`: 在上传多个小块的时候，需要按照编号顺序上传，否则合并后的文件将出现错误。OSS SDK自动确保分块按照其编号升序上传。
* `块上传被取消`: 在分块上传过程中，如果出现了错误或者用户取消了上传操作，需要取消未完成的块上传。
* `上传块的顺序变化`:  上传顺序的变化会导致文件合并出错，因此在进行分块上传时，需要确保上传顺序的不变性。

**分片上传完成**，阿里云OSS SDk内部会调用`completeMultipartUpload`API通知OSS进行文件合并

在进行阿里云分片上传时，客户端需要按照指定的大小分割文件，并设置并发上传数，以确保上传的效率和稳定性。同时，阿里云OSS也提供了断点续传和秒传等功能，进一步提高了分片上传的效果和性能。


### 怎么保证多个分块属于同一个文件

当文件开始进行分块上传时会生成一个文件任务id：
文件进行分割时每个分块会有一个part_number变量：指代当前分块在整个数据流中的编号
每个分块开始上传的时候会生成一个分块id：用来标识哪些块属于同一个大文件与文件任务id进行绑定，
**怎么进行分割**
通过file.read方法读取对应大小的数据
```
chunk_size = 8 * 1024 * 1024  # 设置每个分块的大小，8MB

data = file.read(chunk_size)
            if not data:
                break
```

### 分块上传之后如何验证上传成功

验证分块上传的完整性和正确性的方法如下：

* 当每个块上传结束后，客户端可以通过比较本地计算出的MD5值和OSS返回的ETag值是否相等来检查块是否上传正确。如果两者不一致，则表示该块上传失败，需要重新上传；

* 当所有块上传至阿里云后，OSS服务会将各个块的MD5值合并计算，并得到上传文件的MD5值，在完成分块上传后返回给客户端，客户端可以通过比较本地计算出的MD5值和OSS返回的MD5值是否相等来检查上传文件是否上传正确。

```
import hashlib

# 计算文件的MD5值
def get_md5(file):
    m = hashlib.md5()
    while True:
        data = file.read(4096)
        if not data:
            break
        m.update(data)
    return m.hexdigest()

# 比较本地计算出的MD5值和OSS返回的MD5值是否相同
def check_md5(file_path, md5):
    with open(file_path, 'rb') as f:
        local_md5 = get_md5(f)
        return local_md5 == md5

# 获取OSS返回的文件MD5值
def get_file_md5(bucket, object_name, upload_id):
    e_tags = []
    for part in oss2.ObjectIterator(bucket, key_marker=object_name,
                                    upload_id_marker=upload_id):
        e_tags.append(part.etag.strip('"'))
    return hashlib.md5(''.join(e_tags).encode('utf-8')).hexdigest()
```

### 分片上传生成的文件任务id和分块顺序标识
* ossSDK调用InitiateMultipartUpload API 服务端返回的是一个唯一标识上传任务的uploadId
* 分块上传时将 uploadId partNumber 两个参数
```
import oss2

# 替换成您自己的AccessKeyId和AccessKeySecret
auth = oss2.Auth('<yourAccessKeyId>', '<yourAccessKeySecret>')

# 创建Bucket对象
bucket = oss2.Bucket(auth, 'https://<yourBucketName>.<yourEndpoint>', '<yourBucketName>')

# 准备要分片上传的文件
object_name = 'example.zip'
file_path = '<path_to_your_local_file>/example.zip'
chunk_size = 8 * 1024 * 1024  # 设置每个分块的大小，8MB
upload_id = ''  # 用于标识上传任务的uploadId变量，API调用InitiateMultipartUpload（开启分块上传）方法返回的值
part_number = 1  # 分块编号，按照从1开始递增

try:
    # 开始上传任务
    # ossSDK调用InitiateMultipartUpload API 服务端返回的是一个唯一标识上传任务的uploadId
    result = bucket.init_multipart_upload(object_name)
    upload_id = result.upload_id

    // print函数的作用是用来输出上传分块的信息，包括分块的序号和MD5值等信息，以便于开发人员进行调试和监控上传进度。
    // 每个分块成功上传至阿里云OSS后，都会返回该块的MD5值（即ETag值），开发人员可以根据该值验证上传的块是否正确。

    print('InitiateMultipartUpload success. UploadId:', upload_id)
    
    # 分块上传文件
    with open(file_path, 'rb') as file:
        while True:
            data = file.read(chunk_size)
            if not data:
                break
            # 并且会在object_name加上upload_id参数，将每个上传块标识与该upload_id关联在一起
            # part_number指代当前分块在整个数据流中的编号
            res = bucket.upload_part(object_name, upload_id, part_number, data)
            e_tag = res.etag  # 获取响应结果的ETag字段（MD5值）
            print('PartNumber:', part_number, 'ETag:', e_tag)
            part_number += 1
            
    print('All parts have been uploaded.')
    
    # 合并分块上传的数据
    bucket.complete_multipart_upload(object_name, upload_id, [oss2.models.PartInfo(i, e_tag) for i, e_tag in enumerate(e_tags, 1)])
    print('CompleteMultipartUpload success. UploadId:', upload_id)

except Exception as e:
    if upload_id:
        bucket.abort_multipart_upload(object_name, upload_id)
    print('MultipartUpload failed:', e)
```

### 文档库文件上传需求实现原理：

1、上传文件，前端生成md5访问服务端是否有对应文件，如果存在，直接调用保存接口。
2、不存在：调用aliyun-OSS SDk 分片上传功能
3、执行oss-sdk分片上传流程
4、停止上传: 调用aliyun oss-sdk 的abortMultipartUpload 方法停止
5、继续上传: 采用断点续传上传功能，（先调接口查询该文件是否已存在） 通过后端调用阿里云服务器的checkpoint接口获取到当前point数据，前端调用osd-sdk的断点续传功能继续上传，
6、上传成功，oss-sdk触发回调函数，前端调用保存接口，完成上传流程。

### 阿里云oss-sdk上传流程

1、开始
2、文件分块
3、触发initMultipartUpload方法生成upload id 返回给前端
4、调用分片上传方法
5、判断是否为最后一个分块
6、发送合并请求
7、完成上传。


**分片上传完成之后aliyun返回的数据**
![截屏2023-04-27 下午2.04.45.png](https://upload-images.jianshu.io/upload_images/11846892-c7577b7a082d9405.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**停止上传后端调用的checkpoint返回的数据**

![截屏2023-04-27 下午2.08.41.png](https://upload-images.jianshu.io/upload_images/11846892-c3d4d55b372c4efa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// Vue.js 示例代码
<template>
  <div>
    <input type="file" @change="onChangeUploadFile">
    <button @click="startUpload">开始上传</button>
    <button @click="pauseUpload">暂停上传</button>
    <button @click="resumeUpload">继续上传</button>
  </div>
</template>

<script>
import OSS from 'ali-oss';
import OSSVue from 'vue-oss-upload';

export default {
  data() {
    return {
      client: null, // OSS 客户端对象
      objectName: '', // 文件在 OSS 中的 object key
      file: null, // 待上传的文件对象
      uploadId: '', // 当前上传任务的 ID
      partInfo: [], // 当前文件已上传块的信息
      progress: 0, // 当前已上传进度
      isUploading: false // 当前是否正在上传
    }
  },
  methods: {
    // 初始化阿里云 OSS 客户端对象
    initOSSClient() {
      this.client = new OSS({
        region: 'oss-cn-hangzhou', // 区域节点
        accessKeyId: 'your-access-key-id', // Access Key ID
        accessKeySecret: 'your-access-key-secret', // Access Key Secret
        bucket: 'your-bucket-name' // 存储空间名称
      });
    },
    // 选择上传的文件
    onChangeUploadFile(event) {
      this.file = event.target.files[0];
      this.objectName = this.file.name;
      this.progress = 0;
    },
    // 开始上传文件
    startUpload() {
      this.initOSSClient();
      // 初始化上传任务
      this.client.multipartUpload(this.objectName, this.file)
        .then((result) => {
          this.uploadId = result.uploadId;
          this.partInfo = result.partList;
          this.isUploading = true;
        });
    },
    // 暂停上传任务
    pauseUpload() {
      this.client.abortMultipartUpload(this.objectName, this.uploadId)
        .then((result) => {
          this.partInfo = result.partList; // 保存已上传块的信息
          this.isUploading = false;
        });
    },
    // 继续上传任务
    resumeUpload() {
      this.isUploading = true;
      // 恢复上传任务
      this.uploadParts(this.objectName, this.partInfo, this.uploadId, this.file)
        .then(() => {
          console.log('续传成功');
          this.partInfo = []; // 清空已上传块的信息
          this.isUploading = false;
        })
        .catch((err) => {
          console.log('续传失败: ', err);
          this.isUploading = false;
        });
    },
    // 上传分块
    async uploadPart(part) {
      const partNumber = part.number;
      const { client, objectName, uploadId } = this;
      return await client.uploadPart(objectName, uploadId, partNumber, part.part)
        .then(({ ETag }) => {
          part.ETag = ETag;
          return part;
        });
    },
    // 上传所有分块
    async uploadParts(objectName, parts, uploadId, file) {
      const { client } = this;
      return await Promise.all(parts.map(async (part) => {
        part.part = file.slice(part.offset, part.offset + part.size);
        return await this.uploadPart(part);
      })).then(async (partList) => {
        return await client.completeMultipartUpload(objectName, uploadId, partList)
      });
    }
  }
}
</script>

```
### MD5是什么？

MD5值是一种哈希算法，用于将任意长度的数据转换为固定长度的数据字符串，通常是32个字符。该算法具有不可逆性和唯一性，即对于同一个输入数据，其输出结果是唯一的，而且无法从输出结果反推出原始的输入数据。因此，MD5值可以用于验证数据完整性和正确性，确保数据在传输过程中没有被篡改或者损坏。

###  Bucket是什么？

Bucket原意为桶，是用户用来管理所存储对象的存储空间。 需要注意的是，用户可拥有多个Bucket，但其名称在OSS中必须是全局唯一的，一旦创建便无法修改，其内部对象数量不限
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：

* [阿里云OSS文档](https://help.aliyun.com/document_detail/31850.html)

