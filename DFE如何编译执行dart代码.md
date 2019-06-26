# Dart 前端如何编译执行 dart 代码

> DFE: Dart Front End, Dart 前端

1. Dfe 的 kernel-service 的启动:

- main (sdk/runtime/bin/main.cc)
  - dart::bin::main (sdk/runtime/bin/main.cc)
  ```c++
  #if !defined(DART_PRECOMPILED_RUNTIME)
    dfe.Init(Options::target_abi_version());
    ...
  #endif
  ```
    - dart::bin::DFE::Init (sdk/runtime/bin/dfe.cc)
      - dart::bin::DFE::InitKernelServiceAndPlatformDills (sdk/runtime/bin/dfe.cc)
        将 dart::bin::DFE::frontend_filename_ 设置为: 
        * out/dart-sdk/lib/_internal/abiversions/$target_abi_version/kernel_service.dill.
        或
        * out/dart-sdk/bin/snapshots/kernel-service.dart.snapshot, 这个文件运行 sdk/utils/kernel-service/BUILD.gn 调用 kernel_service.dill 训练生成的.

  ```c++
    // Initialize the Dart VM.
    Dart_InitializeParams init_params;
    ...
    init_params.create = CreateIsolateAndSetup;
    ...
  #if !defined(DART_PRECOMPILED_RUNTIME)
    init_params.start_kernel_isolate =
        dfe.UseDartFrontend() && dfe.CanUseDartFrontend();
  #else
    ...
  #endif

    error = Dart_Initialize(&init_params);
  ```
    - dart::Dart_Initialize (sdk/runtime/vm/dart_api_impl.cc)
        ```c++
        return Dart::Init(params->vm_snapshot_data, params->vm_snapshot_instructions,
                        params->create, params->shutdown, params->cleanup,
                        params->thread_exit, params->file_open, params->file_read,
                        params->file_write, params->file_close,
                        params->entropy_source, params->get_service_assets,
                        params->start_kernel_isolate);
        ```
      - dart::Dart::Init (sdk/runtime/vm/dart.cc)
        - dart::KernelIsolate::Run (sdk/runtime/vm/kernel_isolate.cc)
        ```c++
        bool task_started = Dart::thread_pool()->Run(new RunKernelTask());
        ```
          - dart::RunKernelTask::Run (sdk/runtime/vm/kernel_isolate.cc)

