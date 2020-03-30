---
title: Azure 串列主控台主動 GRUB 配置*微軟文檔
description: 跨各種分發配置 GRUB，允許在 Azure 虛擬機器中訪問單個使用者和復原模式。
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186925"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>主動確保您能夠訪問 GRUB 和 sysrq，可以節省大量停機時間

在大多數情況下，訪問串列主控台和 GRUB 將縮短 IaaS Linux 虛擬機器的恢復時間。 GRUB 提供恢復選項，否則恢復 VM 需要更長時間。 


執行 VM 恢復的原因很多，可以歸因於以下情況：

   - 損壞的檔案系統/內核/MBR（主開機記錄）
   - 內核升級失敗
   - 錯誤的 GRUB 內核參數
   - 不正確的 fstab 配置
   - 防火牆配置
   - 丟失的密碼
   - 混亂 sshd 設定檔
   - 網路設定

 [此處](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)詳述的許多其他方案

驗證是否可以訪問 AZURE 中部署的 VM 上的 GRUB 和串列主控台。 

如果您是串列主控台的新增功能，請參閱[此連結](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/)。

> [!TIP]
> 確保在進行更改之前對檔進行備份

觀看下面的此視頻，瞭解如何在您訪問 GRUB 後快速恢復 Linux VM

[恢復 Linux VM 視頻](https://youtu.be/KevOc3d_SG4)

有許多方法可以説明恢復 Linux VM。 在雲環境中，此過程具有挑戰性。
在工具和功能方面不斷取得進展，以確保服務得到快速恢復。

使用 Azure 串列主控台，您可以像在系統主控台上一樣與 Linux VM 進行交互。

您可以操作許多設定檔，包括內核的啟動方式。 

經驗更豐富的 Linux/Unix sys 管理員將欣賞通過 Azure 串列主控台訪問**的單一使用者**和**緊急模式**，使許多恢復方案的磁片交換和 VM 刪除變得多餘。

恢復方法取決於遇到的問題，例如丟失或放錯位置的密碼可以通過 Azure 門戶選項 ->**重置密碼**重置。 **重置密碼**功能稱為擴展，並與 Linux 來賓代理通信。

其他擴展（如自訂腳本）可用，但這些選項要求 Linux **waagent**處於正常運行狀態，這種情況並非總是如此。

![代理狀態](./media/virtual-machines-serial-console/agent-status.png)


確保您有權訪問 Azure 串列主控台和 GRUB 意味著密碼更改或不正確的配置可以在幾分鐘而不是幾小時內得到糾正。 如果磁片上有多個內核在主內核損壞的情況下，甚至可以強制 VM 從備用內核啟動。

![多內核](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>建議的恢復方法順序：

- Azure 串列主控台

- 磁片交換 – 可以使用：

   - [電源外殼恢復腳本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash 恢復腳本](https://github.com/sribs/azure-support-scripts)

- 傳統方法

## <a name="disk-swap-video"></a>磁片交換視頻：

如果您無法訪問 GRUB 觀看[此](https://youtu.be/m5t0GZ5oGAc)視頻並查看，如何輕鬆自動執行磁片交換過程以恢復 VM

## <a name="challenges"></a>挑戰：

並非所有 Linux Azure VM 在預設情況下都配置為 GRUB 訪問，它們也沒有配置為使用 sysrq 命令中斷。 某些較舊的發行版本（如 SLES 11）未配置為在 Azure 串列主控台中顯示登錄提示

在本文中，我們將回顧各種 Linux 發行版本和文檔配置，瞭解如何使 GRUB 可用。




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>如何配置 Linux VM 以接受 SysRq 金鑰
預設情況下，sysrq 金鑰在某些較新的 Linux 發行版本上啟用，儘管在另一些版本上，它可能配置為僅接受某些 SysRq 函數的值。
在較舊的發行版本上，它可能被完全禁用。

SysRq 功能可用於直接從 Azure 串列主控台重新開機崩潰或掛起的 VM，這也有助於獲得對 GRUB 功能表的訪問，或者從其他門戶視窗重新開機 VM 或 ssh 會話可能會斷開當前主控台連接因此，即將到期的 GRUB 超時，用於顯示 GRUB 功能表。
必須將 VM 配置為接受內核參數的值 1，該值啟用 sysrq 或 128 的所有功能，這允許重新開機/關閉電源


[啟用 sysrq 視頻](https://youtu.be/0doqFRrHz_Mc)


要將 VM 配置為通過 Azure 門戶上的 SysRq 命令接受重新開機，您需要為內核參數內核設置值 1。

為此配置要保留重新開機，請向檔**sysctl.conf**添加一個條目

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

動態配置內核參數

`sysctl -w kernel.sysrq=1`

如果沒有**根**訪問或 sudo 已損壞，則無法從 shell 提示符配置 sysrq。

在這種情況下，可以使用 Azure 門戶啟用 sysrq。 如果**sudoers.d/waagent**檔已損壞或已被刪除，此方法可能是有益的。

使用 Azure 門戶操作 -> 運行命令 -> RunShellScript 功能，需要 waagent 過程正常運行，然後可以注入此命令以啟用 sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

如下所示：![啟用 sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

完成後，您可以嘗試訪問**sysrq，** 並應看到可以重新開機。

![啟用 sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

選擇**重新開機**並**發送 SysRq**命令

![啟用 sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

系統應記錄重置消息，如

![啟用 sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>烏本圖 GRUB 配置

預設情況下，您應該能夠在 VM 啟動期間按住**Esc**鍵來訪問 GRUB，如果未顯示 GRUB 功能表，則可以使用這些選項之一強制並將 GRUB 功能表保留在螢幕上。

**選項 1** - 強制 GRUB 顯示在螢幕上 

更新檔 /etc/預設/grub.d/50-雲-設置.cfg，使 GRUB 功能表在螢幕上保留指定的 TIMEOUT。
您無需命中**Esc，** 因為 GRUB 將立即顯示

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**選項 2** - 允許在啟動前按下**Esc**

通過更改檔 /etc/預設/grub 並觀察 3 秒超時以命中**Esc，** 可能會遇到類似的行為


注釋掉這兩行：

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
並添加此行：

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>烏本圖 12\.04

Ubuntu 12.04 將允許訪問串列主控台，但無法提供交互功能。 **登錄：** 看不到提示


對於 12.04 獲取**登錄名：** 提示：
1. 創建名為 /etc/init/ttyS0.conf 的檔，其中包含以下文本：

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. 要求暴發戶開始蓋蒂     
    ```
    sudo start ttyS0
    ```
 
在此處[可以找到為](https://help.ubuntu.com/community/SerialConsoleHowto)Ubuntu 版本配置串列主控台所需的設置

## <a name="ubuntu-recovery-mode"></a>烏本圖復原模式

Ubuntu 可通過 GRUB 提供其他恢復和清理選項，但僅在相應地配置內核參數時才能訪問這些設置。
未能配置此內核啟動參數將強制將恢復功能表發送到 Azure 診斷，而不是發送到 Azure 串列主控台。
您可以按照以下步驟訪問 Ubuntu 恢復功能表：

中斷引導過程並訪問 GRUB 功能表

選擇 Ubuntu 的高級選項，然後按 Enter

![烏本圖茨1](./media/virtual-machines-serial-console/ubunturec1.png)

選擇顯示*線（復原模式）* 不要按輸入，但按"e"

![烏本圖茨2](./media/virtual-machines-serial-console/ubunturec2.png)

找到將載入內核的行，並將最後一個**參數無模式集**替換為**主控台_ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![烏本圖茨3](./media/virtual-machines-serial-console/ubunturec3.png)

按**Ctrl-x**啟動並載入內核。
如果一切順利，您將看到這些附加選項，這可以説明執行其他恢復選項

![烏本圖茨4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>紅帽 GRUB 配置

## <a name="red-hat-74-grub-configuration"></a>紅帽\.7\+ 4 GRUB 配置
這些版本的預設 /etc/預設/grub 配置配置已充分配置

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

啟用 SysRq 金鑰

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>紅帽\.7 2\.和 7 3 GRUB 配置
要修改的檔是 /etc/預設/grub = 預設配置如下所示：：

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

在 /etc/default/grub 中更改以下行

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

此外，還會添加此行：

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/預設/grub 現在應類似于此示例：

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
使用完成和更新 grub 配置

`grub2-mkconfig -o /boot/grub2/grub.cfg`

設置 SysRq 內核參數：

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

或者，您也可以在 shell 中或通過 Run 命令使用單行配置 GRUB 和 SysRq。 在運行此命令之前備份檔案：


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>紅帽\.6 x GRUB 配置
要修改的檔是 /boot/grub/grub.conf。 該`timeout`值將確定 GRUB 的顯示時間。

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


最後一行*終端 [超時] 5 串列主控台*將通過添加 5 秒的提示顯示**按任意鍵繼續**，進一步增加**GRUB**超時。

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB 功能表應顯示在螢幕上，用於配置的超時=15，而無需按 Esc。請確保在瀏覽器中的主控台中按一下以啟動功能表並選擇所需的內核

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
要麼根據官方文檔使用 yast 引導載入[程式](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

或添加/更改 /etc/default/grub以下參數：

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
驗證 ttys0 是否用於GRUB_CMDLINE_LINUX或GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

重新創建 grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
串列主控台顯示並顯示引導消息，但不顯示**登錄名：** 提示

在 VM 中打開 ssh 會話，並通過取消注釋此行來更新檔 **/etc/inittab：**

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

下一個運行命令 

`telinit q`

要啟用 GRUB，應對 /boot/grub/menu.lst 進行以下更改 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 此配置將使消息 **"按任意鍵"繼續**顯示在主控台上 5 秒鐘 

然後，它將再顯示 GRUB 功能表 5 秒 - 通過按下向下箭頭，您將中斷計數器並選擇要啟動的內核，以追加單使用者模式的關鍵字**單**一，該內核需要設置根密碼。

追加命令**init_/bin/bash**將載入內核，但可確保 init 程式替換為 bash 外殼。

無需輸入密碼即可訪問 shell。 然後，您可以繼續更新 Linux 帳戶的密碼或進行其他配置更改。


## <a name="force-the-kernel-to-a-bash-prompt"></a>強制內核到 bash 提示
訪問 GRUB 允許您中斷初始化過程，這種交互對於許多恢復過程非常有用。
如果您沒有根密碼，並且單個使用者要求您具有根密碼，則可以啟動內核，用 bash 提示替換 init 程式 - 此中斷可以通過將 init_/bin/bash 追加到內核引導行來實現

![bash1](./media/virtual-machines-serial-console/bash1.png)

使用 命令重新安裝 /（根）檔案系統 RW

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


現在，您可以執行根密碼更改或許多其他 Linux 配置更改

![bash3](./media/virtual-machines-serial-console/bash3.png)

使用 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>單使用者模式

或者，您可能需要在單個使用者或緊急模式下訪問 VM。 選擇您希望使用方向鍵啟動或中斷的內核。
通過將關鍵字**單**或**1**追加到內核引導行，輸入所需的模式。 在 RHEL 系統上，您還可以追加**rd.break**。

有關如何訪問單一使用者模式的詳細資訊，請參閱[此文檔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>後續步驟
瞭解有關[Azure 串列主控台]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)的資訊