NPU模拟器，我们称为cmodel

它的功能是在x86上模拟GX8010（GX8008）snpu或者npu对tensorflow op的运算操作

我们使用杭州国芯提供的gxdnn编译器，把tensorflow模型编译成npu模型后，就可以在cmodel上进行功能正确性的验证

---

开发流程一般如下：

准备TensorFlow生成的PB文件，和其对应的CKPT文件

通过TensorFlow提供的freeze\_graph.py将PB与CKPT合并，生成新的PB文件

通过命令行直接编译：gxnpuc xxx.yaml

xxx.yaml代表你自己定义的名字

如果出现编译错误，一般是模型使用的op我们还没有支持，请反馈给我们

编译成功后，使用cmodel验证模型的输入输出是否正确

验证成功后，加载到开发板上，通过snpu或者npu运行，可以加时间进行打印，看看运行的效率如何



[  
](https://13421398942.gitbooks.io/gx8010_npu/content/di-2-zhang-npu-mo-ni-qi-shi-yong.html)

