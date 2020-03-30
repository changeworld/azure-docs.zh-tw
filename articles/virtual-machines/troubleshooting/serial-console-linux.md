---
title: 適用于 Linux 的 Azure 串列主控台 |微軟文檔
description: 用於 Azure 虛擬機器和虛擬機器縮放集的雙向串列主控台。
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
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167006"
---
# <a name="azure-serial-console-for-linux"></a>適用於 Linux 的 Azure 序列主控台

Azure 門戶中的串列主控台提供對 Linux 虛擬機器 （VM） 和虛擬機器縮放集實例的基於文本主控台的訪問。 此串列連接連接到 VM 或虛擬機器縮放集實例的 ttys0 序列埠，提供獨立于網路或作業系統狀態的訪問。 串列主控台只能通過使用 Azure 門戶進行訪問，並且僅允許具有"參與者"或更高存取權限的使用者訪問 VM 或虛擬機器規模集。

串列主控台的工作方式與 VM 和虛擬機器縮放集實例的工作方式相同。 在本文檔中，除非另有說明，否則對 VM 的所有提及都將隱式包含虛擬機器規模集實例。

有關 Windows 的串列主控台文檔，請參閱[Windows 的串列主控台](../windows/serial-console.md)。

> [!NOTE]
> 串列主控台通常位於全域 Azure 區域中，在 Azure 政府版中提供公共預覽版。 它在 Azure 中國雲中尚不可用。


## <a name="prerequisites"></a>Prerequisites

- VM 或虛擬機器規模集實例必須使用資源管理部署模型。 不支援傳統部署。

- 使用串列主控台的帳戶必須具有 VM 的[虛擬機器參與者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[引導診斷](boot-diagnostics.md)存儲帳戶

- 您的 VM 或虛擬機器規模集實例必須具有基於密碼的使用者。 您可以使用 VM 存取擴充的[重設密碼](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能來建立一個帳戶。 然後，選取 [支援與疑難排解]**** 區段中的 [重設密碼]****。

- VM 或虛擬機器規模集實例必須啟用[啟動診斷](boot-diagnostics.md)。

    ![開機診斷設定](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- 如需有關 Linux 發行版本的特定設定，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

- 必須為 上的`ttys0`串列輸出配置 VM 或虛擬機器規模集實例。 這是 Azure 映射的預設值，但您需要仔細檢查自訂映射。 詳情[如下](#custom-linux-images)。


> [!NOTE]
> 序列主控台需要一個已設定密碼的本機使用者。 僅配置 SSH 公開金鑰的 VM 或虛擬機器規模集將無法登錄到串列主控台。 若要建立具有密碼的本機使用者，請使用 [VM 存取擴充功能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) (可透過在入口網站中選取 [重設密碼]**** 來使用此功能)，然後建立具有密碼的本機使用者。
> 您也可以[使用 GRUB 來開機進入單一使用者模式](./serial-console-grub-single-user-mode.md)，以重設帳戶中的系統管理員密碼。

## <a name="serial-console-linux-distribution-availability"></a>串列主控台 Linux 發行版本可用性
若要讓序列主控台正確運作，必須設定讓客體作業系統將主控台訊息讀取並寫入至序列連接埠。 預設情況下，大多數[認可的 Azure Linux 發行版本](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都配置了串列主控台。 在 Azure 入口網站的 [支援與疑難排解]**** 區段中選取 [序列主控台]****，可以存取序列主控台。

> [!NOTE]
> 如果您在序列主控台中沒有看到任何項目，請確定您的 VM 上已啟用開機診斷。 點擊**Enter**通常會修復串列主控台中未顯示任何內容的問題。

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
若要啟用自訂 Linux VM 映像的序列主控台，請在 */etc/inittab* 檔案中啟用主控台存取以在 `ttyS0` 上執行終端機。 例如：`S0:12345:respawn:/sbin/agetty -L 115200 console vt102`。 您可能還需要在 ttyS0 上生成 getty。 這可以使用`systemctl start serial-getty@ttyS0.service`。

您還需要添加 ttys0 作為串列輸出的目標。 有關將自訂映射配置為使用串列主控台的詳細資訊，請參閱[在 Azure 中創建和上載 Linux VHD 時](https://aka.ms/createuploadvhd#general-linux-system-requirements)的總體系統要求。

如果您正在建置自訂核心，則可以考慮啟用這些核心旗標：`CONFIG_SERIAL_8250=y` 與 `CONFIG_MAGIC_SYSRQ_SERIAL=y`。 組態檔通常位於 */boot/* 路徑中。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>訪問串列主控台的常見方案

狀況          | 串列主控台中的操作
:------------------|:-----------------------------------------
中斷*的 FSTAB*檔 | 按下 **Enter** 鍵以繼續，並使用文字編輯器來修正 *FSTAB* 檔案。 您可能必須在單一使用者模式下，才能進行此操作。 有關詳細資訊，請參閱["如何修復 fstab 問題和](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)[使用串列主控台訪問 GRUB 和單使用者模式](serial-console-grub-single-user-mode.md)"的串列主控台部分。
不正確的防火牆規則 |  如果已配置 iptables 來阻止 SSH 連接，則可以使用串列主控台與 VM 交互，而無需 SSH。 更多細節可以在[iptables 人頁](https://linux.die.net/man/8/iptables)中找到。<br>同樣，如果您的防火牆阻止 SSH 訪問，則可以通過串列主控台訪問 VM 並重新配置防火牆。 更多詳細資訊請參閱[防火牆文檔](https://firewalld.org/documentation/)。
檔案系統損毀/檢查 | 有關使用串列主控台對損壞的檔案系統進行故障排除的更多詳細資訊，請參閱 Azure Linux VM 的串列主控台部分[無法啟動，因為檔案系統錯誤](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck)。
SSH 配置問題 | 存取序列主控台，然後變更設定。 無論 VM 的 SSH 配置如何，都可以使用串列主控台，因為它不需要 VM 具有網路連接即可工作。 故障排除指南可用於對 Azure [Linux VM 的 SSH 連接進行故障排除，該連接失敗、錯誤或被拒絕](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)。 有關在 Azure[中連接到 Linux VM 的問題的詳細 SSH 故障排除步驟提供了](./detailed-troubleshoot-ssh-connection.md)更多詳細資訊
與開機載入器互動 | 從序列主控台刀鋒視窗內重新啟動您的 VM，以存取 Linux VM 上的 GRUB。 有關詳細資訊和特定于發行版本的資訊，請參閱[使用串列主控台訪問 GRUB 和單使用者模式](serial-console-grub-single-user-mode.md)。

## <a name="disable-the-serial-console"></a>禁用串列主控台

預設情況下，所有訂閱都啟用了串列主控台存取權限。 您可以在訂閱級別或 VM/虛擬機器規模集級別禁用串列主控台。 有關詳細說明，請訪問[啟用和禁用 Azure 串列主控台](./serial-console-enable-disable.md)。

## <a name="serial-console-security"></a>序列主控台安全性

### <a name="access-security"></a>存取安全性
對序列主控台的存取權，僅限於對虛擬機器具有[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高級別存取角色的使用者。 如果 Azure Active Directory 租用戶需要 Multi-Factor Authentication (MFA)，則存取序列主控台也會需要 MFA，因為序列主控台是透過 [Azure 入口網站](https://portal.azure.com)進行存取。

### <a name="channel-security"></a>通道安全性
在網路上來回傳送的所有資料都會經過加密。

### <a name="audit-logs"></a>稽核記錄
目前對序列主控台進行的所有存取都會記錄在虛擬機器的[開機診斷](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)記錄中。 這些記錄的存取權均由 Azure 虛擬機器系統管理員所擁有和控制。

> [!CAUTION]
> 不會記錄主控台的存取密碼。 但是，如果在主控台內執行的命令包含或輸出密碼、祕密、使用者名稱，或任何其他形式的個人識別資訊 (PII)，則這些命令將會寫入至 VM 開機診斷記錄。 它們將與所有其他可見文字一起寫入，作為序列主控台回滾函式實作的一部分。 這些記錄是循環的，只有具有診斷儲存體帳戶讀取權限的個人才能存取它們。 如果您輸入任何包含機密或 PII 的資料器命令，我們建議您使用 SSH，除非絕對必要串列主控台。

### <a name="concurrent-usage"></a>並行使用方式
如果在某位使用者已連線至序列主控台的情況下，另一位使用者成功取得該相同虛擬機器的存取權，系統就會將第一位使用者中斷連線，而讓第二位使用者連線至相同的工作階段。

> [!CAUTION]
> 這意味著斷開連接的使用者不會被登出。在斷開連接時強制執行登出（通過使用 SIGHUP 或類似機制）的能力仍在路線圖中。 就 Windows 而言，在特殊系統管理主控台 (SAC) 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。 為此，請在您用以登入主控台之使用者的 *.bash_profile* 或 *.profile* 檔案中新增 `export TMOUT=600`。 此設定將在 10 分鐘後逾時。

## <a name="accessibility"></a>協助工具選項
協助工具是 Azure 串列主控台的一個關鍵焦點。 為此，我們已確認序列主控台完全可供存取。

### <a name="keyboard-navigation"></a>鍵盤導覽
使用鍵盤上的 **Tab** 鍵，在 Azure 入口網站中的序列主控台介面上導覽。 您的位置會在螢幕上醒目顯示。 要保持串列主控台視窗的焦點，請按鍵盤上的**Ctrl**+**F6。**

### <a name="use-serial-console-with-a-screen-reader"></a>將串列主控台與螢幕閱讀器一起使用
序列主控台具備內建的螢幕助讀程式支援。 在開啟螢幕助讀程式的情況下瀏覽，可讓螢幕助讀程式大聲讀出目前所選按鈕的替代文字。

## <a name="known-issues"></a>已知問題
我們知道串列主控台和 VM 的作業系統有一些問題。 下面是這些問題的清單以及用於緩解 Linux VM 的步驟。 這些問題和緩解措施適用于 VM 和虛擬機器規模集實例。 如果這些錯誤與您看到的錯誤不匹配，請參閱[常見串列主控台錯誤](./serial-console-errors.md)中常見的串列主控台服務錯誤。

問題                           |   降低
:---------------------------------|:--------------------------------------------|
在連線橫幅之後按下 **Enter** 鍵並不會顯示登入提示。 | GRUB 可能配置不正確。 運行以下命令：`grub2-mkconfig -o /etc/grub2-efi.cfg`和/或`grub2-mkconfig -o /etc/grub2.cfg`。 如需詳細資訊，請參閱[按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)。 如果您正在運行導致 Linux 無法連接到序列埠的自訂 VM、強化設備或 GRUB 配置，則可能會出現此問題。
序列主控台文字只會佔用部分的螢幕大小 (通常在使用文字編輯器之後)。 | 序列主控台不支援對視窗大小進行交涉 ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))，這表示系統將不會傳送 SIGWINCH 訊號以更新螢幕大小，而且 VM 將無法得知您終端機的大小。 安裝 xterm 或類似的公用程式，以向您提供 `resize` 命令，然後再執行 `resize`。
貼上長字串沒有作用。 | 序列主控台會將貼上至終端機的字串長度限制為 2048 個字元，以防止多載序列連接埠頻寬。
SLES BYOS 圖像中的鍵盤輸入不穩定。 鍵盤輸入僅偶爾識別。 | 這是普利茅斯套餐的問題。 普利茅斯不應在 Azure 中運行，因為您不需要初始螢幕，普利茅斯干擾了平臺使用串列主控台的功能。 刪除普利茅斯，`sudo zypper remove plymouth`然後重新開機。 或者，通過追加到`plymouth.enable=0`行的末尾來修改 GRUB 配置的內核行。 可以通過[在啟動時編輯引導條目](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)，或者編輯GRUB_CMDLINE_LINUX`/etc/default/grub`行，使用`grub2-mkconfig -o /boot/grub2/grub.cfg`重建 GRUB，然後重新開機來執行此操作。


## <a name="frequently-asked-questions"></a>常見問題集

**問：如何發送回饋？**

A. 在 https://aka.ms/serialconsolefeedback 建立 GitHub 問題來提供意見反應。 或者，您也可以透過 azserialhelp@microsoft.com 或在 https://feedback.azure.com 的虛擬機器類別中傳送意見反應 (較不建議)。

**問：串列主控台是否支援複製/粘貼？**

A. 是。 使用**Ctrl**+**移位**+**C**和**Ctrl**+**移位**+**V**複製並粘貼到終端中。

**問：我可以使用串列主控台而不是 SSH 連接嗎？**

A. 雖然這在技術上應該是可行的，但序列主控台主要是要作為無法透過 SSH 進行連線時的疑難排解工具。 基於以下兩個原因，建議您不要以序列主控台取代 SSH：

- 序列主控台沒有如 SSH 一樣多的頻寬。 因為它是純文字的連線，所以很難進行更多 GUI 大量互動。
- 目前，只有透過使用使用者名稱和密碼才能存取序列主控台。 因為 SSH 金鑰的安全性遠高於使用者名稱/密碼的組合，因此就登入安全性的觀點而言，我們建議 SSH 應比序列主控台優先使用。

**問：誰可以為我的訂閱啟用或禁用串列主控台？**

A. 若要以訂用帳戶層級的規模啟用或停用序列主控台，您必須有該訂用帳戶的寫入權限。 具有寫入權限的角色包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

**問：誰可以訪問 VM/虛擬機器規模集的串列主控台？**

A. 對於 VM 或虛擬機器規模集，必須具有虛擬機器參與者角色或更高角色才能訪問串列主控台。

**問：我的串列主控台未顯示任何內容，我該怎麼辦？**

A. 您映像的序列主控台存取設定很有可能是錯誤的。 如需有關設定您的映像以啟用序列主控台的詳細資訊，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

**問：串列主控台是否可用於虛擬機器規模集？**

A. 是的，它是！ 有關[虛擬機器縮放集，請參閱串列主控台](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**問：如果我僅使用 SSH 金鑰身份驗證設置 VM 或虛擬機器規模設置，我是否仍可以使用串列主控台連接到 VM/虛擬機器規模集實例？**

A. 是。 因為序列主控台並不需要 SSH 金鑰，因此您只需要設定使用者名稱/密碼組合就可以。 您可以透過在 Azure 入口網站中選取 [重設密碼]**** 並使用這些認證登入序列主控台來執行此操作。

## <a name="next-steps"></a>後續步驟
* 使用序列主控台來[存取 GRUB 與單一使用者模式](serial-console-grub-single-user-mode.md)。
* 使用序列主控台來進行 [NMI 和 SysRq 呼叫](serial-console-nmi-sysrq.md)。
* 瞭解如何使用串列主控台[在各種發行版本中啟用 GRUB](serial-console-grub-proactive-configuration.md)
* 串列主控台也可用於 Windows [VM。](../windows/serial-console.md)
* 瞭解有關[引導診斷](boot-diagnostics.md)的更多資訊。

