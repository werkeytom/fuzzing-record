## LibAFL QEMU: A Library for Fuzzing-oriented Emulation

### 问题提出

- 过去基于QEMU的模拟测试改进无法与上游版本保持同步
  - Qiling或PANDA，都类似于基于自己的需求修改出一个“QEMU分支”
  - 而如果要为特定的需求构建自定义QEMU分支，研究人员要被困在旧版本中，或花费相当多的工程努力
  - **需要一个灵活的解决方案**，不需要用户自行修补QEMU，而是通过对上游代码库的最小变化来轻松保持更新
- LibAFL QEMU
  - 用Rust编写的库
  - 将QEMU包装为一个静态库，大部分代码库用于工具化(instrumentation)和模糊测试，脱离QEMU的主源代码树，以便于维护
  - 与LibAFL集成，是一个用于模糊测试开发的模块化框架

- 与AFL++ QEMU相比
  - 能使用可定制接口进行模糊器开发
  - 在速度、覆盖率和漏洞发现上也更优
- 工作主要贡献
  - 开发了一个针对模糊测试的仿真库，具有强大的接口和许多不同的内置功能
  - 提供了两个用例，演示如何对Android库和Windows内核驱动程序进行模糊测试
  - 将框架和示例发布为开源软件（https://github.com/AFLplusplus/libafl_qemu_artifacts）

### 设计

- QEMU分支版本与上游的主分支保持最新
  - https://github.com/AFLplusplus/qemu-libafl-bridge
- fuzzer只需将libafl_qemu作为Cargo.toml的依赖项
  - 只需像其他crate一样使用cargo build
  - 可以自动构建和链接QEMU
- <img src="C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240408163002735.png" alt="image-20240408163002735" style="zoom:50%;" />

- 根据设计将主要库的功能分为3个层次的抽象
  - 低级API：由模拟器方面暴露
  - 高级API：建立在低级API之上的抽象层，提供钩子功能，可以与LibAFL模糊测试器的状态和其他钩子交互
  - 针对模糊测试的功能：建立在低级和高级API之上，实现预制的仪器化，例如内存检测或覆盖率跟踪，这些功能对于构建模糊测试器而不需要从头编写任何钩子非常有用

