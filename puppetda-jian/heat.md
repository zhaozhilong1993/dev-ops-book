# Heat

heat是OpenStack用作编排的一个组件。代码中的实现，主要还是通过调用各个组件的SDK来实现对应的资源的创建。在面向用户使用端，还是通过使用yaml文件来创建对应的资源。

heat的初体验：

```
# vim start_vm.yaml

# heat stack-create -f start_vm.yaml my_first_stack
```



