---
title: Azure 序列主控台主動式 GRUB 設定 | Microsoft Docs
description: 跨各種散發套件設定 GRUB，以在 Azure 虛擬機器中允許單一使用者和復原模式存取。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360543"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>主動確保您有 GRUB 和 sysrq 的存取權，可以為您省下許多停機時間

在大部分情況下，擁有序列主控台和 GRUB 的存取權可改善 IaaS Linux 虛擬機器的復原時間。 GRUB 會提供復原選項，否則其他方法會花更長的時間才能復原您的 VM。 


執行 VM 復原的原因很多，這些原因可歸納為以是案例：

   - 檔案系統/核心/MBR (主開機記錄) 損毀
   - 核心升級失敗
   - GRUB 核心參數不正確
   - fstab 設定不正確
   - 防火牆設定
   - 遺失密碼
   - sshd 設定檔損壞
   - 網路設定

 [這裡](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)詳述其他許多案例

請確認您可以在部署於 Azure 的 VM 上存取 GRUB 和序列主控台。 

如果您不熟悉序列主控台，請參閱[此連結](./serial-console-linux.md)。

> [!TIP]
> 在進行變更之前，請務必先備份檔案

請觀看下列影片，以了解在擁有 GRUB 存取權之後，要如何快速復原 Linux VM

[復原 Linux VM 影片](https://youtu.be/KevOc3d_SG4)

有數種方法可協助您復原 Linux VM。 在雲端環境中，這一直是很有挑戰性的程序。
工具和功能持續在進步，以確保服務能夠快速復原。

透過 Azure 序列主控台，您可以與 Linux VM 互動，就好像您是在系統的主控台一樣。

您可以操控許多設定檔，包括核心的開機方式。 

較有經驗的 Linux/Unix 系統管理員會很重視可透過 Azure 序列主控台來存取的**單一使用者**和**緊急模式**，其會讓許多復原案例的磁碟交換和 VM 刪除變得很多餘。

復原方法取決於所發生的問題，例如密碼遺失或錯置可以透過 Azure 入口網站選項 > [重設密碼] 來重設。 **重設密碼**功能稱為「擴充功能」，會與 Linux 客體代理程式通訊。

也有其他擴充功能 (例如自訂指令碼) 可供使用，不過，要使用這些選項，Linux 的 **waagent** 必須啟動，而且處於健全狀態 (但情況並非總是這樣)。

![代理程式狀態](./media/virtual-machines-serial-console/agent-status.png)


確保您有權存取 Azure 序列主控台和 GRUB 意味著您可以在幾分鐘內 (而不是花上數小時) 就修正密碼變更或不正確的設定。 在主要核心損毀的情況下，如果您在磁碟上有多個核心，您甚至可以強制讓 VM 從替代核心開機。

![多重核心](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>建議的復原方法順序：

- Azure 序列主控台

- 磁碟交換 - 可透過下列方式來自動進行：

   - [PowerShell 復原指令碼](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash 復原指令碼](https://github.com/sribs/azure-support-scripts)

- 舊版方法

## <a name="disk-swap-video"></a>磁碟交換影片：

如果您無法存取 GRUB，請觀看[此](https://youtu.be/m5t0GZ5oGAc)影片，以了解如何輕鬆地自動進行用來復原您 VM 的磁碟交換程序

## <a name="challenges"></a>挑戰：

並非所有 Linux Azure VM 都已預設 GRUB 存取權，而且也不會都設定為可使用 sysrq 命令來中斷。 某些較舊的散發套件 (例如 SLES 11) 就未設定為在 Azure 序列主控台中顯示登入提示

在本文中，我們將檢閱各種 Linux 散發套件，以及關於如何讓 GRUB 可供使用的文件設定。




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>如何設定 Linux VM 以接受 SysRq 金鑰
根據預設，某些較新的 Linux 散發套件上會啟用 sysrq 金鑰，但其他散發套件則可能會設定為只接受特定 SysRq 功能的值。
在較舊的散發套件上，則可能會完全停用。

SysRq 功能適合用來直接從 Azure 序列主控台將已損毀或沒有回應的 VM 重新開機，此外也有助於取得 GRUB 功能表的存取權，或者，從另一個入口網站視窗或 ssh 工作階段重新啟動 VM 可能會讓目前的主控台連線中斷，而讓用來顯示 GRUB 功能表的 GRUB 逾時過期。
VM 必須設定為接受值為 1 的核心參數，這會啟用 sysrq 或 128 的所有功能，而能夠重新開機/關閉電源


[啟用 sysrq 影片](https://youtu.be/0doqFRrHz_Mc)


若要將 VM 設定為可在 Azure 入口網站上透過 SysRq 命令來接受重新開機，您必須將核心參數 kernel.sysrq 的值設定為 1

若要讓此設定能持續進行重新開機，請在 **sysctl.conf** 檔案中新增一個項目

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

動態設定核心參數

`sysctl -w kernel.sysrq=1`

如果您沒有**根**存取權或 sudo 已損壞，則無法從殼層提示字元設定 sysrq。

在此案例中，您可以使用 Azure 入口網站來啟用 sysrq。 如果 **sudoers.d/waagent** 檔案已損壞或遭到刪除，這個方法會有幫助。

使用 Azure 入口網站的 [作業] > [執行命令] > [RunShellScript] 功能需要 waagent 程序處於良好狀態，然後您就可以插入此命令以啟用 sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

如下所示：![啟用 sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

完成後，您就可以嘗試存取 **sysrq**，而且應該會看到重新開機是可行的。

![啟用 sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

選取 [重新開機] 和 [傳送 SysRq] 命令

![啟用 sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

系統應該會記錄重設訊息，例如

![啟用 sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB 設定

根據預設，您應該能夠在 VM 開機期間藉由按住 **Esc** 鍵來存取 GRUB，如果未顯示 GRUB 功能表，則可以使用下列其中一個選項來強制讓 GRUB 功能表出現並留在 Azure 序列主控台的畫面上。

**選項 1** - 強制讓 GRUB 顯示在螢幕上 

更新 /etc/default/grub.d/50-cloudimg-settings.cfg 檔案，讓 GRUB 功能表在螢幕上存留達指定的逾時時間。
您不需要按 **Esc** 鍵，因為 GRUB 會立即顯示

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**選項 2** - 在開機前就考慮到按 **Esc** 這件事

變更 /etc/default/grub 檔案會遇到類似的行為，並請注意僅有 3 秒的逾時時間可供按 **Esc** 鍵


將這兩行註解化：

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
並新增這一行：

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 會允許存取序列主控台，但未提供互動能力。 沒看到**登入:** 提示字元


若要讓 12.04 取得**登入:** 提示字元：
1. 建立名為 /etc/init/ttyS0.conf 的檔案，並於其中包含下列文字：

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

2. 要求 upstart 啟動 getty     
    ```
    sudo start ttyS0
    ```
 
您可以在[這裡](https://help.ubuntu.com/community/SerialConsoleHowto)找到要為 Ubuntu 版本設定序列主控台所需進行的設定

## <a name="ubuntu-recovery-mode"></a>Ubuntu 復原模式

透過 GRUB 可取得 Ubuntu 的其他復原和清除選項，但只有在您相應地設定核心參數時，才可存取這些設定。
若未能設定此核心開機參數，系統會強制將 [復原] 功能表傳送至 Azure 診斷，而不是 Azure 序列主控台。
您可以遵循下列步驟來取得 Ubuntu [復原] 功能表的存取權：

中斷 BOOT 程序並存取 GRUB 功能表

為 Ubuntu 選取 [進階選項]，然後按 Enter 鍵

![螢幕擷取畫面顯示已選取適用于 Ubuntu 的 Advanced 選項的序列主控台。](./media/virtual-machines-serial-console/ubunturec1.png)

選取顯示了 *(recovery mode)* 的那一行，不要按 Enter，而是按 "e"

![螢幕擷取畫面顯示已選取復原模式版本的序列主控台。](./media/virtual-machines-serial-console/ubunturec2.png)

找出會載入核心的那一行，並以目的地 **console=ttyS0** 替換最後一個參數 **nomodeset**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![螢幕擷取畫面顯示具有變更值的序列主控台。](./media/virtual-machines-serial-console/ubunturec3.png)

按 **Ctrl-x** 以啟動並載入核心。
如果一切順利，您就會看到這些額外選項，其可協助您執行其他復原選項

![螢幕擷取畫面顯示 [復原] 功能表上的序列主控台，其提供額外的修復選項。](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB 設定

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB 設定
這些版本上的預設 /etc/default/grub 設定已經過適當設定

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

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 和 7\.3 GRUB 設定
要修改的檔案是 /etc/default/grub - 這是預設設定，其樣貌如下列範例所示：

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

變更 /etc/default/grub 中的下列幾行

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

也請新增這一行：

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub 現在看起來應該會類似下列範例：

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
使用下面這一行來完成和更新 grub 設定

`grub2-mkconfig -o /boot/grub2/grub.cfg`

設定 SysRq 核心參數：

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

或者，您也可以在殼層中或透過「執行命令」，使用單行來設定 GRUB 和 SysRq。 執行此命令之前，請先備份您的檔案：


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB 設定
要修改的檔案是 /boot/grub/grub.conf。 `timeout` 值將決定 GRUB 要顯示多久。

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


最後一行 *terminal –-timeout=5 serial console* 會進一步增加 **GRUB** 逾時時間，其方法是新增會顯示 **Press any key to continue.** 長達 5 秒的提示

![螢幕擷取畫面顯示有輸出的主控台。](./media/virtual-machines-serial-console/rh6-1.png)

不必按下 Esc 鍵，GRUB 功能表應該就會在畫面上出現，持續時間為所設定的逾時值 15。請務必在瀏覽器中按一下主控台，使功能表變成作用中狀態，並選取所需的核心

![螢幕擷取畫面顯示具有兩個 Linux 選項的主控台。](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
按照官方[文件](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)的指示，使用 YaST 開機載入器

或在下列參數中新增/變更 /etc/default/grub：

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
確認 GRUB_CMDLINE_LINUX 或 GRUB_CMDLINE_LINUX_DEFAULT 中使用了 ttys0

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

重新建立 grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
序列主控台隨即出現並顯示開機訊息，但不會顯示**登入:** 提示字元

在 VM 中開啟 ssh 工作階段，並將這一行取消註解以更新 **/etc/inittab** 檔案：

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

接下來，執行命令 

`telinit q`

若要啟用 GRUB，請對 /boot/grub/menu.lst 進行下列變更 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 此設定會讓訊息 **Press any key to continue** 在主控台上出現 5 秒 

接著，其會讓 GRUB 功能表再顯示 5 秒；按向下鍵會中斷計數器，然後選取您想要開機的核心，為需要設定根密碼的單一使用者模式附加關鍵字 **single**。

附加 **init=/bin/bash** 命令會載入核心，但可確保 init 程式會由 Bash 殼層所取代。

您將會取得殼層的存取權，而不需要輸入密碼。 接著，您可以繼續更新 Linux 帳戶的密碼或變更其他設定。


## <a name="force-the-kernel-to-a-bash-prompt"></a>強制讓核心成為 Bash 提示字元
擁有 GRUB 的存取權可讓您中斷初始化程序。這種互動對許多復原程序很有用。
如果您沒有根密碼，且單一使用者要求您擁有根密碼，您可以藉由以 bash 提示字元取代 init 程式來將核心開機；此中斷可藉由將 init=/bin/bash 附加至核心開機行來達成

![螢幕擷取畫面顯示具有更新開機線路的主控台。](./media/virtual-machines-serial-console/bash1.png)

使用命令重新掛接您的/(根) 檔案系統 RW

`mount -o remount,rw /`

![螢幕擷取畫面顯示具有重新掛接動作的主控台。](./media/virtual-machines-serial-console/bash2.png)


現在您可以執行根密碼變更或其他許多 Linux 設定變更

![螢幕擷取畫面顯示主控台，您可以在其中變更根密碼和其他設定。](./media/virtual-machines-serial-console/bash3.png)

使用下面這行重新啟動 VM 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>單一使用者模式

或者，您可能需要以「單一使用者」或「緊急模式」存取 VM。 選取您想要使用方向鍵來進行開機或中斷的核心。
將關鍵字 **single** 或 **1** 附加至核心開機行，以進入所需的模式。 在 RHEL 系統上，您也可以附加 **rd.break**。

如需如何存取單一使用者模式的詳細資訊，請參閱[本文件](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>後續步驟
深入了解 [Azure 序列主控台]( ./serial-console-linux.md)