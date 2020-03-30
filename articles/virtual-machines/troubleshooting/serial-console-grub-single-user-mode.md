---
title: 用於 GRUB 和單使用者模式的 Azure 串列主控台 |微軟文檔
description: 本文介紹如何在 Azure 虛擬機器中使用串列主控台進行 GRUB。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883920"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>使用串列主控台訪問 GRUB 和單使用者模式
在啟動虛擬機器 （VM） 時，GRand 統一引導載入程式 （GRUB） 可能是您首先看到的事情。 由於它在作業系統啟動之前顯示，因此無法通過 SSH 訪問 GRUB。 在 GRUB 中，您可以修改引導配置以引導到單使用者模式等。

單使用者模式是一個功能最少的最低環境。 它可用於調查引導問題、檔案系統問題或網路問題。 在後臺運行的服務較少，並且根據執行層級，檔案系統甚至可能無法自動裝載。

在 VM 可能配置為僅接受 SSH 金鑰進行登錄的情況下，單使用者模式也很有用。 在這種情況下，您可能能夠使用單使用者模式創建具有密碼身份驗證的帳戶。 

> [!NOTE]
> 串列主控台服務僅允許具有*參與者*級別或更高許可權的使用者訪問 VM 的串列主控台。

要進入單使用者模式，請在 VM 啟動時進入 GRUB，並在 GRUB 中修改啟動配置。 在下一節中查看有關輸入 GRUB 的詳細說明。 通常，如果 VM 已配置為顯示 GRUB，則可以使用 VM 串列主控台中的重新開機按鈕重新開機 VM 並顯示 GRUB。

![Linux 串列主控台重新開機按鈕](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>一般 GRUB 存取
要訪問 GRUB，請在串列主控台窗格打開時重新開機 VM。 某些發行版本需要鍵盤輸入來顯示 GRUB，而其他發行版本會自動顯示 GRUB 幾秒鐘，以允許使用者鍵盤輸入取消超時。

為了能夠訪問單使用者模式，您需要確保在 VM 上啟用了 GRUB。 根據您的分佈，可能需要進行一些設置工作，以確保啟用 GRUB。 有關特定于分發的資訊，請參閱 Azure 頁面上的下一節和我們對[Linux 的支援](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)。

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>將您的 VM 重新啟動以在序列主控台中存取 GRUB
您可以通過將滑鼠懸停在 **"重新開機"** 按鈕，然後選擇 **"重新開機 VM"，** 在串列主控台中重新開機 VM。 有關重新開機的通知顯示在窗格的底部。

如果[啟用了 SysRq，](./serial-console-nmi-sysrq.md)還可以通過運行 SysRq"b"命令重新開機 VM。 要瞭解重新開機時對 GRUB 的期望，請參閱下一節中特定于分發的說明。

![Linux 串列主控台重新開機](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>一般單使用者模式訪問
如果尚未配置具有密碼身份驗證的帳戶，則可能需要手動訪問單使用者模式。 修改 GRUB 配置以手動進入單使用者模式。 完成此操作後，請參閱"使用單使用者模式重置或添加密碼"部分，瞭解進一步說明。

如果 VM 無法啟動，分發通常會自動將您放入單使用者模式或緊急模式。 但是，其他發行版本需要額外的設置，例如設置根密碼，然後才能自動將您放入單使用者或緊急模式。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>使用單使用者模式重置或添加密碼
在單使用者模式下後，通過執行以下操作添加具有 sudo 許可權的新使用者：
1. 運行`useradd <username>`以添加使用者。
1. 運行`sudo usermod -a -G sudo <username>`以授予新使用者根許可權。
1. 使用 `passwd <username>` 來為新使用者設定密碼。 然後，您可以以新使用者身份登錄。


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux (RHEL) 存取
如果 RHEL 無法正常啟動，它會自動將您放入單使用者模式。 但是，如果您尚未為單使用者模式設置根存取權限，則沒有根密碼，無法登錄。 有一個解決方法（請參閱 RHEL 中的"手動輸入單使用者模式"部分），但我們建議您首先設置根訪問。

### <a name="grub-access-in-rhel"></a>RHEL 中的 GRUB 存取
RHEL 預設會啟用 GRUB。 要進入 GRUB，請通過 運行`sudo reboot`重新開機 VM，然後按任意鍵。 應顯示 GRUB 窗格。 如果不是，請確保以下行存在於您的 GRUB 檔中 （`/etc/default/grub`：

**用於 RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**用於 RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> 紅帽還提供引導到救援模式、緊急模式或偵錯模式以及重置根密碼的文檔。 有關說明，請參閱[引導期間編輯終端功能表](https://aka.ms/rhel7grubterminal)。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>在 RHEL 中為單使用者模式設置根訪問
預設情況下，根使用者已禁用。 RHEL 中的單使用者模式需要啟用根使用者。 如果需要啟用單使用者模式，請使用以下說明：

1. 通過 SSH 登錄紅帽系統。
1. 切換到根目錄。
1. 通過執行以下操作為根使用者啟用密碼：
    * 運行`passwd root`（設置強根密碼）。
1. 確保根使用者只能通過 ttyS0 登錄，只需執行以下操作：  
    a. 運行`edit /etc/ssh/sshd_config`，並確保允許 RootLogIn 設置為`no`。  
    b. 運行`edit /etc/securetty file`以僅通過 ttyS0 允許登錄。

現在，如果系統引導到單使用者模式，您可以使用根密碼登錄。

或者，對於 RHEL 7.4+ 或 6.9+，要在 GRUB 提示符中啟用單使用者模式，請參閱[引導到單使用者模式](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)。

### <a name="manually-enter-single-user-mode-in-rhel"></a>在 RHEL 中手動進入單使用者模式
如果使用上述說明設置 GRUB 和根訪問，則可以通過執行以下操作進入單使用者模式：

1. 要進入 GRUB，請在重新開機 VM 時按 Esc。
1. 在 GRUB 中，按 E 編輯要引導到的作業系統。 作業系統通常列在第一行。
1. 查找內核行。 在 Azure 中，它從*linux16*開始。
1. 按 Ctrl+E 轉到行尾。
1. 在行尾，添加*系統.unit_rescue.目標*。
    
    此操作將引導您進入單使用者模式。 如果要使用緊急模式，請使用*系統.unit_emergency.target*添加到線路的末尾（而不是*系統.unit_rescue.target）。*

1. 按 Ctrl+X 退出並重新啟動應用的設置。

   在輸入單使用者模式之前，系統將提示您輸入管理員密碼。 此密碼是您在前面的說明中創建的密碼。

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>在 RHEL 中未啟用根帳號的情況下輸入單使用者模式
如果未按照前面的說明啟用根使用者，您仍可以通過執行以下操作來重置根密碼：

> [!NOTE]
> 如果您使用的是 SELinux，則重置根密碼時，請務必按照[紅帽文檔中](https://aka.ms/rhel7grubterminal)介紹的其他步驟操作。

1. 要進入 GRUB，請在重新開機 VM 時按 Esc。

1. 在 GRUB 中，按 E 編輯要引導到的作業系統。 作業系統通常列在第一行。
1. 查找內核行。 在 Azure 中，它從*linux16*開始。
1. 在行的末尾，將*rd.break*添加到行的末尾。 在內核行和*rd.break*之間留出一個空格。

    此操作在將控制項傳遞到`initramfs`之前中斷啟動過程`systemd`，如[紅帽文檔中](https://aka.ms/rhel7rootpassword)所述。
1. 按 Ctrl+X 退出並重新啟動應用的設置。

   重新開機後，您將使用唯讀檔案系統進入緊急模式。 
   
1. 在 shell 中`mount -o remount,rw /sysroot`，輸入以重新裝入具有讀/寫許可權的根檔案系統。
1. 啟動到單使用者模式後，進入`chroot /sysroot`以切換到`sysroot`監獄。
1. 你現在是根。 您可以通過輸入`passwd`然後使用上述說明來重置根密碼，從而進入單使用者模式。 
1. 完成後，進入`reboot -f`以重新開機。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 運行前面的說明會將您放入緊急 shell，以便您還可以執行編輯等任務`fstab`。 但是，我們通常建議您重置根密碼，並用它來進入單使用者模式。

## <a name="access-for-centos"></a>CentOS 的存取
與紅帽企業 Linux 一樣，CentOS 中的單使用者模式需要 GRUB 和根使用者啟用。

### <a name="grub-access-in-centos"></a>CentOS 中的 GRUB 存取
CentOS 預設會啟用 GRUB。 要進入 GRUB，請通過輸入`sudo reboot`重新開機 VM，然後按任意鍵。 此操作將顯示 GRUB 窗格。

### <a name="single-user-mode-in-centos"></a>CentOS 中的單使用者模式
要在 CentOS 中啟用單使用者模式，請按照 RHEL 的較早說明操作。

## <a name="access-for-ubuntu"></a>Ubuntu 的存取
Ubuntu 圖像不需要根密碼。 如果系統啟動到單使用者模式，則可以不使用其他憑據使用它。

### <a name="grub-access-in-ubuntu"></a>Ubuntu 中的 GRUB 存取
要訪問 GRUB，請按住在 VM 啟動時按住 Esc。

預設情況下，Ubuntu 圖像可能不會自動顯示 GRUB 窗格。 您可以通過執行以下操作來更改設置：
1. 在文字編輯器中，打開 */etc/default/grub.d/50-雲-設置.cfg*檔。

1. 將`GRUB_TIMEOUT`值更改為非零值。
1. 在文字編輯器中，打開 */etc/預設/grub*。
1. 注釋掉行`GRUB_HIDDEN_TIMEOUT=1`。
1. 確保有一`GRUB_TIMEOUT_STYLE=menu`條線。
1. 執行 `sudo update-grub`。

### <a name="single-user-mode-in-ubuntu"></a>烏本圖中的單使用者模式
如果 Ubuntu 無法正常啟動，它會自動將您放入單使用者模式。 要手動進入單使用者模式，請執行以下操作：

1. 在 GRUB 中，按 E 編輯引導條目（Ubuntu 條目）。
1. 尋找從*linux*開始的行，然後尋找*ro。*
1. 在*ro*之後添加*單*，確保*單*前後有一個空格。
1. 按 Ctrl+X 使用這些設置重新開機並進入單使用者模式。

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>使用 GRUB 在 Ubuntu 中調用 bash
嘗試上述說明後，可能會出現以下情況（如忘記的根密碼），您仍無法訪問 Ubuntu VM 中的單使用者模式。 您還可以告訴內核以 init`/bin/bash`身份運行，而不是系統 init。 此操作為您提供了一個 bash 外殼，並允許進行系統維護。 使用下列指示：

1. 在 GRUB 中，按 E 編輯引導條目（Ubuntu 條目）。

1. 尋找從*linux*開始的行，然後尋找*ro。*
1. 用*rw init_/bin/bash*替換*ro。*

    此操作將檔案系統裝載為讀寫，並用作`/bin/bash`init 進程。
1. 按 Ctrl+X 可使用這些設置重新開機。

## <a name="access-for-coreos"></a>CoreOS 的存取
CoreOS 中的單使用者模式需要啟用 GRUB。

### <a name="grub-access-in-coreos"></a>CoreOS 中的 GRUB 存取
要訪問 GRUB，請按 VM 啟動時的任何鍵。

### <a name="single-user-mode-in-coreos"></a>CoreOS 中的單使用者模式
如果 CoreOS 無法正常啟動，它會自動將您放入單使用者模式。 要手動進入單使用者模式，請執行以下操作：

1. 在 GRUB 中，按 E 編輯引導條目。

1. 尋找以*linux$* 開頭的行。 行應該有兩個實例，每個實例封裝在不同的*if...否則*條款。
1. 將*coreos.autologin_ttyS0*追加到每個*linux$* 行的末尾。
1. 按 Ctrl+X 使用這些設置重新開機並進入單使用者模式。

## <a name="access-for-suse-sles"></a>SUSE SLES 的存取
如果系統進入緊急模式，SLES 12 SP3+ 的較新映射允許通過串列主控台進行訪問。

### <a name="grub-access-in-suse-sles"></a>SUSE SLES 中的 GRUB 存取
SLES 中的 GRUB 訪問需要通過 YaST 進行引導載入程式配置。 要創建配置，請執行以下操作：

1. 使用 SSH 登錄到 SLES VM，然後運行`sudo yast bootloader`。 按"選項卡"，按 Enter，然後使用方向鍵在功能表中導航。

1. 轉到**內核參數**，然後選擇 **"使用串列主控台**"核取方塊。
1. 添加到`serial --unit=0 --speed=9600 --parity=no`**主控台**參數。
1. 按 F10 可保存設置和退出。
1. 要進入 GRUB，請重新開機 VM，並在啟動序列中按任意鍵以保持 GRUB 窗格的顯示。

    GRUB 的預設超時為**1。** 您可以通過更改`GRUB_TIMEOUT`*/etc/default/grub*檔中的變數來修改此設置。

![初始化引導載入程式配置](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 中的單使用者模式
如果 SLES 無法正常啟動，則會自動將您放入緊急外殼中。 要手動輸入緊急外殼，請執行以下操作：

1. 在 GRUB 中，按 E 編輯引導條目（SLES 條目）。

1. 查找以*linux*開頭的內核行。
1. 追加*系統.unit_緊急.目標*到內核行的末尾。
1. 按 Ctrl+X 使用這些設置重新開機並輸入緊急外殼。

   > [!NOTE]
   > 此操作將您放入具有唯讀檔案系統的緊急外殼中。 要編輯任何檔，請重新裝入具有讀寫許可權的檔案系統。 為此，請輸入`mount -o remount,rw /`殼體。

## <a name="access-for-oracle-linux"></a>Oracle Linux 的存取
與紅帽企業 Linux 一樣，Oracle Linux 中的單使用者模式需要啟用 GRUB 和根使用者。

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux 中的 GRUB 存取
Oracle Linux 預設會啟用 GRUB。 要進入 GRUB，請通過運行`sudo reboot`重新開機 VM，然後按 Esc。此操作將顯示 GRUB 窗格。 如果未顯示 GRUB 窗格，請確保`GRUB_TERMINAL`行的值包含*串列主控台*（即 ）。 `GRUB_TERMINAL="serial console"` 使用`grub2-mkconfig -o /boot/grub/grub.cfg`重建 GRUB。

### <a name="single-user-mode-in-oracle-linux"></a>甲骨文Linux中的單使用者模式
要在 Oracle Linux 中啟用單使用者模式，請按照 RHEL 的較早說明操作。

## <a name="next-steps"></a>後續步驟
要瞭解有關串列主控台的資訊，請參閱：
* [Linux 串列主控台文檔](serial-console-linux.md)
* [使用串列主控台在各種發行版本中啟用 GRUB](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [將串列主控台用於 NMI 和 SysRq 調用](serial-console-nmi-sysrq.md)
* [適用于 Windows VM 的串列主控台](serial-console-windows.md)
* [開機診斷](boot-diagnostics.md)
