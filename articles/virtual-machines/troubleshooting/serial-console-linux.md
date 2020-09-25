---
title: 適用于 Linux 的 Azure 序列主控台 |Microsoft Docs
description: 使用 Linux 範例的 Azure 虛擬機器和虛擬機器擴展集的雙向序列主控台。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 9a31a22a5b037162198f594d9bcf35c91a0a4654
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306866"
---
# <a name="azure-serial-console-for-linux"></a>適用於 Linux 的 Azure 序列主控台

Azure 入口網站中的序列主控台可讓您存取 Linux 虛擬機器的文字型主控台 (Vm) 和虛擬機器擴展集實例。 此序列連線會連接到 VM 或虛擬機器擴展集實例的 ttys0 序列埠，以提供與網路或作業系統狀態無關的存取權。 您只能使用 Azure 入口網站來存取序列主控台，且只允許對 VM 或虛擬機器擴展集具有「參與者」或更高存取角色的使用者進行存取。

序列主控台的運作方式與 VM 和虛擬機器擴展集執行個體相同。 在本文件中，除非另有指示，否則所有提及的 VM 都隱含虛擬機器擴展集執行個體。

序列主控台已在全球 Azure 區域正式推出，並在 Azure Government 公開預覽。 目前尚未在「Azure 中國」雲端中提供序列主控台。

如需 Windows 的序列主控台檔，請參閱 [適用于 windows 的序列主控台](./serial-console-windows.md)。

> [!NOTE]
> 序列主控台目前與受控開機診斷儲存體帳戶不相容。 若要使用序列主控台，請確定您使用的是自訂的儲存體帳戶。

## <a name="prerequisites"></a>必要條件

- VM 或虛擬機器擴展集執行個體必須使用資源管理部署模型。 不支援傳統部署。

- 使用序列主控台的帳戶必須具有 VM 的[虛擬機器參與者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[開機診斷](boot-diagnostics.md)儲存體帳戶

- VM 或虛擬機器擴展集執行個體必須有以密碼為基礎的使用者。 您可以使用 VM 存取擴充的[重設密碼](../extensions/vmaccess.md#reset-password)功能來建立一個帳戶。 然後，選取 [支援與疑難排解] 區段中的 [重設密碼]。

- 您的 VM 或虛擬機器擴展集實例必須啟用 [開機診斷](boot-diagnostics.md) 。

    ![開機診斷設定](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- 如需有關 Linux 發行版本的特定設定，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

- 您的 VM 或虛擬機器擴展集實例必須設定為的序列輸出 `ttys0` 。 這是 Azure 映射的預設值，但您會想要在自訂映射上再次檢查這項功能。 詳細資料 [如下](#custom-linux-images)。


> [!NOTE]
> 序列主控台需要一個已設定密碼的本機使用者。 只有使用 SSH 公開金鑰設定的 Vm 或虛擬機器擴展集無法登入序列主控台。 若要建立具有密碼的本機使用者，請使用 [VM 存取擴充功能](../extensions/vmaccess.md) (可透過在入口網站中選取 [重設密碼]**** 來使用此功能)，然後建立具有密碼的本機使用者。
> 您也可以[使用 GRUB 來開機進入單一使用者模式](./serial-console-grub-single-user-mode.md)，以重設帳戶中的系統管理員密碼。

## <a name="serial-console-linux-distribution-availability"></a>序列主控台 Linux 發行版本可用性
若要讓序列主控台正確運作，必須設定讓客體作業系統將主控台訊息讀取並寫入至序列連接埠。 最 [背書的 Azure Linux 發行](../linux/endorsed-distros.md) 版預設會設定序列主控台。 在 Azure 入口網站的 [支援與疑難排解]**** 區段中選取 [序列主控台]****，可以存取序列主控台。

> [!NOTE]
> 如果您在序列主控台中沒有看到任何項目，請確定您的 VM 上已啟用開機診斷。 按 **Enter** 通常可修正序列主控台中未顯示任何專案的問題。

散發      | 序列主控台存取
:-----------|:---------------------
Red Hat Enterprise Linux    | 預設啟用的序列主控台存取。
CentOS      | 預設啟用的序列主控台存取。
Debian      | 預設啟用的序列主控台存取。
Ubuntu      | 預設啟用的序列主控台存取。
CoreOS      | 預設啟用的序列主控台存取。
SUSE        | Azure 上提供的較新 SLES 映像已預設啟用序列主控台存取。 如果您使用 Azure 上較舊的 SLES 版本 (10 或更舊)，請依照這篇 [KB 文章](https://www.novell.com/support/kb/doc.php?id=3456486) \(英文\) 來啟用序列主控台。
Oracle Linux        | 預設啟用的序列主控台存取。

### <a name="custom-linux-images"></a>自訂 Linux 映像
若要啟用自訂 Linux VM 映像的序列主控台，請在 */etc/inittab* 檔案中啟用主控台存取以在 `ttyS0` 上執行終端機。 例如：`S0:12345:respawn:/sbin/agetty -L 115200 console vt102`。 您也可能需要在 ttyS0 上產生 getty。 這可以透過來完成 `systemctl start serial-getty@ttyS0.service` 。

您也會想要將 ttys0 新增為序列輸出的目的地。 如需有關設定自訂映射以使用序列主控台的詳細資訊，請參閱在 [Azure 中建立和上傳 LINUX VHD](https://aka.ms/createuploadvhd#general-linux-system-requirements)的一般系統需求。

如果您正在建置自訂核心，則可以考慮啟用這些核心旗標：`CONFIG_SERIAL_8250=y` 與 `CONFIG_MAGIC_SYSRQ_SERIAL=y`。 組態檔通常位於 */boot/* 路徑中。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>存取序列主控台的常見案例

狀況          | 序列主控台中的動作
:------------------|:-----------------------------------------
中斷的 *FSTAB* 檔案 | 按下 **Enter** 鍵以繼續，並使用文字編輯器來修正 *FSTAB* 檔案。 您可能必須在單一使用者模式下，才能進行此操作。 如需詳細資訊，請參閱序列主控台一節， [以瞭解如何修正 fstab 問題](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) ，並 [使用序列主控台來存取 GRUB 和單一使用者模式](serial-console-grub-single-user-mode.md)。
不正確的防火牆規則 |  如果您已將 iptables 設定為封鎖 SSH 連線能力，您可以使用序列主控台來與您的 VM 進行互動，而不需要 SSH。 您可以在 [iptables man 頁面](https://linux.die.net/man/8/iptables)找到更多詳細資料。<br>同樣地，如果您的 firewalld 會封鎖 SSH 存取，您可以透過序列主控台存取 VM，並重新設定 firewalld。 您可以在 [firewalld 檔](https://firewalld.org/documentation/)中找到更多詳細資料。
檔案系統損毀/檢查 | 請參閱 Azure Linux VM 的序列主控台區段 [無法啟動，因為檔案系統錯誤](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) ，如需使用序列主控台針對損毀檔案系統進行疑難排解的詳細資訊。
SSH 設定問題 | 存取序列主控台，然後變更設定。 無論 VM 的 SSH 設定為何，都可以使用序列主控台，因為它不需要讓 VM 具備網路連線能力就能運作。 疑難排解指南適用于對 [失敗、發生錯誤或被拒的 Azure LINUX VM 的 SSH 連線進行疑難排解](./troubleshoot-ssh-connection.md)。 如需更多詳細資料，請參閱在 [Azure 中連線至 LINUX VM 之問題的詳細 SSH 疑難排解步驟](./detailed-troubleshoot-ssh-connection.md)
與開機載入器互動 | 從序列主控台刀鋒視窗內重新啟動您的 VM，以存取 Linux VM 上的 GRUB。 如需詳細資訊和發行版本特定的資訊，請參閱 [使用序列主控台來存取 GRUB 和單一使用者模式](serial-console-grub-single-user-mode.md)。

## <a name="disable-the-serial-console"></a>停用序列主控台

根據預設，所有訂用帳戶都已啟用序列主控台存取。 您可在訂用帳戶層級或 VM/虛擬機器擴展集層級停用序列主控台。 如需詳細指示，請瀏覽[啟用和停用 Azure 序列主控台](./serial-console-enable-disable.md)。

## <a name="serial-console-security"></a>序列主控台安全性

### <a name="access-security"></a>存取安全性
對序列主控台的存取權，僅限於對虛擬機器具有[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高級別存取角色的使用者。 如果 Azure Active Directory 租用戶需要 Multi-Factor Authentication (MFA)，則存取序列主控台也會需要 MFA，因為序列主控台是透過 [Azure 入口網站](https://portal.azure.com)進行存取。

### <a name="channel-security"></a>通道安全性
在網路上來回傳送的所有資料都會經過加密。

### <a name="audit-logs"></a>稽核記錄
目前對序列主控台進行的所有存取都會記錄在虛擬機器的[開機診斷](./boot-diagnostics.md)記錄中。 這些記錄的存取權均由 Azure 虛擬機器系統管理員所擁有和控制。

> [!CAUTION]
> 不會記錄主控台的存取密碼。 但是，如果在主控台內執行的命令包含或輸出密碼、祕密、使用者名稱，或任何其他形式的個人識別資訊 (PII)，則這些命令將會寫入至 VM 開機診斷記錄。 它們將與所有其他可見文字一起寫入，作為序列主控台回滾函式實作的一部分。 這些記錄是循環的，只有具有診斷儲存體帳戶讀取權限的個人才能存取它們。 如果您輸入任何包含秘密或 PII 的 dataor 命令，除非需要序列主控台，否則建議使用 SSH。

### <a name="concurrent-usage"></a>並行使用方式
如果在某位使用者已連線至序列主控台的情況下，另一位使用者成功取得該相同虛擬機器的存取權，系統就會將第一位使用者中斷連線，而讓第二位使用者連線至相同的工作階段。

> [!CAUTION]
> 這表示已中斷連線的使用者將不會登出。在中斷連線 (使用 SIGHUP 或類似的機制來強制執行登出的能力，) 仍在藍圖中。 就 Windows 而言，在特殊系統管理主控台 (SAC) 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。 為此，請在您用以登入主控台之使用者的 *.bash_profile* 或 *.profile* 檔案中新增 `export TMOUT=600`。 此設定將在 10 分鐘後逾時。

## <a name="accessibility"></a>協助工具選項
協助工具是 Azure 序列主控台的主要焦點。 為此，我們已確認序列主控台完全可供存取。

### <a name="keyboard-navigation"></a>鍵盤導覽
使用鍵盤上的 **Tab** 鍵，在 Azure 入口網站中的序列主控台介面上導覽。 您的位置會在螢幕上醒目顯示。 若要離開序列主控台視窗的焦點，請在鍵盤上按 **Ctrl**+**F6**。

### <a name="use-serial-console-with-a-screen-reader"></a>使用序列主控台搭配螢幕閱讀程式
序列主控台具備內建的螢幕助讀程式支援。 在開啟螢幕助讀程式的情況下瀏覽，可讓螢幕助讀程式大聲讀出目前所選按鈕的替代文字。

## <a name="known-issues"></a>已知問題
我們已了解序列主控台和 VM 作業系統的一些問題。 以下是這些問題的清單，以及針對 Linux Vm 進行緩和的步驟。 這些問題和緩解措施適用於 VM 和虛擬機器擴展集執行個體。 如果這些不符合所看到的錯誤，請參閱[常見的序列主控台錯誤](./serial-console-errors.md)中常見的序列主控台服務錯誤。

問題                           |   降低
:---------------------------------|:--------------------------------------------|
在連線橫幅之後按下 **Enter** 鍵並不會顯示登入提示。 | GRUB 可能未正確設定。 執行下列命令： `grub2-mkconfig -o /etc/grub2-efi.cfg` 和/或 `grub2-mkconfig -o /etc/grub2.cfg` 。 如需詳細資訊，請參閱[按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)。 如果您執行的是自訂 VM、強化設備，或是導致 Linux 無法連線到序列埠的 GRUB 設定，就會發生此問題。
序列主控台文字只會佔用部分的螢幕大小 (通常在使用文字編輯器之後)。 | 序列主控台不支援對視窗大小進行交涉 ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))，這表示系統將不會傳送 SIGWINCH 訊號以更新螢幕大小，而且 VM 將無法得知您終端機的大小。 安裝 xterm 或類似的公用程式，以向您提供 `resize` 命令，然後再執行 `resize`。
貼上長字串沒有作用。 | 序列主控台會將貼上至終端機的字串長度限制為 2048 個字元，以防止多載序列連接埠頻寬。
SLES BYOS 映射中的鍵盤輸入不穩定。 鍵盤輸入只會偶爾被辨識。 | 這是 Plymouth 套件的問題。 Plymouth 不應該在 Azure 中執行，因為您不需要啟動顯示畫面，而 Plymouth 會干擾平臺使用序列主控台的能力。 移除 Plymouth `sudo zypper remove plymouth` ，然後重新開機。 或者，將 GRUB 設定的核心行附加至該行的結尾，以修改 GRUB 設定的核心行 `plymouth.enable=0` 。 若要這麼做，您可以 [在開機時編輯開機專案](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)，或在中編輯 GRUB_CMDLINE_LINUX 行，然後以 `/etc/default/grub` 重建 GRUB，然後 `grub2-mkconfig -o /boot/grub2/grub.cfg` 重新開機。


## <a name="frequently-asked-questions"></a>常見問題集

**問：如何傳送意見反應？**

A. 在 https://aka.ms/serialconsolefeedback 建立 GitHub 問題來提供意見反應。 或者，您也可以透過 azserialhelp@microsoft.com 或在 https://feedback.azure.com 的虛擬機器類別中傳送意見反應 (較不建議)。

**問：序列主控台是否支援複製/貼上？**

A. 是。 請使用 **Ctrl**+**Shift**+**C** 與 **Ctrl**+**Shift**+**V** 來針對終端機進行複製及貼上。

**問。我可以使用序列主控台而不是 SSH 連接嗎？**

A. 雖然這在技術上應該是可行的，但序列主控台主要是要作為無法透過 SSH 進行連線時的疑難排解工具。 基於以下兩個原因，建議您不要以序列主控台取代 SSH：

- 序列主控台沒有如 SSH 一樣多的頻寬。 因為它是純文字的連線，所以很難進行更多 GUI 大量互動。
- 目前，只有透過使用使用者名稱和密碼才能存取序列主控台。 因為 SSH 金鑰的安全性遠高於使用者名稱/密碼的組合，因此就登入安全性的觀點而言，我們建議 SSH 應比序列主控台優先使用。

**問。誰可以啟用或停用訂用帳戶的序列主控台？**

A. 若要以訂用帳戶層級的規模啟用或停用序列主控台，您必須有該訂用帳戶的寫入權限。 具有寫入權限的角色包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

**問。誰可以存取我的 VM/虛擬機器擴展集的序列主控台？**

A. 您必須擁有 VM 或虛擬機器擴展集的「虛擬機器參與者」角色或更高許可權，才能存取序列主控台。

**問：我的序列主控台沒有顯示任何內容，該怎麼辦？**

A. 您映像的序列主控台存取設定很有可能是錯誤的。 如需有關設定您的映像以啟用序列主控台的詳細資訊，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

**問：序列主控台是否適用於虛擬機器擴展集？**

A. 是，它適用！ 請參閱[虛擬機器擴展集的序列主控台](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**問。如果我只使用 SSH 金鑰驗證來設定 VM 或虛擬機器擴展集，我是否仍然可以使用序列主控台來連線到我的 VM/虛擬機器擴展集實例？**

A. 是。 因為序列主控台並不需要 SSH 金鑰，因此您只需要設定使用者名稱/密碼組合就可以。 您可以透過在 Azure 入口網站中選取 [重設密碼]**** 並使用這些認證登入序列主控台來執行此操作。

## <a name="next-steps"></a>後續步驟
* 使用序列主控台來[存取 GRUB 與單一使用者模式](serial-console-grub-single-user-mode.md)。
* 使用序列主控台來進行 [NMI 和 SysRq 呼叫](serial-console-nmi-sysrq.md)。
* 瞭解如何使用序列主控台 [在各種散發版本中啟用 GRUB](serial-console-grub-proactive-configuration.md)
* 序列主控台也適用于 [Windows vm](./serial-console-windows.md)。
* 深入了解[開機診斷](boot-diagnostics.md)。
