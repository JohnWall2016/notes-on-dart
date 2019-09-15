# kernel-service 编译 dart 代码

> kernel-service: 是将 dart 代码编译成 kernel IR (中间表示) 的后台服务. 源代码位置在 sdk/pkg/vm/bin/kernel_service.dart. dart jit 后端只能运行 kernel IR, kernel IR 保存的文件一般以 dill 作为后缀.

## 开启 DFE 的 verbose (输出详细信息) 模式
```bash
dart -DDFE_VERBOSE=true test/lang_test.dart
```
