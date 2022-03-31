---
title: 如何加速或减速一个视频
top: false
cover: false
toc: true
mathjax: false
date: 2022-03-31 21:27
password:
summary:
tags: [FFmpeg, 翻译]
categories:
---
[原文](http://trac.ffmpeg.org/wiki/How%20to%20speed%20up%20/%20slow%20down%20a%20video)
# 加速/减速视频
可以通过更改每个视频帧的呈现时间戳 (PTS) 来更改视频流的速度。这可以通过两种方法完成：使用 setpts 视频过滤器（需要重新编码）或通过将视频导出为原始比特流格式并在创建新时间戳时复用到容器来擦除时间戳。

请注意，在以下示例中，音频流未更改，因此理想情况下应使用 -an 禁用它。

### 原始比特流方法
这种方法是无损的，除了更改时间戳之外，还可以按原样复制视频流。如果您不需要对输入视频进行其他更改，请使用此选项。

首先，将视频复制为原始比特流格式。

对于 H.264：
`ffmpeg -i input.mp4 -map 0:v -c:v copy -bsf:v h264_mp4toannexb raw.h264`
对于 H.265：
`ffmpeg -i input.mp4 -map 0:v -c:v copy -bsf:v hevc_mp4toannexb raw.h265`
然后在复用到容器时生成新的时间戳：
`ffmpeg -fflags +genpts -r 30 -i raw.h264 -c:v copy output.mp4`
将 -r 的值更改为所需的播放帧速率。

### setpts 视频过滤器
要使用 setpts 过滤器将视频速度加倍，您可以使用：
`ffmpeg -i input.mkv -filter:v "setpts=0.5*PTS" output.mkv`
该过滤器通过更改每个视频帧的呈现时间戳 (PTS) 来工作。例如，如果在时间戳 1 和 2 处连续显示两个帧，并且您想加快视频速度，则这些时间戳需要分别变为 0.5 和 1。因此，我们必须将它们乘以 0.5。

请注意，此方法将丢帧以达到所需的速度。您可以通过指定比输入更高的输出帧速率来避免丢帧。例如，从 4 FPS 的输入到加速到 4 倍（16 FPS）的输入：
`ffmpeg -i input.mkv -r 16 -filter:v "setpts=0.25*PTS" output.mkv`
要减慢视频速度，您必须使用大于 1 的乘数：
`ffmpeg -i input.mkv -filter:v "setpts=2.0*PTS" output.mkv`

#### 平滑
您可以使用插值视频过滤器平滑慢/快视频。这也称为“运动插值”或“光流”。
`ffmpeg -i input.mkv -filter:v "minterpolate='mi_mode=mci:mc_mode=aobmc:vsbmc=1:fps=120'" output.mkv`
其他选项包括[​slowmoVideo](https://github.com/slowmoVideo/slowmoVideo/) 和[​Butterflow](https://github.com/dthpham/butterflow)。

### 加快/减慢音频
您可以使用 [​atempo](http://ffmpeg.org/ffmpeg-all.html#atempo) 音频过滤器加快或减慢音频。将音频速度加倍：
`ffmpeg -i input.mkv -filter:a "atempo=2.0" -vn output.mkv`
atempo 过滤器仅限于使用 0.5 到 2.0 之间的值（因此它可以将其减慢到不低于原始速度的一半，并且加速不超过输入的两倍）。如果需要，您可以通过将多个 atempo 过滤器串在一起来绕过此限制。以下是音频速度的四倍：
`ffmpeg -i input.mkv -filter:a "atempo=2.0,atempo=2.0" -vn output.mkv`
使用复杂的过滤器图，您可以同时加速视频和音频：
`ffmpeg -i input.mkv -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" output.mkv`
使用上面的原始比特流方法示例，不需要复杂的过滤器图。您可以通过以下方式同时减慢 30 fps 的视频和音频：
`ffmpeg -fflags +genpts -r 15 -i raw.h264 -i input.mp4 -map 0:v -c:v copy -map 1:a -af atempo=0.5 -movflags faststart output.mp4`