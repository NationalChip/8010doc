# OpenWRT启动流程分析

OpenWRT针对主线内核中不存在的各种硬件和独特应用程序提供了许多补丁，补丁的分析在Issue \#86254。这些补丁位于

openwrt/target/linux/generic/patches-&lt;patchver&gt;中。其中，无论选择哪个版本的Linux，都可以找到一个名为921-

use\_preinit\_as\_init.patch 的补丁，它在init进程列表的第一个位置注入OpenWRT init进程。patch内容如下：

--- a/init/main.c

+++ b/init/main.c

@@ -964,7 +964,8 @@ static int \_\_ref kernel\_init\(void \*unuse

panic\("Requested init %s failed \(error %d\).",

execute\_command, ret\);

}

if \(!try\_to\_run\_init\_process\("/sbin/init"\) \|\|

if \(!try\_to\_run\_init\_process\("/etc/preinit"\) \|\|

!try\_to\_run\_init\_process\("/sbin/init"\) \|\|

!try\_to\_run\_init\_process\("/etc/init"\) \|\|

!try\_to\_run\_init\_process\("/bin/init"\) \|\|

!try\_to\_run\_init\_process\("/bin/sh"\)\)

---

/sbin/init应用程序主函数位于procd/initd/init.c 文件。大致流程如下：

![](/assets/openwrt_sbin_init.png)

