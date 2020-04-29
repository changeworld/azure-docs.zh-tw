---
title: Azure 序列主控台主動式 GRUB 設定 |Microsoft Docs
description: 跨各種散發設定 GRUB，以在 Azure 虛擬機器中允許單一使用者和復原模式存取。
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
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262888"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>主動確保您有 GRUB 和 sysrq 的存取權，可以為您節省許多停機時間

在大部分情況下，可以存取序列主控台和 GRUB，將可改善 IaaS Linux 虛擬機器的復原時間。 GRUB 提供復原選項，否則會花更長的時間來復原您的 VM。 


執行 VM 復原的原因很多，而且可以是下列案例的屬性：

   - 損毀的檔案系統/核心/MBR （主開機記錄）
   - 核心升級失敗
   - 不正確的 GRUB 核心參數
   - 不正確的 fstab 設定
   - 防火牆設定
   - 遺失密碼
   - 損壞的 sshd 設定檔案
   - 網路設定

 其他許多案例，詳細資訊請參閱[這裡](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

確認您可以在 Azure 中部署的 Vm 上存取 GRUB 和序列主控台。 

如果您不熟悉序列主控台，請參閱[此連結](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/)。

> [!TIP]
> 在進行變更之前，請務必先備份檔案

請觀看下列影片，以瞭解在您可以存取 GRUB 之後，如何快速復原 Linux VM

[復原 Linux VM 影片](https://youtu.be/KevOc3d_SG4)

有數種方法可協助復原 Linux Vm。 在雲端環境中，此程式是一項挑戰。
系統會持續對工具和功能進行進度，以確保服務能夠快速復原。

使用 Azure 序列主控台，您可以與您的 Linux VM 互動，就好像您是在系統的主控台一樣。

您可以操控許多設定檔案，包括核心的開機方式。 

較有經驗的 Linux/Unix sys 管理員將會感謝透過 Azure 序列主控台存取的**單一使用者**和**緊急模式**，讓許多復原案例有多餘的磁片交換和 VM 刪除。

復原的方法取決於所發生的問題，例如遺失或錯置的密碼可以透過 Azure 入口網站選項 >**重設密碼**來重設。 **重設密碼**功能稱為「延伸模組」，並與 Linux 「來賓代理程式」通訊。

其他擴充功能（例如自訂腳本）可供使用，但是這些選項會要求 Linux **waagent**必須處於狀況良好的狀態，但不一定都是如此。

![代理程式狀態](./media/virtual-machines-serial-console/agent-status.png)


確保您有權存取 Azure 序列主控台和 GRUB，表示密碼變更或不正確的設定可以在幾分鐘內（而不是數小時）更正。 您甚至可以強制 VM 從替代核心開機，萬一您的主要核心已損毀的情況下，您必須在磁片上有多個核心。

![多重核心](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>建議的修復方法順序：

- Azure 序列主控台

- 磁片交換–可以使用下列其中一種方式來自動化：

   - [Power Shell 修復腳本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash 修復腳本](https://github.com/sribs/azure-support-scripts)

- 舊版方法

## <a name="disk-swap-video"></a>磁片交換影片：

如果您沒有 GRUB 的存取權，請觀看[這](https://youtu.be/m5t0GZ5oGAc)段影片並查看，如何輕鬆地將磁片交換程式自動化以復原您的 VM

## <a name="challenges"></a>挑戰

並非所有 Linux Azure Vm 預設都會針對 GRUB 存取進行設定，而且它們都不會設定為使用 sysrq 命令來中斷。 某些較舊的散發版本（例如 SLES 11）未設定為在 Azure 序列主控台中顯示登入提示

在本文中，我們將探討各種 Linux 散發套件，以及如何讓 GRUB 可用的檔設定。




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>如何設定 Linux VM 以接受 SysRq 金鑰
根據預設，在某些較新的 Linux 散發版本上會啟用 sysrq 金鑰，雖然其他人可能會將其設定為只接受特定 SysRq 功能的值。
在較舊的散發版本上，它可能已完全停用。

SysRq 功能適用于直接從 Azure 序列主控台重新開機已當機或停止回應的 VM，也有助於取得 GRUB 功能表的存取權，或從另一個入口網站視窗重新開機 VM，或 ssh 會話可能會捨棄目前的主控台連線，因此會使用來顯示 GRUB 功能表的 GRUB 超時過期。
VM 必須設定為接受核心參數的值1，以啟用 sysrq 或128的所有功能，以允許重新開機/關機


[啟用 sysrq 影片](https://youtu.be/0doqFRrHz_Mc)


若要將 VM 設定為透過 Azure 入口網站上的 SysRq 命令來接受重新開機，您必須針對核心參數 SysRq 設定1的值。

若要讓此設定持續重新開機，請將專案新增至**sysctl**檔案

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

動態設定核心參數

`sysctl -w kernel.sysrq=1`

如果您沒有**根**存取權，或 sudo 已中斷，則無法從 shell 提示字元設定 sysrq。

您可以使用 Azure 入口網站，在此案例中啟用 sysrq。 如果**sudoers. d/waagent**檔案已損毀或已被刪除，這個方法會很有説明。

使用 Azure 入口網站作業 > 執行命令 > RunShellScript 功能，要求 waagent 程式的狀況良好，您可以接著插入此命令以啟用 sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

如下所示： ![啟用 sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

完成之後，您就可以嘗試存取**sysrq** ，而且應該會看到重新開機是可行的。

![啟用 sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

選取**重新開機**和**傳送 SysRq**命令

![啟用 sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

系統應記錄重設訊息，例如

![啟用 sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB 設定

根據預設，您應該能夠在 VM 開機期間按住**Esc**鍵來存取 GRUB，如果未顯示 grub 功能表，您可以使用下列其中一個選項來強制並保留 Azure 序列主控台中的 GRUB 功能表。

**選項 1** -強制在螢幕上顯示 GRUB 

更新檔案/etc/default/grub.d/50-cloudimg-settings.cfg，以將 GRUB 功能表保留在螢幕上指定的超時時間。
您不需要按**Esc 鍵**，就會立即顯示 GRUB

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**選項 2** -允許在開機前按下**Esc 鍵**

對/etc/default/grub 檔案進行變更，並觀察3秒的超時時間以按**Esc** ，可能會有類似的行為


將這兩行標記為批註：

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
並加入下面這一行：

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 將允許存取序列主控台，但不提供互動的能力。 找不到**登入：** 提示


若要讓12.04 取得**登入：** 提示：
1. 建立名為/etc/init/ttyS0.conf 的檔案，其中包含下列文字：

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

2. 要求新崛起啟動 getty     
    ```
    sudo start ttyS0
    ```
 
您可以在[這裡](https://help.ubuntu.com/community/SerialConsoleHowto)找到為 Ubuntu 版本設定序列主控台所需的設定。

## <a name="ubuntu-recovery-mode"></a>Ubuntu 修復模式

Ubuntu 的其他復原和清除選項可透過 GRUB 取得，但只有在您設定核心參數時，才可存取這些設定。
無法設定此核心開機參數會強制將 [復原] 功能表傳送至 Azure 診斷，而不是 Azure 序列主控台。
您可以遵循下列步驟來取得 Ubuntu 復原功能表的存取權：

中斷開機進程並存取 GRUB 功能表

為 Ubuntu 選取 [Advanced Options]，然後按 enter 鍵

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

選取顯示的行 *（修復模式）* 不要按 enter，但按 "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

找出將會載入核心的程式程式碼，並將最後一個參數**nomodeset**替換為目的地作為**主控台 = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

按**Ctrl-x**以啟動並載入核心。
如果一切順利，您會看到這些額外的選項，可協助您執行其他修復選項

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB 設定

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB 設定
這些版本上的預設/etc/default/grub 設定已適當設定

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
要修改的檔案是/etc/default/grub –預設設定看起來像這個範例：

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

變更/etc/default/grub 中的下列幾行

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

也請加入下面這一行：

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub 現在看起來應該類似此範例：

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
使用來完成和更新 grub 設定

`grub2-mkconfig -o /boot/grub2/grub.cfg`

設定 SysRq 核心參數：

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

或者，您也可以在 shell 中或透過執行命令，使用單一程式列來設定 GRUB 和 SysRq。 執行此命令之前，請先備份您的檔案：


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB 設定
要修改的檔案是/boot/grub/grub.conf。 此`timeout`值會決定顯示 GRUB 的時間長度。

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


最後一行*終端機–-timeout = 5 序列主控台*會進一步增加**GRUB**超時，方法是新增5秒的提示 **，並按任意鍵以繼續。**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB 功能表應該會在畫面上顯示設定的 timeout = 15，而不需要按下 Esc 鍵。請務必在瀏覽器中按一下主控台，使其作用中的功能表，並選取所需的核心

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
請[根據官方檔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)使用 yast 開機載入器

或新增/變更以/etc/default/grub 下列參數：

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
確認 GRUB_CMDLINE_LINUX 或 GRUB_CMDLINE_LINUX_DEFAULT 中使用了 ttys0

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

重新建立 grub

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
序列主控台隨即出現並顯示開機訊息，但不會顯示**登入：** 提示

開啟 VM 的 ssh 會話，並取消批註這一行來更新檔案 **/etc/inittab** ：

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

接下來，執行命令 

`telinit q`

若要啟用 GRUB，應對/boot/grub/menu.lst 進行下列變更 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 此設定可讓訊息**按任意鍵繼續**在主控台上顯示5秒 

接著，它會顯示一個額外5秒的 GRUB 功能表-按下箭號，您將會中斷計數器，然後選取您想要開機的核心，將關鍵字**single**附加到需要設定根密碼的單一使用者模式。

附加命令**init =/bin/bash**將會載入核心，但會確保 init 程式已由 bash shell 所取代。

您將可以取得 shell 的存取權，而不需要輸入密碼。 接著，您可以繼續更新 Linux 帳戶的密碼或進行其他設定變更。


## <a name="force-the-kernel-to-a-bash-prompt"></a>強制核心到 bash 提示字元
有了 GRUB 的存取權，就能讓您中斷初始化程式。這種互動對許多復原程式很有用。
如果您沒有根密碼，且單一使用者要求您擁有根密碼，您可以使用 bash 提示來啟動核心取代 init 程式–此插斷可以藉由將 init =/bin/bash 附加至核心開機行來達成

![bash1](./media/virtual-machines-serial-console/bash1.png)

使用命令重新掛接您的/（根）檔案系統 RW

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


現在您可以執行根密碼變更或其他許多 Linux 設定變更

![bash3](./media/virtual-machines-serial-console/bash3.png)

使用重新開機 VM 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>單一使用者模式

或者，您可能需要以「單一使用者」或「緊急模式」存取 VM。 選取您想要使用方向鍵開機或中斷的核心。
將關鍵字**single**或**1**附加至核心開機行，以進入所需的模式。 在 RHEL 系統上，您也可以附加**rd. break**。

如需如何存取單一使用者模式的詳細資訊，請參閱[這](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access)份檔 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>後續步驟
深入瞭解[Azure 序列主控台]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
