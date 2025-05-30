.. include:: ../disclaimer-zh_CN.rst

:Original: Documentation/admin-guide/README.rst

:译者:

 吴想成 Wu XiangCheng <bobwxc@email.cn>

Linux内核6.x版本 <http://kernel.org/>
=========================================

以下是Linux版本6的发行注记。仔细阅读它们，
它们会告诉你这些都是什么，解释如何安装内核，以及遇到问题时该如何做。

什么是Linux？
---------------

  Linux是Unix操作系统的克隆版本，由Linus Torvalds在一个松散的网络黑客
  （Hacker，无贬义）团队的帮助下从头开始编写。它旨在实现兼容POSIX和
  单一UNIX规范。

  它具有在现代成熟的Unix中应当具有的所有功能，包括真正的多任务处理、虚拟内存、
  共享库、按需加载、共享的写时拷贝（COW）可执行文件、恰当的内存管理以及包括
  IPv4和IPv6在内的复合网络栈。

  Linux在GNU通用公共许可证，版本2（GNU GPLv2）下分发，详见随附的COPYING文件。

它能在什么样的硬件上运行？
-----------------------------

  虽然Linux最初是为32位的x86 PC机（386或更高版本）开发的，但今天它也能运行在
  （至少）Compaq Alpha AXP、Sun SPARC与UltraSPARC、Motorola 68000、PowerPC、
  PowerPC64、ARM、Hitachi SuperH、Cell、IBM S/390、MIPS、HP PA-RISC、Intel 
  IA-64、DEC VAX、AMD x86-64 Xtensa和ARC架构上。

  Linux很容易移植到大多数通用的32位或64位体系架构，只要它们有一个分页内存管理
  单元（PMMU）和一个移植的GNU C编译器（gcc；GNU Compiler Collection，GCC的一
  部分）。Linux也被移植到许多没有PMMU的体系架构中，尽管功能显然受到了一定的
  限制。
  Linux也被移植到了其自己上。现在可以将内核作为用户空间应用程序运行——这被
  称为用户模式Linux（UML）。

文档
-----
因特网上和书籍上都有大量的电子文档，既有Linux专属文档，也有与一般UNIX问题相关
的文档。我建议在任何Linux FTP站点上查找LDP（Linux文档项目）书籍的文档子目录。
本自述文件并不是关于系统的文档：有更好的可用资源。

 - 因特网上和书籍上都有大量的（电子）文档，既有Linux专属文档，也有与普通
   UNIX问题相关的文档。我建议在任何有LDP（Linux文档项目）书籍的Linux FTP
   站点上查找文档子目录。本自述文件并不是关于系统的文档：有更好的可用资源。

 - 文档/子目录中有各种自述文件：例如，这些文件通常包含一些特定驱动程序的
   内核安装说明。请阅读
   :ref:`Documentation/process/changes.rst <changes>` 文件，它包含了升级内核
   可能会导致的问题的相关信息。

安装内核源代码
---------------

 - 如果您要安装完整的源代码，请把内核tar档案包放在您有权限的目录中（例如您
   的主目录）并将其解包::

     xz -cd linux-6.x.tar.xz | tar xvf -

   将“X”替换成最新内核的版本号。

   【不要】使用 /usr/src/linux 目录！这里有一组库头文件使用的内核头文件
   （通常是不完整的）。它们应该与库匹配，而不是被内核的变化搞得一团糟。

 - 您还可以通过打补丁在6.x版本之间升级。补丁以xz格式分发。要通过打补丁进行
   安装，请获取所有较新的补丁文件，进入内核源代码（linux-6.x）的目录并
   执行::

     xz -cd ../patch-6.x.xz | patch -p1

   请【按顺序】替换所有大于当前源代码树版本的“x”，这样就可以了。您可能想要
   删除备份文件（文件名类似xxx~ 或 xxx.orig)，并确保没有失败的补丁（文件名
   类似xxx# 或 xxx.rej）。如果有，不是你就是我犯了错误。

   与6.x内核的补丁不同，6.x.y内核（也称为稳定版内核）的补丁不是增量的，而是
   直接应用于基本的6.x内核。例如，如果您的基本内核是6.0，并且希望应用6.0.3
   补丁，则不应先应用6.0.1和6.0.2的补丁。类似地，如果您运行的是6.0.2内核，
   并且希望跳转到6.0.3，那么在应用6.0.3补丁之前，必须首先撤销6.0.2补丁
   （即patch -R）。更多关于这方面的内容，请阅读
   :ref:`Documentation/process/applying-patches.rst <applying_patches>` 。

   或者，脚本 patch-kernel 可以用来自动化这个过程。它能确定当前内核版本并
   应用找到的所有补丁::

     linux/scripts/patch-kernel linux

   上面命令中的第一个参数是内核源代码的位置。补丁是在当前目录应用的，但是
   可以将另一个目录指定为第二个参数。

 - 确保没有过时的 .o 文件和依赖项::

     cd linux
     make mrproper

   现在您应该已经正确安装了源代码。

软件要求
---------

   编译和运行6.x内核需要各种软件包的最新版本。请参考
   :ref:`Documentation/process/changes.rst <changes>`
   来了解最低版本要求以及如何升级软件包。请注意，使用过旧版本的这些包可能会
   导致很难追踪的间接错误，因此不要以为在生成或操作过程中出现明显问题时可以
   只更新包。

为内核建立目录
---------------

   编译内核时，默认情况下所有输出文件都将与内核源代码放在一起。使用
   ``make O=output/dir`` 选项可以为输出文件（包括 .config）指定备用位置。
   例如::

     kernel source code: /usr/src/linux-6.x
     build directory:    /home/name/build/kernel

   要配置和构建内核，请使用::

     cd /usr/src/linux-6.x
     make O=/home/name/build/kernel menuconfig
     make O=/home/name/build/kernel
     sudo make O=/home/name/build/kernel modules_install install

   请注意：如果使用了 ``O=output/dir`` 选项，那么它必须用于make的所有调用。

配置内核
---------

   即使只升级一个小版本，也不要跳过此步骤。每个版本中都会添加新的配置选项，
   如果配置文件没有按预定设置，就会出现奇怪的问题。如果您想以最少的工作量
   将现有配置升级到新版本，请使用 ``make oldconfig`` ，它只会询问您新配置
   选项的答案。

 - 其他配置命令包括::

     "make config"      纯文本界面。

     "make menuconfig"  基于文本的彩色菜单、选项列表和对话框。

     "make nconfig"     增强的基于文本的彩色菜单。

     "make xconfig"     基于Qt的配置工具。

     "make gconfig"     基于GTK的配置工具。

     "make oldconfig"   基于现有的 ./.config 文件选择所有选项，并询问
                        新配置选项。

     "make olddefconfig"
                        类似上一个，但不询问直接将新选项设置为默认值。

     "make defconfig"   根据体系架构，使用arch/$arch/defconfig或
                        arch/$arch/configs/${PLATFORM}_defconfig中的
                        默认选项值创建./.config文件。

     "make ${PLATFORM}_defconfig"
                        使用arch/$arch/configs/${PLATFORM}_defconfig中
                        的默认选项值创建一个./.config文件。
                        用“make help”来获取您体系架构中所有可用平台的列表。

     "make allyesconfig"
                        通过尽可能将选项值设置为“y”，创建一个
                        ./.config文件。

     "make allmodconfig"
                        通过尽可能将选项值设置为“m”，创建一个
                        ./.config文件。

     "make allnoconfig" 通过尽可能将选项值设置为“n”，创建一个
                        ./.config文件。

     "make randconfig"  通过随机设置选项值来创建./.config文件。

     "make localmodconfig" 基于当前配置和加载的模块（lsmod）创建配置。禁用
                           已加载的模块不需要的任何模块选项。

                           要为另一台计算机创建localmodconfig，请将该计算机
                           的lsmod存储到一个文件中，并将其作为lsmod参数传入。

                           此外，通过在参数LMC_KEEP中指定模块的路径，可以将
                           模块保留在某些文件夹或kconfig文件中。

                   target$ lsmod > /tmp/mylsmod
                   target$ scp /tmp/mylsmod host:/tmp

                   host$ make LSMOD=/tmp/mylsmod \
                           LMC_KEEP="drivers/usb:drivers/gpu:fs" \
                           localmodconfig

                           上述方法在交叉编译时也适用。

     "make localyesconfig" 与localmodconfig类似，只是它会将所有模块选项转换
                           为内置（=y）。你可以同时通过LMC_KEEP保留模块。

     "make kvm_guest.config"
                        为kvm客户机内核支持启用其他选项。

     "make xen.config"  为xen dom0客户机内核支持启用其他选项。

     "make tinyconfig"  配置尽可能小的内核。

   更多关于使用Linux内核配置工具的信息，见文档
   Documentation/kbuild/kconfig.rst。

 - ``make config`` 注意事项:

    - 包含不必要的驱动程序会使内核变大，并且在某些情况下会导致问题：
      探测不存在的控制器卡可能会混淆其他控制器。

    - 如果存在协处理器，则编译了数学仿真的内核仍将使用协处理器：在
      这种情况下，数学仿真永远不会被使用。内核会稍微大一点，但不管
      是否有数学协处理器，都可以在不同的机器上工作。

    - “kernel hacking”配置细节通常会导致更大或更慢的内核（或两者
      兼而有之），甚至可以通过配置一些例程来主动尝试破坏坏代码以发现
      内核问题，从而降低内核的稳定性（kmalloc()）。因此，您可能应该
      用于研究“开发”、“实验”或“调试”特性相关问题。

编译内核
---------

 - 确保您至少有gcc 5.1可用。
   有关更多信息，请参阅 :ref:`Documentation/process/changes.rst <changes>` 。

 - 执行 ``make`` 来创建压缩内核映像。如果您安装了lilo以适配内核makefile，
   那么也可以进行 ``make install`` ，但是您可能需要先检查特定的lilo设置。

   实际安装必须以root身份执行，但任何正常构建都不需要。
   无须徒然使用root身份。

 - 如果您将内核的任何部分配置为模块，那么还必须执行 ``make modules_install`` 。

 - 详细的内核编译/生成输出：

   通常，内核构建系统在相当安静的模式下运行（但不是完全安静）。但是有时您或
   其他内核开发人员需要看到编译、链接或其他命令的执行过程。为此，可使用
   “verbose（详细）”构建模式。
   向 ``make`` 命令传递 ``V=1`` 来实现，例如::

     make V=1 all

   如需构建系统也给出内个目标重建的愿意，请使用 ``V=2`` 。默认为 ``V=0`` 。

 - 准备一个备份内核以防出错。对于开发版本尤其如此，因为每个新版本都包含
   尚未调试的新代码。也要确保保留与该内核对应的模块的备份。如果要安装
   与工作内核版本号相同的新内核，请在进行 ``make modules_install`` 安装
   之前备份modules目录。

   或者，在编译之前，使用内核配置选项“LOCALVERSION”向常规内核版本附加
   一个唯一的后缀。LOCALVERSION可以在“General Setup”菜单中设置。

 - 为了引导新内核，您需要将内核映像（例如编译后的
   .../linux/arch/x86/boot/bzImage）复制到常规可引导内核的位置。

 - 不再支持在没有LILO等启动装载程序帮助的情况下直接从软盘引导内核。

   如果从硬盘引导Linux，很可能使用LILO，它使用/etc/lilo.conf文件中
   指定的内核映像文件。内核映像文件通常是/vmlinuz、/boot/vmlinuz、
   /bzImage或/boot/bzImage。使用新内核前，请保存旧映像的副本，并复制
   新映像覆盖旧映像。然后您【必须重新运行LILO】来更新加载映射！否则，
   将无法启动新的内核映像。

   重新安装LILO通常需要运行/sbin/LILO。您可能希望编辑/etc/lilo.conf
   文件为旧内核映像指定一个条目（例如/vmlinux.old)防止新的不能正常
   工作。有关更多信息，请参阅LILO文档。

   重新安装LILO之后，您应该就已经准备好了。关闭系统，重新启动，尽情
   享受吧！

   如果需要更改内核映像中的默认根设备、视频模式等，请在适当的地方使用
   启动装载程序的引导选项。无需重新编译内核即可更改这些参数。

 - 使用新内核重新启动并享受它吧。

若遇到问题
-----------

如果您发现了一些可能由于内核缺陷所导致的问题，请参阅：
Documentation/translations/zh_CN/admin-guide/reporting-issues.rst 。

想要理解内核错误报告，请参阅：
Documentation/translations/zh_CN/admin-guide/bug-hunting.rst 。

更多用GDB调试内核的信息，请参阅：
Documentation/translations/zh_CN/dev-tools/gdb-kernel-debugging.rst
和 Documentation/dev-tools/kgdb.rst 。
