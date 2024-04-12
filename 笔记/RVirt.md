- RVirt是一个用于RISC-V架构的S-mode trap-and-emulate hypervisor
  - 跟随仓库README可启动一个带有RVirt的QEMU示例，并在其中运行Linux（提前下载了镜像vmlinux和stage4-disk.img）
  - 启动遇到问题（待解决）

### RVirt踩坑记录

启动遇到的问题是

![image-20240412111919930](C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240412111919930.png)

一开始通过研究和改Makefile地址做了些尝试，也尝试查看Rvirt和地址相关源码（Makefile第一步是编译rust项目生成rvirt)

观察到Rvirt是一个五年前的项目，以及搜索报错信息尝试理解，最终猜测应该是和新版本qemu冲突（冲突的两段地址其中一段来自qemu）



尝试使用qemu4.0.0

```commandline
 $ wget https://download.qemu.org/qemu-4.0.0.tar.xz
 $ tar xvJf qemu-4.0.0.tar.xz
 $ cd qemu-4.0.0/
 $ ./configure --target-list=riscv64-softmmu,riscv64-linux-user --prefix=/opt/qemu4.0.0
 $ make -j 4
 $ sudo make install
```

这个过程会出现错误

![image-20240412115030388](C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240412115030388.png)

搜索后可找到patch方案，我们直接手动执行

```
$ cd /opt/qemu4.0.0/bin
$ vim syscall.c
```

![image-20240412115244454](C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240412115244454.png)

增加上这样一个include，再次执行 `make`，qemu4.0.0即完成安装



因为一般都装了新版的qemu，所以我们可以修改`Rvirt/Makefile` ，将qemu改成绝对路径调用

![image-20240412115425849](C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240412115425849.png)

如图，我改成 `/opt/qemu4.0.0/bin/qemu-system-riscv64`



现在可以跑起来

![image-20240412115527784](C:\Users\qi\AppData\Roaming\Typora\typora-user-images\image-20240412115527784.png)



