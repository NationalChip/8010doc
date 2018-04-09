# 工程目录结构 {#4-工程目录结构}

![](/assets/%E9%80%89%E5%8C%BA_114.png)

vsp\_simulate 的目录结构如上图所示。

---

## 目录简介 {#41-目录简介}

* **tb/algorithm/**: 用来放置对应的算法库，工程默认提供了 fft 和 logfbank 定点的实现，算法工程师即将调试和优化的算法（比如：aec、vad、doa等）放在这里。
* **tb/arch/xtensa/lsp-sim/**: 是用来生成 lsp 的，其功能类似于 ld 链接脚本，算法开发者可以不用关心它。
* **tb/audio\_data/**: 用来存放待处理的音频 wave 文件，注意阅读里面的 readme。
* **tb/common/**: 将 wave 封装成 context 的核心代码
* **tb/include/**: 头文件，context 结构的定义在 vsp\_context.h 中，与 vsp 工程的头文件是一致的。
* **tb/src/**: 算法实现，最重要的两个文件是 vsp\_process.c 和 vsp\_algorithm\_implement.c。其中 vsp\_process.c 里面有一个所有算法函数入口`int VspProcessActive(VSP_CONTEXT *context)`，这个文件在 vsp 工程中同样存在，也就是说后面上板子调试算法时，可以直接将此文件的实现平移到 vsp 工程里面去；vsp\_algorithm\_implement.c 是对 tb/algorithm/ 算法的实现。

---

## 目录间的调用关系 {#42-目录间的调用关系}

1. **关系流程**： vsp\_main.c --&gt;&gt;vsp\_context.c --&gt;&gt;vsp\_process.c --&gt;&gt;vsp\_algorithm\_implemet.c --&gt;&gt;algorithm算法
2. **细节描述**：

* vsp\_main.c: 工程的入口
* vsp\_context.c: wave 文件的读取 和 context 的封装实现
* vsp\_process.c: dsp 算法入口
* vsp\_algorithm\_implement.c:



