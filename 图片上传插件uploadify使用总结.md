---
date: 2016-06-15 17:37
status: public
tags: javascript,jquery,uploadify,图片上传
title: 图片上传插件uploadify使用总结
---

uploadify是Jquery的一个上传插件，带进度显示。


```javascript
//点击出现上传框
$('#file_upload').click(function () {
        $('#custom_file_upload').trigger('click');
        $('#SWFUpload_0').trigger('click');
        $('#custom_file_upload-button').trigger('click');
        $('.uploadify-button-text').trigger('click');
    });


 $('#file_upload').uploadify({
            //uploadify.swf 文件的相对路径，该swf文件是一个带有文字BROWSE的按钮，点击后淡出打开文件对话框
            'swf'      :'/bauna/resources/js/uploadify/uploadify.swf',
            //处理上传图片服务器的URL            
            'uploader':'saveImage',
            //选择图片按钮上的文字
            'buttonText':'请选择上传图片',
            //是否自动上传图片 true自动，false手动
            'auto':true,
            //服务器端文件对象名  
            'fileObjName':'filedata',
            //允许上传的图片类型
            'fileTypeExts': '*.JPG;*.JPEG;*.GIF;*.PNG',
            //上传图片最大容量
            'fileSizeLimit':'5MB',
            //同时上传文件的最大个数
            'queueSizeLimit':10,
            //等待服务端返回上传成功的秒数
            'successTimeout':30,
            //最大允许上传文件数量
            'uploadLimit':10,
            //与文件一起发送到服务端的附加数据
            'formData':{
                "orderId":modal_order_id
            },
            //每个文件上传完成后触发
            'onUploadComplete':function(file){
                //console.table(file);
            }
        });
```