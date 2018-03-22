# xt-size 分析dsp需要的内存大小 {#7-xt-size-分析dsp需要的内存大小}

size 是 GNU Binutils 之一，它能为列出 elf 文件的每个段的大小。我们的 dsp 工具链也集成了此工具叫 xt-size。

```
$ xt-size output/dsp.elf
   text       data        bss        dec        hex    filename
 154801       3056      17640     175497      2ad89    output/dsp.elf

$ xt-size --format=xtensa output/dsp.elf
TYPE          text     rodata       data        bss        dec    hex    filename
code        103253          0          0          0     103253      19355    output/dsp.elf
literal       1620          0          0          0       1620        654    output/dsp.elf
other         3164      46764       3056      17640      70624      113e0    output/dsp.elf
Total       108037      46764       3056      17640     175497      2ad89    output/dsp.elf

```

上面的 text、rodata、data 段会打包在dsp.bin中；运行时，除了那 3 个段外，还有一个 bss 也会驻留在内存中。所以最终dsp程序至少需要这 4 个段的 size 和。

