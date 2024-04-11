- no_std
  - 不依赖标准库的编译环境
  - 比如嵌入式系统、操作系统内核、裸机编程等
- LibAFL仓库有构建baby_no_std fuzzer的example
  - https://github.com/AFLplusplus/LibAFL
  - fuzzers/baby_no_std
  - 运行配置参考李羽飞的记录
    - https://github.com/LearningOS/osbiglab-2024s-fuzzingkernel/blob/main/reports/week6.md
  - 运行成功
    - ![image-20240411212129649](C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240411212129649.png)
    - 找到了使崩溃的input

- LibAFL的book

  - https://aflplus.plus/libafl-book/advanced_features/no_std.html

  - 在`no_std`环境中使用自定义的时间戳支持来配合`LibAFL`。在项目的任何地方，你需要实现`external_current_millis`函数，该函数返回以毫秒为单位的当前时间戳。

  - example中的实现

  - ```rust
    #[no_mangle]
    pub extern "C" fn external_current_millis() -> u64 {
        // TODO: use "real" time here
        1000
    }
    ```

    

