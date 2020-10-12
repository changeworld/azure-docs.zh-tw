---
title: 適用于 GRUB 和單一使用者模式的 Azure 序列主控台 |Microsoft Docs
description: 本文說明如何在 Azure 虛擬機器中使用適用于 GRUB 的序列主控台。
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
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439543"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>使用序列主控台來存取 GRUB 和單一使用者模式
 (GRUB) 的高整合開機載入器可能是您在將虛擬機器啟動 (VM) 時所看到的第一件事。 由於它會在作業系統啟動之前顯示，因此無法透過 SSH 存取 GRUB。 在 GRUB 中，您可以修改開機設定，以開機進入單一使用者模式，還有其他專案。

單一使用者模式是具有基本功能的基本環境。 它有助於調查開機問題、檔案系統問題或網路問題。 較少的服務可以在背景中執行，而且根據 runlevel，可能甚至不會自動掛接檔案系統。

如果您的 VM 可能設定為僅接受 SSH 金鑰進行登入，則單一使用者模式也很有用。 在此情況下，您可以使用單一使用者模式來建立具有密碼驗證的帳戶。

> [!NOTE]
> 序列主控台服務只允許具有 *參與者* 層級或更高許可權的使用者存取 VM 的序列主控台。

若要進入單一使用者模式，請在 VM 開機時進入 GRUB，並修改 GRUB 中的開機設定。 在下一節中，請參閱輸入 GRUB 的詳細指示。 一般而言，如果您的 VM 已設定為顯示 GRUB，您可以使用 VM 序列主控台內的 [重新開機] 按鈕來重新開機 VM，並顯示 GRUB。

![Linux 序列主控台重新開機按鈕](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>一般 GRUB 存取
若要存取 GRUB，請在序列主控台窗格開啟時重新開機您的 VM。 某些散發需要鍵盤輸入才能顯示 GRUB，而其他則會自動顯示 GRUB 幾秒鐘，以允許使用者鍵盤輸入來取消超時。

若要能夠存取單一使用者模式，您想要確定您的 VM 上已啟用 GRUB。 根據您的散發套件，可能需要進行一些設定工作，以確保已啟用 GRUB。 如需散發特定的資訊，請參閱下一節。

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>將您的 VM 重新啟動以在序列主控台中存取 GRUB
您可以將滑鼠移至 [ **重新開機** ] 按鈕，然後選取 [ **重新開機 vm**]，以在序列主控台中重新開機 vm。 有關重新開機的通知會顯示在窗格的底部。

如果啟用 [SysRq](./serial-console-nmi-sysrq.md) ，您也可以藉由執行 SysRq "b" 命令來重新開機 VM。 若要瞭解當您重新開機時，GRUB 的預期內容，請參閱下一節中的散發特定指示。

![Linux 序列主控台重新開機](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>一般單一使用者模式存取
當您尚未使用密碼驗證設定帳戶時，您可能需要手動存取單一使用者模式。 修改 GRUB 設定以手動進入單一使用者模式。 完成此步驟之後，請參閱「使用單一使用者模式來重設或新增密碼」一節，以取得進一步的指示。

如果 VM 無法開機，分佈通常會自動將您帶到單一使用者模式或緊急模式。 不過，其他發行版本則需要額外的設定，例如設定根密碼，才能自動將您帶到單一使用者或緊急模式。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>使用單一使用者模式來重設或新增密碼
處於單一使用者模式之後，請執行下列動作來新增具有 sudo 許可權的使用者：
1. 執行 `useradd <username>` 以新增使用者。
1. 執行 `sudo usermod -a -G sudo <username>` 以授與新的使用者根許可權。
1. 使用 `passwd <username>` 來為新使用者設定密碼。 然後，您可以使用新的使用者登入。


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux (RHEL) 存取
如果 RHEL 無法正常開機，則會自動將您帶到單一使用者模式。 但是，如果您還沒有為單一使用者模式設定根目錄存取，就不會有根密碼，也無法登入。 有因應措施 (請參閱「在 RHEL 中手動進入單一使用者模式」一節) ，但建議您一開始就設定根目錄存取。

### <a name="grub-access-in-rhel"></a>RHEL 中的 GRUB 存取
RHEL 預設會啟用 GRUB。 若要進入 GRUB，請執行來重新開機您的 VM， `sudo reboot` 然後按下任何鍵。 [GRUB] 窗格應該會顯示。 如果不是，請確定您的 GRUB 檔案中有下列幾行 (`/etc/default/grub`) ：

**適用于 RHEL 8**

>[!NOTE]
> Red Hat 建議使用 Grubby 來設定 RHEL 8 + 中的核心命令列參數。 目前無法使用 grubby 更新 grub timeout 和終端機參數。 若要修改，請更新所有開機專案的 GRUB_CMDLINE_LINUX 引數，然後執行 `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` 。 您可以在 [這裡](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)找到更多詳細資料。

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**適用于 RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat 也提供開機進入修復模式、緊急模式或偵測模式的檔，以及重設根密碼的檔。 如需相關指示，請參閱 [開機期間的終端機功能表編輯](https://aka.ms/rhel7grubterminal)。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>在 RHEL 中設定單一使用者模式的根存取
預設會停用根使用者。 RHEL 中的單一使用者模式需要啟用根使用者。 如果您需要啟用單一使用者模式，請使用下列指示：

1. 透過 SSH 登入 Red Hat 系統。
1. 切換至根目錄。
1. 執行下列動作，以啟用根使用者的密碼：
    * 執行 `passwd root` (設定強式根密碼) 。
1. 執行下列動作，確定根使用者只能透過 ttyS0 登入： a。 執行 `edit /etc/ssh/sshd_config` ，並確定 PermitRootLogIn 設定為 `no` 。
    b. 執行 `edit /etc/securetty file` 以僅允許透過 ttyS0 登入。

現在，如果系統開機進入單一使用者模式，您就可以使用根密碼登入。

或者，您也可以針對 RHEL 7.4 + 或 6.9 +，在 GRUB 提示中啟用單一使用者模式，請參閱開機 [進入單一使用者模式](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)。

### <a name="manually-enter-single-user-mode-in-rhel"></a>在 RHEL 中手動進入單一使用者模式
如果您已使用上述指示設定 GRUB 和根存取，則可以執行下列動作來進入單一使用者模式：

1. 若要進入 GRUB，請在重新開機 VM 時按下 Esc 鍵。
1. 在 GRUB 中，按 E 以編輯您要開機的作業系統。 OS 通常會列在第一行。
1. 尋找核心線。 在 Azure 中，它會從 *linux16*開始。
1. 按 Ctrl + E 鍵移至該行的結尾。
1. 在該行的結尾，新增 *systemd。 unit = 修復。目標*。

    此動作會將您引導至單一使用者模式。 如果您想要使用緊急模式，請將 *systemd* 新增至行尾 (而不是 *systemd。 unit = 修復。目標*) 。

1. 按下 Ctrl + X，結束並重新啟動已套用的設定。

   系統會提示您輸入系統管理員密碼，您才能進入單一使用者模式。 此密碼是您在先前的指示中所建立的密碼。

    ![顯示命令列介面的動畫影像。 使用者選取伺服器，找到核心行的結尾，然後輸入指定的文字。](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>輸入在 RHEL 中未啟用根帳號的單一使用者模式
如果您未遵循先前的指示來啟用根使用者，您仍然可以執行下列動作來重設根密碼：

> [!NOTE]
> 如果您是使用 SELinux，則當您重設根密碼時，請務必遵循 [Red Hat 檔](https://aka.ms/rhel7grubterminal)中所述的其他步驟。

1. 若要進入 GRUB，請在重新開機 VM 時按下 Esc 鍵。

1. 在 GRUB 中，按 E 以編輯您要開機的作業系統。 OS 通常會列在第一行。
1. 尋找核心線。 在 Azure 中，它會從 *linux16*開始。
1. 在該行的結尾，將 *rd* 新增至行尾。 在核心線路與 *rd*之間保留一個空格。

    這項動作會在將控制權傳遞至之前，中斷開機程式 `initramfs` `systemd` ，如 [Red Hat 檔](https://aka.ms/rhel7rootpassword)所述。
1. 按下 Ctrl + X，結束並重新啟動已套用的設定。

   重新開機之後，您就會進入具有唯讀檔案系統的緊急模式。

1. 在 shell 中，輸入 `mount -o remount,rw /sysroot` 以讀取/寫入權限重新掛載根檔案系統。
1. 在您開機進入單一使用者模式之後，請輸入 `chroot /sysroot` 以切換至 `sysroot` 越獄。
1. 您現在是在根目錄。 您可以輸入 `passwd` ，然後使用上述指示來進入單一使用者模式，以重設您的根密碼。
1. 完成之後，請輸入 `reboot -f` 以重新開機。

![顯示命令列介面的動畫影像。 使用者選取伺服器、找出核心行的結尾，然後輸入指定的命令。](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 透過上述指示執行時，會將您帶到緊急 shell，讓您也可以執行編輯之類的工作 `fstab` 。 不過，我們通常會建議您重設根密碼，並用它來進入單一使用者模式。

## <a name="access-for-centos"></a>CentOS 的存取
就像 Red Hat Enterprise Linux 一樣，CentOS 中的單一使用者模式需要 GRUB 和根使用者才能啟用。

### <a name="grub-access-in-centos"></a>CentOS 中的 GRUB 存取
CentOS 預設會啟用 GRUB。 若要進入 GRUB，請輸入，然後 `sudo reboot` 按下任何鍵來重新開機您的 VM。 此動作會顯示 GRUB 窗格。

### <a name="single-user-mode-in-centos"></a>CentOS 中的單一使用者模式
若要在 CentOS 中啟用單一使用者模式，請遵循 RHEL 先前的指示。

## <a name="access-for-ubuntu"></a>Ubuntu 的存取
Ubuntu 映射不需要根密碼。 如果系統開機進入單一使用者模式，您就可以使用它，而不需要額外的認證。

### <a name="grub-access-in-ubuntu"></a>Ubuntu 中的 GRUB 存取
若要存取 GRUB，請在 VM 啟動時按住 Esc 鍵。

根據預設，Ubuntu 映射可能不會自動顯示 GRUB 窗格。 您可以執行下列動作來變更設定：
1. 在文字編輯器中，開啟 */etc/default/grub.d/50-cloudimg-settings.cfg* 檔案。

1. 將此 `GRUB_TIMEOUT` 值變更為非零值。
1. 在文字編輯器中，開啟 */etc/default/grub*。
1. 批註 `GRUB_HIDDEN_TIMEOUT=1` 一行。
1. 確定有 `GRUB_TIMEOUT_STYLE=menu` 一行。
1. 執行 `sudo update-grub`。

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu 中的單一使用者模式
如果 Ubuntu 無法正常開機，則會自動將您帶到單一使用者模式。 若要手動進入單一使用者模式，請執行下列動作：

1. 在 GRUB 中，按 E 以編輯 (Ubuntu 專案) 的開機專案。
1. 尋找以 *linux*為開頭的行，然後尋找 *ro*。
1. 在*ro*之後加入*single* ，以確保在*單一*之前和之後都有一個空格。
1. 按下 Ctrl + X 以使用這些設定重新開機，並進入單一使用者模式。

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>使用 GRUB 在 Ubuntu 中叫用 bash
在您嘗試上述指示之後，可能會發生 (例如忘記的根密碼) ，您仍無法在 Ubuntu VM 中存取單一使用者模式。 您也可以告知核心以 init 的 `/bin/bash` 形式執行，而不是系統初始化。 此動作會為您提供 bash shell，並允許系統維護。 使用下列指示：

1. 在 GRUB 中，按 E 以編輯 (Ubuntu 專案) 的開機專案。

1. 尋找以 *linux*為開頭的行，然後尋找 *ro*。
1. 以*rw init =/bin/bash*取代*ro* 。

    此動作會將您的檔案系統掛接為讀寫，並 `/bin/bash` 做為 init 進程使用。
1. 按下 Ctrl + X 以使用這些設定重新開機。

## <a name="access-for-coreos"></a>CoreOS 的存取
CoreOS 中的單一使用者模式需要啟用 GRUB。

### <a name="grub-access-in-coreos"></a>CoreOS 中的 GRUB 存取
若要存取 GRUB，請在 VM 開機時按任意鍵。

### <a name="single-user-mode-in-coreos"></a>CoreOS 中的單一使用者模式
如果 CoreOS 無法正常開機，則會自動將您帶到單一使用者模式。 若要手動進入單一使用者模式，請執行下列動作：

1. 在 GRUB 中，按 E 以編輯您的開機專案。

1. 尋找以 *linux $* 開頭的行。 這兩個程式程式碼都應該有兩個實例，每個都封裝在不同的 *if .。。else* 子句。
1. 將 *coreos. autologin = ttyS0* 附加到每個 *linux $* line 的結尾。
1. 按下 Ctrl + X 以使用這些設定重新開機，並進入單一使用者模式。

## <a name="access-for-suse-sles"></a>SUSE SLES 的存取
如果系統開機進入緊急模式，SLES 12 SP3 + 的較新映射可允許透過序列主控台存取。

### <a name="grub-access-in-suse-sles"></a>SUSE SLES 中的 GRUB 存取
SLES 中的 GRUB 存取需要透過 YaST 的開機載入器設定。 若要建立設定，請執行下列動作：

1. 使用 SSH 登入 SLES VM，然後執行 `sudo yast bootloader` 。 按下 Tab 鍵，然後按 Enter 鍵，然後使用方向鍵來流覽功能表。

1. 移至 [ **核心參數**]，然後選取 [ **使用序列主控台** ] 核取方塊。
1. 新增 `serial --unit=0 --speed=9600 --parity=no` 至 **主控台** 引數。
1. 按 F10 儲存您的設定並結束。
1. 若要進入 GRUB，請重新開機您的 VM，並在開機順序期間按下任何鍵，以保持 GRUB 窗格的顯示。

    GRUB 的預設超時值是 **1**。 您可以藉由變更/etc/default/grub 檔中的變數來修改此設定 `GRUB_TIMEOUT` 。 */etc/default/grub*

![正在初始化開機載入器設定](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 中的單一使用者模式
如果 SLES 無法正常開機，您會自動放入緊急 shell。 若要手動輸入緊急 shell，請執行下列動作：

1. 在 GRUB 中，按 E 以編輯您的開機專案， (SLES 專案) 。

1. 尋找以 *linux*為開頭的核心行。
1. 將 *systemd* 附加至核心行的結尾。
1. 按下 Ctrl + X 以使用這些設定重新開機，然後輸入緊急 shell。

   > [!NOTE]
   > 此動作會將您帶到具有唯讀檔案系統的緊急 shell。 若要編輯任何檔案，請以讀寫許可權重新掛接檔案系統。 若要這樣做，請 `mount -o remount,rw /` 在 shell 中輸入。

## <a name="access-for-oracle-linux"></a>Oracle Linux 的存取
就像 Red Hat Enterprise Linux 一樣，Oracle Linux 中的單一使用者模式需要 GRUB 和根使用者才能啟用。

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux 中的 GRUB 存取
Oracle Linux 預設會啟用 GRUB。 若要進入 GRUB，請執行來將您的 VM 重新開機 `sudo reboot` ，然後按下 Esc 鍵。此動作會顯示 GRUB 窗格。 如果未顯示 GRUB 窗格，請確定行的值 `GRUB_TERMINAL` 包含 *序列主控台* (也就是 `GRUB_TERMINAL="serial console"`) 。 使用重建 GRUB `grub2-mkconfig -o /boot/grub/grub.cfg` 。

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux 中的單一使用者模式
若要在 Oracle Linux 中啟用單一使用者模式，請遵循 RHEL 先前的指示。

## <a name="next-steps"></a>接下來的步驟
若要深入瞭解序列主控台，請參閱：
* [Linux 序列主控台檔](serial-console-linux.md)
* [使用序列主控台啟用各種散發套件中的 GRUB](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [使用序列主控台進行 NMI 和 SysRq 呼叫](serial-console-nmi-sysrq.md)
* [適用于 Windows Vm 的序列主控台](serial-console-windows.md)
* [開機診斷](boot-diagnostics.md)
