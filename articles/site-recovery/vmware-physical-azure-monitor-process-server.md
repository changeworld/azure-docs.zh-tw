---
title: 監視 Azure Site Recovery 進程伺服器
description: 本文說明如何監視用於 VMware VM/實體伺服器嚴重損壞修復的 Azure Site Recovery 進程伺服器
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257065"
---
# <a name="monitor-the-process-server"></a>監視處理序伺服器

本文說明如何監視[Site Recovery](site-recovery-overview.md)進程伺服器。

- 當您設定內部部署 VMware Vm 和實體伺服器至 Azure 的嚴重損壞修復時，會使用進程伺服器。
- 根據預設，進程伺服器會在設定伺服器上執行。 當您部署設定伺服器時，預設會安裝它。
- （選擇性）若要調整和處理較大量的複寫機器和較大量的複寫流量，您可以部署額外的相應放大進程伺服器。

[深入瞭解](vmware-physical-azure-config-process-server-overview.md)進程伺服器的角色和部署。

## <a name="monitoring-overview"></a>監視概觀

由於進程伺服器有這麼多角色，特別是在複寫的資料快取、壓縮和傳送至 Azure，因此請務必持續監視處理伺服器的健全狀況。

有幾種情況通常會影響進程伺服器效能。 影響效能的問題會對 VM 健全狀況有串聯效果，最後將進程伺服器和其複寫的機器同時推送到重大狀態。 情況包括：

- 大量的 Vm 會使用進程伺服器，接近或超過建議的限制。
- 使用進程伺服器的 Vm 具有高變換率。
- Vm 與進程伺服器之間的網路輸送量不足，無法將複寫資料上傳到進程伺服器。
- 進程伺服器與 Azure 之間的網路輸送量，不足以將複寫資料從進程伺服器上傳至 Azure。

這些問題都可能會影響 Vm 的復原點目標（RPO）。 

**因此?** 由於產生 VM 的復原點需要 VM 上的所有磁片都有共同的點。 如果某個磁片的變換率較高、複寫速度很慢，或進程伺服器不是最佳的，則會影響建立復原點的效率。

## <a name="monitor-proactively"></a>主動監視

若要避免進程伺服器發生問題，請務必：

- 瞭解使用[容量和大小指引](site-recovery-plan-capacity-vmware.md#capacity-considerations)的進程伺服器的特定需求，並確定進程伺服器已根據建議部署和執行。
- 監視警示，並針對發生的問題進行疑難排解，讓進程伺服器有效率地運作。


## <a name="process-server-alerts"></a>進程伺服器警示

進程伺服器會產生一些健康情況警示，在下表中摘要說明。

**警示類型** | **詳細資料**
--- | ---
![Healthy][green] | 進程伺服器已連線且狀況良好。
![警告][yellow] | 過去15分鐘內的 CPU 使用率 > 80%
![警告][yellow] | 過去15分鐘內記憶體使用量 > 80%
![警告][yellow] | 過去15分鐘內快取資料夾可用空間 < 30%
![警告][yellow] | Site Recovery 會每隔五分鐘監視擱置/傳出資料，並估計在30分鐘內無法將進程伺服器快取中的資料上傳至 Azure。
![警告][yellow] | 進程伺服器服務未在過去15分鐘內執行
![重大][red] | 過去15分鐘內的 CPU 使用率 > 95%
![重大][red] | 過去15分鐘內記憶體使用量 > 95%
![重大][red] | 過去15分鐘內快取資料夾可用空間 < 25%
![重大][red] | Site Recovery 會每隔五分鐘監視擱置/傳出資料，並估計進程伺服器快取中的資料在45分鐘內無法上傳至 Azure。
![重大][red] | 進程伺服器沒有15分鐘的任何心跳。

![資料表索引鍵](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 進程伺服器的整體健全狀況狀態是根據產生的最差警示。



## <a name="monitor-process-server-health"></a>監視進程伺服器健全狀況

您可以監視進程伺服器的健全狀況狀態，如下所示： 

1. 若要監視複寫電腦及其進程伺服器的複寫健康情況和狀態，請在 [保存庫] > [複寫的**專案**] 中，按一下您要監視的電腦。
2. 在 [複寫**健康**情況] 中，您可以監視 VM 健全狀況狀態。 按一下 [狀態] 向下切入以取得錯誤詳細資料。

    ![VM 儀表板中的進程伺服器健全狀況](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在 [**進程伺服器健全狀況**] 中，您可以監視進程伺服器的狀態。 向下切入以取得詳細資料。

    ![VM 儀表板中的進程伺服器詳細資料](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 您也可以使用 VM 頁面上的圖形化標記法來監視健康情況。
    - 如果有相關的警告，則相應放大進程伺服器將會以橙色反白顯示，如果有任何重大問題，則會以紅色醒目提示。 
    - 如果進程伺服器正在設定伺服器上的預設部署中執行，則設定伺服器會據以反白顯示。
    - 若要向下切入，請按一下設定伺服器或進程伺服器。 請注意任何問題，以及任何補救建議。

您也可以在**Site Recovery 基礎結構**底下的保存庫中監視進程伺服器。 在 [**管理您的 Site Recovery 基礎結構**] 中，按一下 [設定**伺服器**]。 選取與進程伺服器相關聯的設定伺服器，然後向下切入進程伺服器詳細資料。


## <a name="next-steps"></a>後續步驟

- 如果您有任何進程伺服器問題，請遵循我們的[疑難排解指引](vmware-physical-azure-troubleshoot-process-server.md)
- 如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
