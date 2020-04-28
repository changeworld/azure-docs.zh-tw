---
title: 使用 Azure Site Recovery 針對 VMware VM 嚴重損壞修復中的容錯回復進行疑難排解
description: 本文說明對使用 Azure Site Recovery 從 VMWare VM 災害復原至 Azure 期間所發生的容錯回復和重新保護問題進行疑難排解的方式。
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75498099"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>針對從 Azure 至內部部署環境的容錯回復進行疑難排解

本文說明如何排解您在使用 [Azure Site Recovery](site-recovery-overview.md) 執行容錯移轉後從 Azure VM 容錯回復至內部部署 VMware 基礎結構時可能遇到的問題。

容錯回復基本上涉及兩個主要步驟。 第一個步驟是容錯移轉之後，您必須重新保護移轉至內部部署的 Azure VM，使其能夠開始進行複寫。 第二個步驟是從 Azure 執行容錯移轉，以容錯回復到您的內部部署網站。

## <a name="common-issues"></a>常見問題

- 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，保護會成功且容錯回復可作用。 在重新保護期間，容錯回復會失敗，因為無法探索資料存放區。 重新保護期間未列出資料存放區是容錯失敗的徵兆。 若要解決此問題，您可以使用具有適當權限的帳戶更新 vCenter 認證，然後重試作業。
- 當您將 Linux 虛擬機器容錯回復並在內部部署執行它時，您會看到 Network Manager 封裝已從該機器解除安裝。 發生此解除安裝的原因是，在 Azure 中復原虛擬機器時，網路管理員套件已移除。
- 當 Linux 虛擬機器已設定靜態 IP 位址且容錯移轉至 Azure 時，就會從 DHCP 取得 IP 位址。 當您容錯移轉至內部部署時，該虛擬機器會繼續使用 DHCP 取得 IP 位址。 視需要手動登入機器，然後將 IP 位址重新設定為靜態位址。 Windows 虛擬機器可以重新取得其靜態位址 IP。
- 如果您使用 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版，則容錯移轉會成功，但容錯回復不會成功。 若要啟用容錯回復，請升級到上述程式的評估授權。
- 若無法從處理伺服器連線至組態伺服器，請使用 Telnet 檢查在連接埠 443 上連至組態伺服器的連線。 您也可以嘗試從處理伺服器 Ping 組態伺服器。 當處理序伺服器已連線至組態伺服器時，應該會有活動訊號。
- 以實體內部部署伺服器的形式受到保護的 Windows Server 2008 R2 SP1 伺服器無法從 Azure 容錯回復至內部部署網站。
- 在下列情況下，您無法容錯回復：
    - 已將機器遷移至 Azure。 [深入了解](migrate-overview.md#what-do-we-mean-by-migration)。
    - 已將 VM 移至另一個資源群組。
    - 已刪除 Azure VM。
    - 已停用 VM 的保護。
    - 已在 Azure 中手動建立 VM。 機器一開始應該在內部部署環境中受到保護，並於容錯移轉至 Azure 後再重新受保護。
    - 您只能容錯回復至 ESXi 主機。 您不能將 VMware VM 或實體伺服器容錯回復至 Hyper-V 主機、實體機器或 VMware 工作站。


## <a name="troubleshoot-reprotection-errors"></a>對重新保護錯誤進行疑難排解

本節詳述常見的重新保護錯誤及其更正方式。

### <a name="error-code-95226"></a>錯誤碼 95226

重新保護失敗，因為 Azure 虛擬機器無法觸達內部部署組態伺服器。****

此錯誤的發生條件如下：

* Azure VM 無法連線到內部部署組態伺服器。 無法探索到 VM 並將其註冊至組態伺服器。
* 在容錯移轉之後，InMage Scout 應用程式服務未執行於 Azure VM 上。 必須有此服務，才能與內部部署組態伺服器進行通訊。

若要解決此問題：

* 確認 Azure VM 網路允許 Azure VM 與內部部署組態設定伺服器進行通訊。 您可以將站對站 VPN 設為您的內部部署資料中心，或是使用 Azure VM 之虛擬網路上的私人對等互連來設定 Azure ExpressRoute 連線。
* 如果 VM 可以使用內部部署組態伺服器通訊，則請登入 VM。 然後檢查 InMage Scout 應用程式服務。 如果您看到其未執行，請手動啟動服務。 檢查服務啟動類型是否設定為 [自動]****。

### <a name="error-code-78052"></a>錯誤碼 78052

**無法為虛擬機器完成保護。**

在您要容錯回復的主要目標伺服器上已有同名的 VM 存在，就可能發生此問題。

若要解決此問題：

* 在不同的主機上選取不同的主要目標伺服器，使重新保護可在不同的主機上建立機器，而其中的名稱不會衝突。
* 您也可以使用 vMotion 將主要目標移到不同的主機，而其中的名稱不會發生衝突。 如果現有的 VM 是偏離機器，您可以將其重新命名，以在相同的 ESXi 主機上建立新的 VM。


### <a name="error-code-78093"></a>錯誤碼 78093

**VM 未執行，處於停止回應狀態或無法存取。**

若要解決此問題：

若要重新保護已容錯移轉的 VM，Azure VM 必須執行，如此一來，行動服務就會向組態伺服器內部部署進行註冊，並可與流程伺服器通訊，開始進行複寫。 如果電腦在不正確的網路上或未執行（沒有回應或關機），設定伺服器就無法連線到 VM 上的行動服務以開始重新保護。

* 請重新啟動 VM，讓它能夠開始通訊回內部部署。
* 啟動 Azure 虛擬機器之後，重新啟動重新保護作業。

### <a name="error-code-8061"></a>錯誤碼 8061

**無法從 ESXi 主機存取資料存放區。**

查看容錯回復的[主要目標必要條件和支援的資料存放區](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server)。


## <a name="troubleshoot-failback-errors"></a>對容錯回復錯誤進行疑難排解

本節說明您在容錯回復期間可能會遇到的常見錯誤。

### <a name="error-code-8038"></a>錯誤碼 8038

**因為發生錯誤，所以無法啟動內部部署虛擬機器。**

此問題可能會在未佈建足夠記憶體的主機上啟動內部部署 VM 時發生。 

若要解決此問題：

* 在 ESXi 主機上佈建更多記憶體。
* 此外，您可以使用 vMotion 將虛擬機器移到另一部有足夠記憶體可啟動 VM 的 ESXi 主機。
