---
title: 適用于 GRUB 和單一使用者模式的 Azure 序列主控台
description: 針對 Azure 虛擬機器中的 grub 使用序列主控台。
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758502"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>使用序列主控台來存取 GRUB 與單一使用者模式
GRUB 的全名是 GRand Unified Bootloader。 從 GRUB，您可以修改開機設定，以開機到單一使用者模式。

單一使用者模式是具有最基本功能的最基本環境。 它非常適用於調查開機問題、檔案系統問題或網路問題。 背景只有基本的服務在執行，而且視執行層級而定，甚至可能不會自動掛載檔案系統。

在您的 VM 可能只設定為接受 SSH 金鑰來登入的情況下，單一使用者模式也很實用。 在此案例中，您可以使用單一使用者模式來建立含密碼驗證的帳戶。

若要進入單一使用者模式，您必須在 VM 開機時進入 GRUB，並修改 GRUB 中的開機設定。 您可以使用 VM 序列主控台來完成此動作。

## <a name="general-grub-access"></a>一般 GRUB 存取
若要存取 GRUB，您必須將序列主控台維持開啟，並將 VM 重新開機。 某些發行版本需要鍵盤輸入才能顯示 GRUB，而其他發行版本將會自動顯示 GRUB 幾秒並允許使用者鍵盤輸入以取消逾時。 

建議您確定已在您的 VM 上啟用 GRUB，以便存取單一使用者模式。 視您的發行版本而定，您可能必須完成一些設定工作，才能啟用 GRUB。 發行版本特定資訊如下。

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>將您的 VM 重新開機以在序列主控台中存取 GRUB
在序列主控台刀鋒視窗維持開啟的情況下將您的 VM 重新開機可以透過 SysRq `'b'` 命令來完成 (若 [SysRq](./serial-console-nmi-sysrq.md) 已啟用)，或透過按一下 [概觀] 刀鋒視窗中的 [重新啟動] 按鈕 (在瀏覽器索引標籤中開啟 VM，以在不關閉序列主控台刀鋒視窗的情況下重新開機) 來完成。 依照下面的發行版本特定指示來了解重新開機時，GRUB 會發生什麼情況。

## <a name="general-single-user-mode-access"></a>一般單一使用者模式存取
在未設定含密碼驗證的情況下，可能需要手動存取單一使用者模式。 您將必須修改 GRUB 設定以手動進入單一使用者模式。 完成此動作之後，請參閱＜使用單一使用者模式來重設或新增密碼＞，以取得進一步的指示。

萬一 VM 無法開機，發行版本將常會自動讓您進入單一使用者模式或緊急模式。 不過，其他發行版本要求您進行額外的設定，才會自動讓您進入單一使用者模式或緊急模式 (例如設定 root 密碼)。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>使用單一使用者模式來重設或新增密碼
進入單一使用者模式之後，請執行下列動作以使用 sudo 權限新增使用者：
1. 執行 `useradd <username>` 以新增使用者
1. 執行 `sudo usermod -a -G sudo <username>` 以將 root 權限授與新使用者
1. 使用 `passwd <username>` 來為新使用者設定密碼。 接著您將能以新使用者身分登入

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux (RHEL) 存取
RHEL 會在無法正常開機時讓您進入單一使用者模式。 不過，若您尚未設定單一使用者模式的開機存取，您將不會有 root 密碼，而且將無法登入。 有一個因應措施 (請參閱下面的＜手動進入單一使用者模式＞)，但還是建議您一開始就設定 root 存取。

### <a name="grub-access-in-rhel"></a>RHEL 中的 GRUB 存取
RHEL 預設會啟用 GRUB。 若要進入 GRUB，請使用 `sudo reboot` 將您的 VM 重新開機並按任意鍵。 您將會看到 GRUB 畫面。

> 注意：Red Hat 也提供開機到「救援模式」、「緊急模式」、「偵錯模式」以及重設 root 密碼的文件。 [按一下這裡以存取該文件](https://aka.ms/rhel7grubterminal)。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>在 RHEL 中針對單一使用者模式設定 root 存取
RHEL 中的單一使用者模式要求啟用 root 使用者 (預設為停用)。 如果您需要啟用單一使用者模式，請依下列指示進行操作：

1. 透過 SSH 登入 Red Hat 系統
1. 切換至 root
1. 啟用根使用者的密碼 
    * `passwd root` (設定強式根密碼)
1. 確定根使用者只能透過 ttyS0 進行登入
    * `edit /etc/ssh/sshd_config` 並確定將 PermitRootLogIn 設定為 no
    * `edit /etc/securetty file` 以只允許透過 ttyS0 進行登入 

現在，如果系統開機進入單一使用者模式，您將能夠透過根密碼進行登入。

或者，針對 RHEL 7.4 以上或 6.9 以上的版本，您也可以在 GRUB 提示字元中啟用單一使用者模式，請參閱[這裡](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)的指示

### <a name="manually-enter-single-user-mode-in-rhel"></a>在 RHEL 中手動進入單一使用者模式
若要根據上述指示設定 GRUB 與 root 存取，您可以使用下列指示來進入單一使用者模式：

1. 在重新啟動 VM 之後按 'Esc' 以進入 GRUB
1. 在 GRUB 中，按 'e' 以編輯您要開機進入其中的特定 OS (通常是第一行)
1. 尋找核心行，在 Azure 中，它的開頭是 `linux16`
1. 按 Ctrl + E 以移至行尾
1. 在行尾加入下列內容：`systemd.unit=rescue.target`
    * 這會讓您開機進入單一使用者模式。 若要使用緊急模式，請將 `systemd.unit=emergency.target` 加到行尾，而不是 `systemd.unit=rescue.target`
1. 按 Ctrl + X 以結束並以套用的設定重新開機
1. 系統會要求您輸入系統管理員密碼，才能進入單一使用者模式，此密碼與您在上述指示中建立的密碼相同    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>在 RHEL 中於未啟用 root 帳戶的情況下進入單一使用者模式
若未依照上述步驟啟用 root 使用者，您仍然可以重設您的 root 密碼。 使用下列指示：

> 注意：若您使用 SELinux，當您重設 root 密碼之前，請確定您已執行[這裡](https://aka.ms/rhel7grubterminal)的 Red Hat 文件中所述的額外步驟。

1. 在重新啟動 VM 之後按 'Esc' 以進入 GRUB
1. 在 GRUB 中，按 'e' 以編輯您要開機進入其中的特定 OS (通常是第一行)
1. 尋找核心行，在 Azure 中，它的開頭是 `linux16`
1. 將 `rd.break` 加到行尾，並確定 `rd.break` 前面有一個空格 (請參閱下面的範例)
    - 這會在控制權從 `initramfs` 傳遞到 `systemd` 之前中斷開機程序，如[這裡](https://aka.ms/rhel7rootpassword) Red Hat 文件所述。
1. 按 Ctrl + X 以結束並以套用的設定重新開機
1. 開機之後，您會進入緊急模式，而且檔案系統是唯讀的。 在殼層中輸入 `mount -o remount,rw /sysroot` 以使用讀取/寫入權限重新掛載 root 檔案系統
1. 開機進入單一使用者模式之後，輸入 `chroot /sysroot` 以切換到 `sysroot` jail
1. 您現在是 root 了。 您可以使用 `passwd` 重設您的 root 密碼，然後使用上述指示來進入單一使用者模式。 完成之後，輸入 `reboot -f` 以重新開機。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> 注意：執行上述指示將會進入緊急殼層，因此您也可以執行諸如編輯 `fstab` 的工作。 不過，一般公認建議是重設您的 root 密碼，並使用它來進入單一使用者模式。 


## <a name="access-for-centos"></a>CentOS 的存取
與 Red Hat Enterprise Linux 非常像，CentOS 中的單一使用者模式也要求必須啟用 GRUB 與 root 使用者。 

### <a name="grub-access-in-centos"></a>CentOS 中的 GRUB 存取
CentOS 預設會啟用 GRUB。 若要進入 GRUB，請使用 `sudo reboot` 將您的 VM 重新開機並按任意鍵。 您將會看到 GRUB 畫面。

### <a name="single-user-mode-in-centos"></a>CentOS 中的單一使用者模式
依照上述適用於 RHEL 的指示來在 CentOS 中啟用單一使用者模式。

## <a name="access-for-ubuntu"></a>Ubuntu 的存取 
Ubuntu 映像不需要 root 密碼。 如果系統開機進入單一使用者模式，您無須額外的認證即可存取。 

### <a name="grub-access-in-ubuntu"></a>Ubuntu 中的 GRUB 存取
若要存取 GRUB，請在 VM 開機時長按 'Esc'。 

根據預設，Ubuntu 映像將不會自動顯示 GRUB 畫面。 您可以依照下列指示來變更此行為：
1. 在您慣用的文字編輯器中開啟 `/etc/default/grub.d/50-cloudimg-settings.cfg`
1. 將 `GRUB_TIMEOUT` 值變更為非零值
1. 在您慣用的文字編輯器中開啟 `/etc/default/grub`
1. 將 `GRUB_HIDDEN_TIMEOUT=1` 行變成註解
1. `sudo update-grub`執行

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu 中的單一使用者模式
Ubuntu 會在無法正常開機時讓您進入單一使用者模式。 若要手動進入單一使用者模式，請依下列指示執行：

1. 從 GRUB，按 'e' 以編輯您的開機項目 (Ubuntu 項目)
1. 尋找開頭為 `linux` 的行，然後尋找 `ro`
1. 將 `single` 加到 `ro` 後方，並確定 `single` 前後都有一個空格
1. 按 Ctrl + X 以使用者些設定重新開機並進入單一使用者模式

## <a name="access-for-coreos"></a>CoreOS 的存取
CoreOS 中的單一使用者模式要求您必須啟用 GRUB。 

### <a name="grub-access-in-coreos"></a>CoreOS 中的 GRUB 存取
若要存取 GRUB，請在您的 VM 開機時按任意鍵。

### <a name="single-user-mode-in-coreos"></a>CoreOS 中的單一使用者模式
CoreOS 會在無法正常開機時讓您進入單一使用者模式。 若要手動進入單一使用者模式，請依下列指示執行：
1. 從 GRUB，按 'e' 以編輯您的開機項目
1. 尋找開頭為 `linux$` 的行。 應該有 2 個項目，包裝在在不同的 if/else 子句中
1. 將 `coreos.autologin=ttyS0` 附加到 2 個 `linux$` 行的結尾
1. 按 Ctrl + X 以使用者些設定重新開機並進入單一使用者模式

## <a name="access-for-suse-sles"></a>SUSE SLES 的存取
SLES 12 SP3 以上版本的新版映像允許在系統開機進入緊急模式時，透過序列主控台存取。 

### <a name="grub-access-in-suse-sles"></a>SUSE SLES 中的 GRUB 存取
SLES 中的 GRUB 存取要求必須透過 YaST 設定開機載入程式。 若要這樣做，請依照下列指示執行：

1. ssh 到您的 SLES VM 並執行 `sudo yast bootloader`。 使用 `tab` 鍵、`enter` 鍵與方向鍵來瀏覽功能表。 
1. 瀏覽到 `Kernel Parameters``Use serial console`。 
1. 將 `serial --unit=0 --speed=9600 --parity=no` 加到 Console 引數中

1. 按 F10 以儲存您的設定並結束
1. 若要進入 GRUBM，請將您的 VM 重新開機並在開機順序期間按任意鍵以讓 GRUB 維持顯示在畫面上
    - GRUB 的預設逾時是 1 秒。 您可以透過變更 `/etc/default/grub` 中的 `GRUB_TIMEOUT` 變數以變更此時間長度

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 中的單一使用者模式
若 SLES 無法正常開機，將會自動讓您進入緊急殼層。 若要手動進入緊急殼層，請依下列指示執行：

1. 從 GRUB，按 'e' 以編輯您的開機項目 (SLES 項目)
1. 尋找核心行，它的開頭是 `linux`
1. 將 `systemd.unit=emergency.target` 附加到行尾
1. 按 Ctrl + X 以使用者些設定重新開機並進入緊急殼層
   > 請注意，您會進入緊急殼層，而且檔案系統是_唯讀_的。 若要對任何檔案進行任何變更，您將必須使用讀取/寫入權限重新掛載檔案系統。 若要這樣做，請輸入 `mount -o remount,rw /` 到殼層

## <a name="access-for-oracle-linux"></a>Oracle Linux 的存取
與 Red Hat Enterprise Linux 非常像，Oracle Linux 中的單一使用者模式也要求必須啟用 GRUB 與 root 使用者。 

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux 中的 GRUB 存取
Oracle Linux 預設會啟用 GRUB。 若要進入 GRUB，請使用 `sudo reboot` 將您的 VM 重新開機並按 'Esc'。 您將會看到 GRUB 畫面。

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux 中的單一使用者模式
依照上述適用於 RHEL 的指示來在 Oracle Linux 中啟用單一使用者模式。

## <a name="next-steps"></a>後續步驟
* 主要序列主控台 Linux 檔頁面位於[這裡](serial-console.md)。
* 使用「序列主控台」來進行[NMI 和 SysRq 呼叫](serial-console-nmi-sysrq.md)
* 「序列主控台」也適用於 [Windows](../windows/serial-console.md) VM
* 深入了解[開機診斷](boot-diagnostics.md)
