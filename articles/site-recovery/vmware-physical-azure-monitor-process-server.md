---
title: 監視 Azure 網站恢復過程伺服器
description: 本文介紹如何監視用於 VMware VM/物理伺服器災害復原的 Azure 網站恢復進程伺服器
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257065"
---
# <a name="monitor-the-process-server"></a>監視處理序伺服器

本文介紹如何監視[網站恢復](site-recovery-overview.md)過程伺服器。

- 當您將本地 VMware VM 和物理伺服器的災害復原設置為 Azure 時，將使用進程伺服器。
- 預設情況下，進程伺服器在佈建服務器上運行。 預設情況下，在部署佈建服務器時安裝它。
- 或者，為了擴展和處理更多複製的電腦和更多數量的複製流量，您可以部署其他橫向擴展進程伺服器。

[詳細瞭解](vmware-physical-azure-config-process-server-overview.md)進程伺服器的角色和部署。

## <a name="monitoring-overview"></a>監視概觀

由於進程伺服器具有如此多的角色，尤其是在複製的資料緩存、壓縮和傳輸到 Azure 中，因此持續監視進程伺服器運行狀況非常重要。

有許多情況通常影響進程伺服器性能。 影響性能的問題將對 VM 運行狀況產生級聯影響，最終將進程伺服器及其複製的電腦推入關鍵狀態。 情況包括：

- 大量 VM 使用進程伺服器，接近或超過建議的限制。
- 使用進程伺服器的 VM 具有高改動率。
- VM 和進程伺服器之間的網路輸送量不足以將複製資料上載到進程伺服器。
- 進程伺服器和 Azure 之間的網路輸送量不足以將複製資料從進程伺服器上載到 Azure。

所有這些問題都可能影響 VM 的復原點目標 （RPO）。 

**為什麼？** 由於為 VM 生成復原點需要 VM 上的所有磁片具有公共點。 如果一個磁片具有高改動率、複製速度慢或進程伺服器不是最佳磁片，則會影響創建復原點的效率。

## <a name="monitor-proactively"></a>主動監視

為了避免進程伺服器出現問題，請務必：

- 瞭解使用[容量和大小調整指南](site-recovery-plan-capacity-vmware.md#capacity-considerations)的進程伺服器的特定要求，並確保根據建議部署和運行流程伺服器。
- 監視警報並在問題發生時進行故障排除，以保持進程伺服器高效運行。


## <a name="process-server-alerts"></a>處理伺服器警報

進程伺服器生成許多運行狀況警報，概述在下表中。

**警報類型** | **詳細資料**
--- | ---
![Healthy][green] | 進程伺服器已連接且正常。
![警告][yellow] | CPU 利用率> 80% 的過去 15 分鐘
![警告][yellow] | 過去 15 分鐘> 80% 的記憶體使用量
![警告][yellow] | 快取檔案夾可用空間<過去 15 分鐘 30%
![警告][yellow] | 網站恢復每五分鐘監視一次掛起/傳出資料，並估計進程伺服器緩存中的資料無法在 30 分鐘內上載到 Azure。
![警告][yellow] | 進程伺服器服務在過去 15 分鐘內未運行
![重大][red] | 過去 15 分鐘，CPU 利用率> 95%
![重大][red] | 記憶體使用量> 95% 的最後 15 分鐘
![重大][red] | 快取檔案夾可用空間< 25% 的最後 15 分鐘
![重大][red] | 網站恢復每五分鐘監視一次掛起/傳出資料，並估計進程伺服器緩存中的資料無法在 45 分鐘內上載到 Azure。
![重大][red] | 15 分鐘內沒有來自進程伺服器的活動訊號。

![表鍵](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 進程伺服器的總體運行狀況基於生成的最差警報。



## <a name="monitor-process-server-health"></a>監視進程伺服器運行狀況

您可以按照如下方式監視進程伺服器的運行狀況： 

1. 要監視複製電腦及其進程伺服器的複製運行狀況和狀態，請在保存庫>**複製項**中，按一下要監視的電腦。
2. 在**複製運行狀況**中，可以監視 VM 運行狀況狀態。 按一下狀態以向下切入以瞭解錯誤詳細資訊。

    ![在 VM 儀表板中處理伺服器運行狀況](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. 在**進程伺服器運行狀況**中，可以監視進程伺服器的狀態。 向下切入以瞭解詳細資訊。

    ![在 VM 儀表板中處理伺服器詳細資訊](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 還可以使用 VM 頁上的圖形表示來監視運行狀況。
    - 如果存在與其關聯的警告，橫向擴展進程伺服器將以橙色突出顯示;如果存在任何關鍵問題，則為紅色。 
    - 如果進程伺服器在佈建服務器上的預設部署中運行，則佈建服務器將相應地突出顯示。
    - 要向下切入，請按一下佈建服務器或進程伺服器。 注意任何問題和任何補救建議。

您還可以在**網站恢復基礎結構**下監視保存庫中的進程伺服器。 在**管理您的網站恢復基礎結構**中，按一下**佈建服務器**。 選擇與進程伺服器關聯的佈建服務器，然後向下切入流程伺服器詳細資訊。


## <a name="next-steps"></a>後續步驟

- 如果您有任何進程伺服器問題，請按照我們的[故障排除指南操作](vmware-physical-azure-troubleshoot-process-server.md)
- 如果您需要更多協助，請將您的問題貼到 [Azure Site Recovery 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) \(英文\) 中。 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
