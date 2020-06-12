---
title: 監視 Azure Site Recovery 處理序伺服器
description: 本文說明如何監視用於 VMware VM/實體伺服器災害復原的 Azure Site Recovery 處理序伺服器
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 296254db83a065623b692d7947c130dfd71cd413
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835048"
---
# <a name="monitor-the-process-server"></a>監視處理序伺服器

本文說明如何監視 [Site Recovery](site-recovery-overview.md) 處理序伺服器。

- 當您將內部部署 VMware VM 和實體伺服器的災害復原目的地設定為 Azure 時，便會使用處理序伺服器。
- 根據預設，處理序伺服器會在設定伺服器上執行。 當您部署設定伺服器時，便會依預設安裝處理序伺服器。
- (選擇性) 若要擴縮和處理較大量的複寫機器以及較大量的複寫流量，您可以部署額外的擴增處理序伺服器。

[深入了解](vmware-physical-azure-config-process-server-overview.md)處理序伺服器的角色和部署。

## <a name="monitoring-overview"></a>監視概觀

由於處理序伺服器有如此多角色，特別是在複寫的資料快取、壓縮和傳輸至 Azure，因此請務必持續監視處理伺服器的健康情況。

有幾種常見情況會影響處理序伺服器的效能。 影響效能的問題會對 VM 的健康情況產生串聯效果，最終會將處理序伺服器和其複寫機器推入危急狀態。 這些情況包括：

- 大量 VM 使用處理序伺服器，接近或超過建議的限制。
- 使用處理序伺服器的 VM 具有高變換率。
- VM 與處理序伺服器之間的網路輸送量不足，無法將複寫資料上傳到處理序伺服器。
- 處理序伺服器與 Azure 之間的網路輸送量不足，無法將複寫資料從處理序伺服器上傳至 Azure。

這些問題都會影響 VM 的復原點目標 (RPO)。 

**原因為何？** 由於產生 VM 的復原點需要 VM 上的所有磁碟有共有點。 如果某個磁碟的變換率較高、複寫速度很慢，或處理序伺服器不是處於最佳狀態，就會影響復原點的建立效率。

## <a name="monitor-proactively"></a>主動監視

若要避免處理序伺服器發生問題，請務必：

- 使用[容量和大小指引](site-recovery-plan-capacity-vmware.md#capacity-considerations)了解處理伺服器的特定需求，並確定處理序伺服器已根據建議完成部署並執行。
- 監視警示，並針對發生的問題進行疑難排解，讓處理序伺服器保持有效率地運作。


## <a name="process-server-alerts"></a>處理序伺服器警示

處理序伺服器會產生一些健康情況警示，其彙總於下表。

**警示類型** | **詳細資料**
--- | ---
![Healthy][green] | 處理序伺服器已連線且狀況良好。
![警告][yellow] | 過去 15 分鐘內的 CPU 使用率 > 80%
![警告][yellow] | 過去 15 分鐘內記憶體使用量 > 80%
![警告][yellow] | 過去 15 分鐘內快取資料夾可用空間 < 30%
![警告][yellow] | Site Recovery 每隔五分鐘監視一次擱置/傳出的資料，且估計處理序伺服器快取中的資料無法在 30 分鐘內上傳至 Azure。
![警告][yellow] | 處理序伺服器服務在過去 15 分鐘內未執行
![重大][red] | 過去 15 分鐘內的 CPU 使用率 > 95%
![重大][red] | 過去 15 分鐘內記憶體使用量 > 95%
![重大][red] | 過去 15 分鐘內快取資料夾可用空間 < 25%
![重大][red] | Site Recovery 每隔五分鐘監視一次擱置/傳出的資料，且估計處理序伺服器快取中的資料無法在 45 分鐘內上傳至 Azure。
![重大][red] | 處理序伺服器已有 15 分鐘未傳來任何活動訊號。

![資料表索引鍵](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 處理序伺服器的整體健全狀態取決於產生的最糟糕警示。



## <a name="monitor-process-server-health"></a>監視處理伺服器健康狀態

您可以監視處理序伺服器的健全狀態，如下所示： 

1. 若要監視複寫電腦及其處理序伺服器的複寫健康情況和狀態，請在保存庫 > [複寫的項目] 中，按一下您要監視的電腦。
2. 在 [複寫健康情況] 中，您可以監視 VM 的健全狀態。 按一下狀態即可向下切入以取得錯誤詳細資料。

    ![VM 儀表板中的處理序伺服器健康情況](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在 [處理序伺服器健康情況] 中，您可以監視處理序伺服器的狀態。 向下切入以取得詳細資料。

    ![VM 儀表板中的處理序伺服器詳細資料](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 您也可以使用 VM 頁面上的圖形表示法來監視健康情況。
    - 如果有相關聯的警告，則擴增處理序伺服器將會以橙色醒目提示，如果有任何危急問題，則會以紅色醒目提示。 
    - 如果處理序伺服器正在設定伺服器上的預設部署中執行，則設定伺服器會據以醒目提示。
    - 若要向下切入，請按一下設定伺服器或處理序伺服器。 請注意任何問題，以及任何補救建議。

您也可以在 [Site Recovery 基礎結構] 底下，監視保存庫中的處理序伺服器。 在 [管理 Site Recovery 基礎結構] 中，按一下 [設定伺服器]。 選取與處理序伺服器相關聯的設定伺服器，然後向下切入以取得處理序伺服器的詳細資料。


## <a name="next-steps"></a>後續步驟

- 如果您有任何處理序伺服器問題，請遵循我們的[疑難排解指引](vmware-physical-azure-troubleshoot-process-server.md)
- 如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-site-recovery.html)。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
