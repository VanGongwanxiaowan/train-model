https://ostechnix.com/20-ffmpeg-commands-beginners/

FFmpeg 命令示例
FFmpeg 命令的典型语法是：

ffmpeg [global_options] {[input_file_options] -i input_url} ...
 {[output_file_options] output_url} ...
接下来我们将学习一些重要且实用的 FFmpeg 命令。

1. 获取音频/视频文件信息

要显示媒体文件的详细信息，请运行：

ffmpeg -i video.mp4
示例输出：

ffmpeg 版本 n4.1.3 版权所有 (c) 2000-2019 FFmpeg 开发人员
使用 gcc 8.2.1 (GCC) 20181127 构建
配置：--prefix=/usr --disable-debug --disable-static --disable-stripping --enable-fontconfig --enable-gmp --enable-gnutls --enable-gpl --enable-ladspa --enable-libaom --enable-libass --enable-libbluray --enable-libdrm --enable-libfreetype --enable-libfribidi --enable-libgsm --enable-libiec61883 --enable-libjack --enable-libmodplug --enable-libmp3lame --enable-libopencore_amrnb --enable-libopencore_amrwb --enable-libopenjpeg --enable-libopus --enable-libpulse --enable-libsoxr --enable-libspeex --enable-libssh --enable-libtheora --enable-libv4l2 --enable-libvidstab --enable-libvorbis --启用libvpx --启用libwebp --启用libx264 --启用libx265 --启用libxcb --启用libxml2 --启用libxvid --启用nvdec --启用nvenc --启用omx --启用shared --启用version3
libavutil 56.22.100 / 56.22.100
libavcodec 58.35.100 / 58.35.100
libavformat 58.20.100 / 58.20.100
libavdevice 58.5.100 / 58.5.100
libavfilter 7.40.101 / 7.40.101
libswscale 5.3.100 / 5.3.100
libswresample 3.3.100 / 3.3.100
libpostproc 55.3.100 / 55.3.100
输入 #0，mov，mp4，m4a，3gp，3g2，mj2，来自'video.mp4'：
元数据：
major_brand：isom
次版本号：512
兼容品牌：isomiso2avc1mp41
编码器：Lavf58.20.100
时长：00:00:28.79，起始时间：0.000000，比特率：454 kb/s
流 #0:0(und): 视频: h264 (High) (avc1 / 0x31637661), yuv420p(tv, smpte170m/bt470bg/smpte170m), 1920x1080 [SAR 1:1 DAR 16:9], 318 kb/s, 30 fps, 30 tbr, 15360 tbn, 60 tbc (default)
元数据：
handler_name：Google Inc. 制作的 ISO 媒体文件。创建于：2019 年 4 月 8 日。
流 #0:1(eng): 音频：aac (LC) (mp4a / 0x6134706D)，44100 Hz，立体声，fltp，128 kb/s（默认）
元数据：
handler_name：Google Inc. 制作的 ISO 媒体文件。创建于：2019 年 4 月 8 日。
必须指定至少一个输出文件。


如上所示，FFmpeg 会显示媒体文件信息以及 FFmpeg 的详细信息，例如版本、配置详细信息、版权声明、构建和库选项等。

如果您不想看到 FFmpeg 横幅和其他详细信息，而只想看到媒体文件信息，请使用-hide_banner如下标志。

ffmpeg -i video.mp4 -hide_banner
示例输出：

使用 FFmpeg 查看音频、视频文件信息
使用 FFmpeg 查看音频、视频文件信息
看到了吗？现在，它只显示媒体文件的详细信息。


2. 将视频文件转换为不同格式

由于 FFmpeg 是一款功能丰富且强大的音频和视频转换器，因此可以转换不同格式的媒体文件。例如，要将mp4文件转换为avi文件，请运行：

ffmpeg -i video.mp4 video.avi
同样，您可以将媒体文件转换为您选择的任何格式。

例如，要将 YouTubeflv格式的视频转换为mpeg其他格式，请运行：

ffmpeg -i video.flv video.mpeg

如果要保持源视频文件的质量，请使用'-qscale 0'以下参数：

ffmpeg -i input.webm -qscale 0 output.mp4
要查看 FFmpeg 支持的格式列表，请运行：

ffmpeg 格式
3. 将视频文件转换为音频文件
要将视频文件转换为音频文件，只需指定输出格式为.mp3、 或.ogg或任何其他音频格式即可。

上述命令会将input.mp4视频文件转换为output.mp3音频文件。

ffmpeg -i input.mp4 -vn output.mp3
此外，您还可以使用如下所示的各种音频转码选项对输出文件进行处理。

ffmpeg -i input.mp4 -vn -ar 44100 -ac 2 -ab 320 -f mp3 output.mp3
这里，

-vn- 表示我们已禁用输出文件中的视频录制。
-ar- 设置输出文件的音频频率。常用值为  100Hz 22050、 100Hz 44100、48000100Hz。
-ac - 设置音频通道数。
-ab- 表示音频比特率。
-f - 输出文件格式。在本例中，它是 .txt 文件mp3 格式。
上述命令将显示如下警告信息。


[libmp3lame @ 0x5589ed539240] 比特率 320 非常低，您是不是想说 320k
比特率参数设置得太低了。它接受的是比特/秒（bits/s）作为参数，而不是千比特/秒（kbits/s）。
这是因为我们设置的320比特率非常低，单位是比特/秒。这样会生成较小的输出文件。为了获得最佳输出质量，请使用比特率值，而320k不是仅仅使用比特率320。

4. 更改音频文件的音量
FFmpeg 允许我们使用选项来改变音频文件的音量"volume filter"。

例如，以下命令会将音量减少一半。

了解更多
转换
Linux
视频
OSTechNix
转码
Linux
视频
FFmpeg
Youtube-dl
libavcodec
ffmpeg -i input.mp3 -af 'volume=0.5' output.mp3
同样地，我们可以按如下方式增加音量：

ffmpeg -i input.mp3 -af 'volume=1.5' output.mp3
5. 更改视频文件的分辨率
如果要为视频文件设置特定分辨率，可以使用以下命令：

ffmpeg -i input.mp4 -filter:v scale=1280:720 -c:a copy output.mp4
了解更多
libavformat
Linux
视频转换
视频
视频编解码器
转换
视频录制
ffmpeg
转码
Linux
或者，

ffmpeg -i input.mp4 -s 1280x720 -c:a copy output.mp4
上述命令会将给定视频文件的分辨率设置为1280x720。

同样地，要将上述文件转换为640x480指定大小，请运行：

ffmpeg -i input.mp4 -filter:v scale=640:480 -c:a copy output.mp4
或者，

ffmpeg -i input.mp4 -s 640x480 -c:a copy output.mp4
这个技巧可以帮助你将视频文件缩放到平板电脑和手机等较小的显示设备上。

6. 压缩视频文件
为了节省磁盘空间，减小媒体文件的大小始终是一个好主意。

以下命令将压缩并减小输出文件的大小。

ffmpeg -i input.mp4 -vf scale=1280:-1 -c:v libx264 -preset veryslow -crf 24 output.mp4
请注意，如果您尝试减小视频文件大小，将会降低视频质量。如果设置过高，您可以crf适当降低该值。2324

您还可以通过添加以下选项，将音频转码为立体声，以减小文件大小。

-ac 2 -c:a aac -strict -2 -b:a 128k
7. 压缩音频文件
就像压缩视频文件一样，你也可以使用-ab标志来压缩音频文件，以节省一些磁盘空间。

假设你有一个320比特率为 kbps 的音频文件。你想通过将比特率更改为如下所示的较低值来压缩它。

ffmpeg -i input.mp3 -ab 128 output.mp3
以下是各种可用的音频比特率列表：

96kbps
112kbps
128kbps
160kbps
192kbps
256kbps
320kbps
8. 从视频文件中移除音频流
如果你不想从视频文件中提取音频，请使用-an标志。

ffmpeg -i input.mp4 -an output.mp4
此处'an'表示不进行音频录制。换句话说，此选项将使音频静音。

上述命令将撤销所有与音频相关的标志。

9. 从媒体文件中移除视频流
同样，如果您不需要视频流，可以使用'vn'标志轻松地将其从媒体文件中移除。vn 表示不录制视频。换句话说，此命令会将给定的媒体文件转换为音频文件。

以下命令将从指定的媒体文件中删除视频。

ffmpeg -i input.mp4 -vn output.mp3
您还可以使用标志指定输出文件的比特率'-ab'，如下例所示。

ffmpeg -i input.mp4 -vn -ab 320 output.mp3
10. 从视频中提取图像
FFmpeg的另一个实用功能是可以轻松地从视频文件中提取图像。如果您想用视频文件创建相册，这将非常有用。

要从视频文件中提取图像，请使用以下命令：

ffmpeg -i input.mp4 -r 1 -f image2 image-%2d.png
这里，

-r- 设置帧速率，即每秒提取为图像的帧数。默认值为25。
-f- 指示输出格式，在本例中为图像格式。
image-%2d.png- 指定提取图像的命名方式。在这种情况下，名称应以 `<image_name>` image-01.png、image-02.png`<image_name>`image-03.png等开头。如果使用 `<image_name>` %3d，则图像名称将以 `<image_name>` image-001.png、image-002.png`<image_name>` 等开头。
11. 视频裁剪
FFMpeg 允许我们按任意尺寸裁剪给定的媒体文件。

以下是裁剪视频文件的语法：

ffmpeg -i input.mp4 -filter:v "crop=w:h:x:y" output.mp4
这里，

input.mp4- 源视频文件。
-filter:v- 表示视频滤镜。
crop- 表示裁剪过滤器。
w- 要从源视频中裁剪出的矩形的宽度。
h- 矩形的高度。
x-要从源视频中裁剪出的矩形的x坐标。
y- 矩形的y坐标。
假设你想从位置 ( )开始，截取一个宽度为640像素、高度为480像素的视频，命令如下：200,150

ffmpeg -i input.mp4 -filter:v "crop=640:480:200:150" output.mp4
请注意，裁剪视频会影响视频质量。除非必要，否则请勿裁剪视频。

12. 转换视频的特定部分
有时，您可能只想将视频文件的特定部分（时长）转换为其他格式。例如，以下命令会将给定 video.mp4 文件的前10几秒转换为 video.avi 格式。

ffmpeg -i input.mp4 -t 10 output.avi
这里，我们以秒为单位指定时间。此外，也可以指定其他 hh.mm.ss格式的时间。

13. 设置视频宽高比
-aspect您可以使用如下标志来设置视频文件的宽高比。

$ ffmpeg -i input.mp4 -aspect 16:9 output.mp4
常用的宽高比有：

16:9
4:3
16:10
5:4
2:21:1
2:35:1
2:39:1
14. 将海报图像添加到媒体文件
您可以将海报图片添加到文件中，这样在播放音频或视频文件时图片就会显示出来。这对于在视频托管或分享网站上托管音频文件非常有用。

ffmpeg -loop 1 -i inputimage.jpg -i inputaudio.mp3 -c:v libx264 -c:a aac -strict experimental -b:a 192k -shortest output.mp4
15. 使用开始时间和结束时间修剪媒体文件（修剪视频的特定部分）
要使用开始时间和结束时间将视频剪辑成更小的片段，我们可以使用以下命令。

ffmpeg -i input.mp4 -ss 00:00:50 -codec copy -t 50 output.mp4
这里，

--s- 表示视频片段的开始时间。在本例中，开始时间为第 50 秒。
-t- 表示总时长。
当您想根据起始时间和结束时间从音频或视频文件中剪切一段内容时，这将非常有用。

同样，我们可以像下面这样剪辑音频文件。

ffmpeg -i audio.mp3 -ss 00:01:54 -to 00:06:53 -c copy output.mp3
16. 将音频/视频文件分割成多个部分

有些网站只允许上传特定大小的视频。例如，WhatsApp 只允许15印度用户将几秒钟的视频设置为状态消息。在这种情况下，您可以像下面这样将大视频文件分割成多个较小的部分。

ffmpeg -i input.mp4 -t 00:00:30 -c copy part1.mp4 -ss 00:00:30 -codec copy part2.mp4
这里，-t 00:00:30表示从视频开头到第 30 秒的部分。-ss 00:00:30显示了视频下一部分的起始时间戳。这意味着第二部分将从第 30 秒开始，一直持续到原始视频文件的结尾。

17. 合并音频和视频文件
要将音频文件和视频文件合并并进行音频重新编码，请运行：

ffmpeg -i inputvideo.mp4 -i inputaudio.mp3 -c:v copy -c:a aac output.mp4
上述命令会将inputvideo.mp4文件合并到inputaudio.mp3一起output.mp4，还会对音频文件进行重新编码。

这里，我假设音频文件和视频文件的长度相同，并且视频文件没有任何音频流。

如果您的音频或视频流较长，您可以添加该-shortest选项，以便 ffmpeg 在一个文件结束后停止编码。

如果你的输入视频已经包含音频，并且你想替换它，你需要告诉 ffmpeg 要使用哪个音频流，如下所示：

ffmpeg -i inputvideo.mp4 -i inputaudio.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 output.mp4
此-map选项使 ffmpeg 仅使用来自第一个输入的第一个视频流和来自第二个输入的第一个音频流作为输出文件。

如果要将音频文件与视频文件合并，且不进行音频重新编码，只需按如下方式复制这两个文件：

ffmpeg -i inputvideo.mp4 -i inputaudio.mp3 -c copy output.mkv
18. 将多个音频/视频片段合并成一个片段
FFmpeg 还会将多个视频片段合并成一个视频文件。

创建join.txt一个文件，其中包含要合并的所有文件的确切路径。所有文件应格式相同（编码格式相同）。所有文件的路径名应逐一列出，如下所示。

文件 /home/sk/myvideos/part1.mp4
文件 /home/sk/myvideos/part2.mp4
文件 /home/sk/myvideos/part3.mp4
文件 /home/sk/myvideos/part4.mp4
现在，使用以下命令合并所有文件：

ffmpeg -f concat -i join.txt -c copy output.mp4
如果您收到类似以下的错误信息；

[concat @ 0x555fed174cc0] 不安全的文件名“/path/to/mp4”
join.txt：操作不允许
添加"-safe 0"：

ffmpeg -f concat -safe 0 -i join.txt -c copy output.mp4
上述命令会将文件 、 、 和 文件合并part1.mp4成part2.mp4一个part3.mp4名为part4.mp4的单个文件"output.mp4"。

或者，您可以使用以下单行命令将目录中的所有文件合并在一起。转到包含文件的目录，然后运行以下命令将名为 `<filename>`、`<filename>` 和 `<filename>` 的文件合并audio1.mp3到audio2,mp3` audio3.mp3<filename>` 中output.mp3。

ffmpeg -i "concat:audio1.mp3|audio2.mp3|audio3.mp3" -c copy output.mp3
19. 为视频文件添加字幕
我们还可以使用 FFmpeg 为视频文件添加字幕。下载与您的视频匹配的字幕文件，并按照如下所示将其添加到视频中。

fmpeg -i input.mp4 -i subtitle.srt -map 0 -map 1 -c copy -c:v libx264 -crf 23 -preset veryfast output.mp4
20. 预览或测试视频或音频文件
您可能需要预览以验证或测试输出文件是否已正确转码。为此，您可以使用以下命令在终端中播放：

ffplay video.mp4
使用 ffplay 播放视频文件
使用 ffplay 播放视频文件
同样，您可以按如下所示测试音频文件。

ffplay audio.mp3
使用 ffplay 播放音频文件
使用 ffplay 播放音频文件
21. 提高/降低视频播放速度
FFmpeg 允许您调整视频播放速度。

要提高视频播放速度，请运行：


ffmpeg -i input.mp4 -vf "setpts=0.5*PTS" output.mp4
该命令会将视频播放速度提高一倍。

要减慢视频播放速度，您需要使用大于某个值的1倍数。要降低播放速度，请运行：

ffmpeg -i input.mp4 -vf "setpts=4.0*PTS" output.mp4
22. 提高/降低音频播放速度
要加快或减慢音频播放速度，请使用"atempo"音频滤波器。以下命令会将音频播放速度提高一倍。

ffmpeg -i input.mp4 -filter:a "atempo=2.0" -vn output.mp4
音频可以使用介于0.5和2.0之间的任何值。

23. 创建动画 GIF
我们几乎在所有社交和专业网络上都使用 GIF 图片，用途广泛。借助 FFmpeg，我们可以轻松快捷地创建动画视频文件。

以下指南解释了如何在 Linux 和类 Unix 系统中使用 FFmpeg 和 ImageMagick 创建动画 GIF 文件。

如何在Linux中创建动画GIF
推荐阅读：

Gifski - 一款跨平台的高质量 GIF 编码器
24. 从 PDF 文件创建视频
多年来，我收集了很多PDF文件，大部分是Linux教程，并保存在我的平板电脑里。有时候我懒得用平板电脑看它们。

所以我决定用PDF文件制作视频，然后在电视或电脑等大屏幕设备上观看。如果您也想知道如何用一组PDF文件制作视频，以下指南将对您有所帮助。

如何在Linux系统中从PDF文件创建视频
25. 旋转视频
如果您有不同方向（纵向或横向）的视频文件，您可以按照以下指南中的说明旋转它们。

如何使用 FFMpeg 通过命令行旋转视频
26. 将视频转换为 WhatsApp 视频格式
WhatsApp 不支持某些视频格式。您无法与联系人分享这些视频，也无法将其设置为 WhatsApp 状态。别担心！我们可以使用 FFmpeg 轻松将视频转换为 WhatsApp 支持的视频格式，具体方法请参见以下链接。

使用 FFmpeg 将视频转换为 WhatsApp 视频格式
27. 视频缩放
FFmpeg 拥有许多用于执行特定任务的实用滤镜。其中之一就是缩放平移滤镜。使用缩放平移滤镜，我们可以轻松地每隔 X 秒进行一次周期性的放大和缩小。

更多详情请参考以下链接：

如何使用 FFmpeg 放大和缩小视频
28.从图像创建视频
FFmpeg 最实用的功能之一就是能够从一系列图像创建视频。你甚至可以为输出的视频添加酷炫的背景音乐。更多详情，请参考以下链接：

如何使用 FFmpeg 从图像创建视频（并添加音频）
29. 寻求帮助
本指南介绍了最常用的 FFmpeg 命令。它还有许多其他选项，可以执行各种高级功能。要了解更多信息，请参阅手册页 (man page)。

ffmpeg
推荐阅读：

Youtube-dl 入门教程及示例
在 Linux 系统上从 YouTube 视频的特定部分提取文本
结论
本指南介绍了 FFmpeg 是什么，以及如何在各种 Linux 发行版中安装 FFmpeg。然后，我们提供了常用FFmpeg 命令列表以及FFmpeg 入门示例。

资源：

FFmpeg 文档
相关阅读：

FFmpeg 通过手写 AVX-512 代码实现了 94 倍的性能提升


