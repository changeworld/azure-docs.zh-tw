---
title: Azure Site Recovery 中的實體伺服器嚴重損壞修復架構
description: 本文概述使用 Azure Site Recovery 服務將內部部署實體伺服器災害復原至 Azure 期間，所使用的元件和架構。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 966636e269043d81912b552711635f34b412f22f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654722"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>實體伺服器至 Azure 的災害復原架構

本文說明透過 [Azure Site Recovery](site-recovery-overview.md) 服務，在內部部署網站與 Azure 之間複寫、容錯移轉和復原 Windows 和 Linux 實體伺服器時所使用的架構和程序。

## <a name="architectural-components"></a>架構元件

下表和圖形提供實體伺服器複寫至 Azure 時所用元件的高層級觀點。

| **元件** | **需求** | **詳細資料** |
| --- | --- | --- |
| **Azure** | Azure 訂用帳戶和 Azure 網路。 | 從內部部署實體機器複寫的資料會儲存在 Azure 受控磁片中。 從內部部署環境容錯移轉至 Azure 時，會以複寫的資料建立 Azure VM。 Azure VM 在建立後會連線到 Azure 虛擬網路。 |
| **進程伺服器** | 預設會與組態伺服器一起安裝。 | 會做為複寫閘道器。 接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。<br/><br/> 處理序伺服器也會在您要複寫的伺服器上安裝行動服務。<br/><br/> 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。 |
| **主要目標伺服器** | 預設會與組態伺服器一起安裝。 | 從 Azure 容錯回復期間，處理複寫資料。<br/><br/> 針對大型部署，您可以新增額外的個別主要目標伺服器進行容錯回復。 |
| **複寫的伺服器** | 行動服務會安裝在您複寫的每部伺服器上。 | 我們建議您允許從處理序伺服器自動安裝。 或者，您可以手動安裝服務，或使用自動部署方法（例如 Configuration Manager）。 |

**實體至 Azure 架構**

![元件](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>設定輸出網路連線能力

如需 Site Recovery 如預期般運作，您必須修改輸出網路連線能力，以允許您的環境進行複寫。

> [!NOTE]
> Site Recovery 不支援使用驗證 Proxy 來控制網路連線能力。

### <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 URL：

| **名稱**                  | **商業**                               | **政府**                                 | **說明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 儲存體                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 提供 Site Recovery 服務 URL 的授權和驗證。 |
| 複寫               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允許 VM 與 Site Recovery 服務進行通訊。 |
| 服務匯流排               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允許 VM 寫入 Site Recovery 監視和診斷資料。 |


## <a name="replication-process"></a>複寫程序

1. 您需設定部署，包括內部部署和 Azure 元件。 在 Recovery Services 保存庫中，您需指定複寫來源和目標、設定組態伺服器、建立複寫原則，以及啟用複寫。
1. 機器會使用複寫原則進行複寫，並將伺服器資料的初始複本複寫到 Azure 儲存體。
1. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 電腦的追蹤變更會保存在副檔名為 _hrl_ 的檔案中。
   - 電腦會與 HTTPS 埠443輸入上的設定伺服器通訊，以進行複寫管理。
   - 電腦會將複寫資料傳送至 HTTPS 埠9443輸入 (上的進程伺服器，可) 修改。
   - 設定伺服器會透過 HTTPS 埠443輸出來協調 Azure 的複寫管理。
   - 進程伺服器會從來源機器接收資料、將其優化並加密，然後透過 HTTPS 埠443輸出將其傳送至 Azure 儲存體。
   - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 如果您將多部機器群組為幾個共用當機時保持一致復原點和應用程式一致復原點的複寫群組，當這些群組在進行容錯移轉時，便會使用多部 VM。 如果電腦執行相同的工作負載且需要一致，這些群組就很有用。
1. 流量透過網際網路複寫到 Azure 儲存體的公用端點。 或者，您可以使用 Azure ExpressRoute [公用對等互連](../expressroute/about-public-peering.md)。

   > [!NOTE]
   > 從內部部署網站或 Azure ExpressRoute 私用對 [等互連](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)的站對站 VPN 不支援複寫。

**實體到 Azure 複寫程序**

![複寫程序](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

設定複寫之後，您可以執行嚴重損壞修復演練 (測試容錯移轉) 檢查一切是否如預期般運作。 然後，您可以視需要進行容錯移轉和容錯回復。 請考慮下列項目：

- 不支援有計劃的容錯移轉。
- 需要容錯回復至內部部署 VMware VM。 您需要內部部署 VMware 基礎結構，即使是將內部部署實體伺服器複寫至 Azure。
- 您可容錯移轉單一機器，或建立復原方案，同時容錯移轉多部機器。
- 當您執行容錯移轉時，系統會從 Azure 儲存體中複寫的資料建立 Azure VM。
- 觸發初始容錯移轉之後，您會認可它以開始從 Azure VM 存取工作負載。
- 當主要的內部部署網站恢復可用狀態時，您就可以容錯回復。
- 設定容錯回復基礎結構，包括：
  - **Azure 中的暫存進程伺服器**：若要從 azure 容錯回復，您可以設定 azure VM 作為進程伺服器，以處理來自 azure 的複寫。 您可以在容錯回復完成後刪除此 VM。
  - **VPN 連線**：若要容錯回復，您需要有從 Azure 網路到內部部署網站的 VPN 連線 (或 Azure ExpressRoute)。
  - **個別的主要目標伺服器**：根據預設，容錯回復是由內部部署 VMware VM 上的設定伺服器安裝的主要目標伺服器所處理。 如果您需要容錯回復大量流量，您應該設定個別的內部部署主要目標伺服器。
  - **容錯回復原則**︰若要複寫回到內部部署網站，您需要容錯回復原則。 當您建立從內部部署至 Azure 的複寫原則時，會自動建立此原則。
  - **Vmware 基礎結構**：若要容錯回復，您需要 VMware 基礎結構。 您無法容錯回復到實體伺服器。
- 在元件就緒之後，容錯回復會以三個階段進行：
  - **階段 1**：重新保護 Azure vm，使其能從 azure 複寫回到內部部署 VMware vm。
  - **階段 2**：執行容錯移轉至內部部署網站。
  - **第3階段**：工作負載容錯回復之後，您會重新啟用複寫。

**從 Azure 容錯回復 VMware**

![容錯回復](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>後續步驟

若要設定實體伺服器到 Azure 的嚴重損壞修復，請參閱操作 [指南](physical-azure-disaster-recovery.md)。
