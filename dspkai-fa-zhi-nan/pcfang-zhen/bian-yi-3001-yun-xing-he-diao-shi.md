# 编译、运行和调试 {#3-编译、运行和调试}

## 编译和运行

```
cd tb // 
make //编译
xt-run --turbo ./output/dsp.elf ./audio_data // 程序默认开启了aec算法，程序执行完后，你会看到目录 audio_data下多了一个 output.wav，这个是第0录mic经过aec后的音频输出。
```

## gdb调试

修改 Makefile，将优化级别改成 -O0

```
xt-gdb --args ./output/dsp.elf ./audio_data
```

## 生成 profile

注意要先确保工程编译通过

```
make profile
```



