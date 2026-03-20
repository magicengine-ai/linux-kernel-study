# 音频实践应用

> 录音/播放/处理完整指南

---

## 📋 音频录制

### arecord 命令

```bash
# 录制 WAV 文件
arecord -f cd -d 10 recording.wav

# 指定格式
arecord -f S16_LE -r 48000 -c 2 recording.wav

# 列出设备
arecord -l
arecord -L

# 录音并压缩
arecord -f cd | lame - output.mp3
```

---

## 🔧 音频播放

### aplay 命令

```bash
# 播放 WAV 文件
aplay audio.wav

# 指定设备
aplay -D hw:0,0 audio.wav

# 列出设备
aplay -l
```

### mpv 播放器

```bash
# 播放音频
mpv audio.mp3

# 播放列表
mpv --playlist=playlist.m3u

# 均衡器
mpv --af=equalizer=2:5:4:3:2:1:0:-1:-2:-3 audio.mp3
```

---

## 🔧 音频处理

### ffmpeg 音频处理

```bash
# 格式转换
ffmpeg -i input.wav -codec:a libopus output.opus

# 提取音频
ffmpeg -i video.mp4 -vn -codec:a copy audio.m4a

# 音频混合
ffmpeg -i music.mp3 -i voice.wav \
    -filter_complex amix=inputs=2:duration=first \
    -codec:a libmp3lame output.mp3

# 降噪
ffmpeg -i input.wav -af highpass=f=200,lowpass=f=3000 output.wav
```

### sox 音频工具

```bash
# 格式转换
sox input.wav output.mp3

# 音频效果
sox input.wav output.wav bass +10 treble -5

# 录音
sox -d recording.wav

# 合并音频
sox file1.wav file2.wav file3.wav output.wav

# 剪切音频
sox input.wav output.wav trim 10 20
```

---

## 🔧 音频分析

```bash
# 查看音频信息
ffprobe -i audio.mp3

# 频谱分析
sox audio.wav -n spectrogram

# 音量分析
ffmpeg -i audio.mp3 -af volumedetect -f null /dev/null
```

---

## ✅ 总结

音频实践核心：

1. **录制** - arecord 命令
2. **播放** - aplay/mpv
3. **处理** - ffmpeg/sox
4. **分析** - ffprobe/sox

---

*学习笔记由 全栈工程师 维护*
