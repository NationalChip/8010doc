# 编译器使用

可以通过命令行直接编译：

`gxnpuc --config=config.yaml`

或者通过python环境运行：

`python gen_model.py config.yaml`

gen\_model.py里的内容为：

> `import npu_compiler`
>
> `import sys`
>
> `config = sys.argv[1]`
>
> `if __name__ == '__main__':`
>
> ```
>     npu_compiler.run(config)
>
> ```

## 编译器的OP支持范围 {#编译器的op支持范围}

编译器通过对Tensorflow的OP解析来支持各种运算，目前支持的OP的列表如下：

* "Placeholder"
* "Const"
* "Identity"
* "MatMul"
* "Add"
* "Sub"
* "Mul"
* "Div"
* "Softmax"
* "Sigmoid"
* "Tanh"
* "Relu"
* "Shape"
* "Fill"
* "StridedSlice"
* "Concat"
* "BiasAdd"
* "Split"
* "Pack"
* "Conv2D"
* "MaxPool"
* "ConcatV2"

如遇到不支持的OP，请随时和我们联系！我们会尽快添加！

