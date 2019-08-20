# Android富文本编辑器

***2019-08-20 更新日志 增加音频功能 增加链接下载功能 和视频下载功能***

***2019-08-16 更新日志 增加各种文件功能***

***2019-08-15 更新日志 增加添加视频功能***

该项目采用的方法是，放静态网页到项目中，通过webview js和html交互的方式，调用html自带的各种富文本编辑的功能，并加上了一些细节性需求优化，提供一定的拓展性，尝试用过用原生实现，安卓自带的Html类里面自定义标签，但是非常难实现和再现，再和其他端打通非常困难，最后发现直接用html编辑是最好的选择，可直接生成html源代码，也可以直接设置显示，这个方法需要你知道一定的js和webview前端知识

最后特别感谢[wasabeef/richeditor-android](https://github.com/wasabeef/richeditor-android "wasabeef/richeditor-android")，因为里面的RichEditor提供了给我全新的思路，来实现这些需求，让我越过了的原生实现的种种困难，
另外感谢HTML"Mogul" @ZX 让我知道html这些细节坑点 如何实现 ，可以视为它的RichEditor升级补充版

# RichHtmlEditorForAndroid

***2019-08-20 Update Log, Audio Function, Link Download Function and Video Download Function***

***2019-08-16 Update Log Added Files Function***

***2019-08-15 Update Log Added Video Function***

This project adopts the method of putting static web pages into the project, calling various rich text editing functions of HTML through interaction between WebView JS and html, and adding some detail requirements optimization to provide certain extensibility, trying to use native implementation, Android's own custom tags in the Html class. But it is very difficult to realize and reproduce, and it is very difficult to get through with other end. Finally, it is found that editing directly with html is the best choice. It can generate HTML source code directly or set up display directly. This method requires you to know some knowledge of JS and WebView front-end.



Finally, I would like to thank

[wasabeef/richeditor-android] (https://github.com/wasabeef/richeditor-android,'wasabeef/richeditor-android'),

because Rich Editor provides me with new ideas to meet these needs and overcomes the difficulties of native implementation.

Thanks to HTML Mogul@ZX for letting me know how to implement these details of HTML as a supplement to its Rich Editor upgrade.



[原理讲解--->](https://blog.csdn.net/qq_28844947/article/details/91870015 "")

[使用方式--->](https://github.com/RexSuper/RichHtmlEditorForAndroid/blob/master/RichHtmlEditorforAndroid/app/src/main/java/com/rex/richeditor/MainActivity.java "")



        
--->  | 支持功能  | Support function| <---
---- | ----- | ------ | ------
Video  | Image  | Audio | File And Download
Blod  | Italic | Subscript| Strikethrough
Underline  | JustifyLeft | JustifyCenter| JustifyRight
Blockquote  | Heading | Undo| Redo
Indent  | Outdent | InsertLink| Checkbox
TextColor  | TextBackgroundColor | FontSize| UnorderedList
OrderedList  | Hint |Superscript | InsertLink
# 实现内容

<img src="https://img-blog.csdnimg.cn/2019061317083452.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI4ODQ0OTQ3,size_16,color_FFFFFF,t_70" width="50%" height="50%" />

<img src="https://camo.githubusercontent.com/2affdf0fcd6e2686d1a610a323dad62a3b2d6b66/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303139303831353131333430303438312e6a70673f782d6f73732d70726f636573733d696d6167652f77617465726d61726b2c747970655f5a6d46755a33706f5a57356e6147567064476b2c736861646f775f31302c746578745f6148523063484d364c7939696247396e4c6d4e7a5a473475626d56304c334678587a49344f4451304f5451332c73697a655f31362c636f6c6f725f4646464646462c745f3730" width="50%" height="50%" />

<img src="https://img-blog.csdnimg.cn/20190815113453215.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI4ODQ0OTQ3,size_16,color_FFFFFF,t_70" width="50%" height="50%"/>

#Edit

```
    private void insertFile() {
        richEditor.insertFileWithDown(TEST_VIDEO_URL,"点击下载");
    }

    private void insertAudio() {
        richEditor.insertAudio("",
                //增加进度控制
                "controls=\"controls\"" +
                        //宽高
                        "height=\"300\" " +
                        //样式
                        " style=\"margin-top:10px;max-width:100%;\""
        );
    }

    private void insertVideo() {
        richEditor.insertVideo(TEST_VIDEO_URL,
                //增加进度控制
                "controls=\"controls\"" +
                        //视频显示第一帧
                        " initial-time=\"0.01\" " +
                        //宽高
                        "height=\"300\" " +
                        //样式
                        " style=\"margin-top:10px;max-width:100%;\""
        );

    }

    public void insertImage() {
        //可按htmlstyle 自定义间距居中等 类似margin-right 不会生效的问题 都是html本身的问题 可用一样的替换方案
        //实战过程中 本地图片需要先上传到服务器生成url 再调用
        richEditor.insertImage(TEST_IMAGE_URL,
                "picvision",
                "margin-top:10px;max-width:100%;");
    }

```

#Show

```
  richEditor.loadDataWithBaseURL("file:///android_asset/",
                    html + CommonJs.IMG_CLICK_JS, "text/html", "utf-8", null);
            richEditor.setOnClickImageTagListener(new RichEditor.OnClickImageTagListener() {
                @Override
                public void onClick(String url) {
                    Toast.makeText(ShowHtmlActivity.this, "url:" + url, Toast.LENGTH_LONG).show();
                }
            });

            richEditor.setDownloadListener(new DownloadListener() {
                @Override
                public void onDownloadStart(String url, String userAgent, String contentDisposition, String mimeType, long contentLength) {
                    //可自行实现
                    String fileName = URLUtil.guessFileName(url, contentDisposition, mimeType);
                    String destPath = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS)
                            .getAbsolutePath() + File.separator + fileName;
                    new DownloadTask(ShowHtmlActivity.this, new DownloadTask.DownloadTaskCallBack() {

                        @Override
                        public void onPreExecute() {
                            Toast.makeText(ShowHtmlActivity.this, "开始下载" , Toast.LENGTH_LONG).show();
                        }

                        @Override
                        public void doInBackground(int progress) {

                        }

                        @Override
                        public void onPostExecute(boolean noError, String url, String destPath) {
                            if (noError) {
                                Toast.makeText(ShowHtmlActivity.this, "下载完成" + destPath, Toast.LENGTH_LONG).show();
                                Intent handlerIntent = new Intent(Intent.ACTION_VIEW);
                                String mimeType = getMIMEType(url);
                                Uri uri = Uri.fromFile(new File(destPath));
                                handlerIntent.setDataAndType(uri, mimeType);
                                startActivity(handlerIntent);
                            } else {
                                Toast.makeText(ShowHtmlActivity.this, "下载失败!", Toast.LENGTH_SHORT).show();
                            }
                        }

                        @Override
                        public void onError(String error) {
                            Toast.makeText(ShowHtmlActivity.this, "下载失败:"+error, Toast.LENGTH_SHORT).show();
                        }
                    }).execute(url, destPath);
                }
            });

```

