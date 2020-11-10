---
title: 管理復原點
description: 瞭解 Azure 備份服務如何管理虛擬機器的復原點
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428588"
---
# <a name="manage-recovery-points"></a>管理復原點

本文說明虛擬機器的保留如何運作。 每當發生備份時，就會建立可執行還原作業的復原點。

虛擬機器的初始備份是完整備份，而後續備份是增量備份。

## <a name="recovery-points-and-retention"></a>復原點和保留期

### <a name="scheduled-initial-and-incremental-backup"></a>排程的初始和增量備份

讓我們使用一個簡單的虛擬機器 *V1* 範例，其中包含由四個區塊組成的資料磁片：區塊1、區塊2、區塊3和區塊4。 每個區塊的大小為 16 KB。

![具有四個區塊的虛擬機器](./media/manage-recovery-points/four-blocks.png)

**步驟 1-初始備份：** 初始備份是完整備份。 它可做為套用後續增量備份的基準。 假設在來源 VM 上有寫入區塊1和區塊2的資料。 相同的資料會在復原服務保存庫儲存體上複寫為 D1 和 D2。

![已複寫初始備份](./media/manage-recovery-points/initial-backup.png)

**步驟 2-增量備份1：** 請考慮將新資料新增至 VM 的區塊3。 相同的資料將會在下一次增量備份時進行複寫，而只有變更的區塊會儲存為 D3。  在每個步驟中，即使區塊的 1 KB 變更，也會在復原點上傳整個 16 KB 的區塊。

![第一個增量備份](./media/manage-recovery-points/first-incremental-backup.png)

**步驟 3-增量備份2：**  現在請考慮來源 VM 上的區塊3和區塊2上有資料變更。 這些變更將會在下一次增量備份中複寫為 D3 ' and D2 '。

![第二個增量備份](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>隨選備份

您可以選擇在設定保護之後隨時執行 VM 的隨選備份。

- 如果是在第一次排程初始備份之前觸發，則隨選備份會是完整備份。
- 如果初始備份已完成，並觸發隨選備份，則會是增量備份。
- 針對隨選備份所建立的復原點保留時間，是您在觸發備份時所指定的保留值。

### <a name="storage-cost"></a>儲存成本

針對初始備份建立的 **復原點** 包含具有資料的所有區塊。 後續的增量復原點只會包含已變更資料的區塊。 儲存體成本對應于所有復原點間的所有區塊總和。

讓我們使用上述範例來瞭解每個步驟之後的儲存體成本：

|步驟  |備份類型  |區塊已變更  |儲存體類型 |
|------|---------|---------|---------|
|1     |     初始備份    | 區塊1，區塊2        |    對應至復原點 1 (D1 + D2)      |
|2     |  增量備份1       |  區塊3       |   對應至復原點 1 (D1 + D2) + 復原點 2 (D3)       |
|3     |    增量備份2     |    區塊2，區塊3     |   對應于復原點 1 (D1 + D2) + 復原點 2 (D3) + 復原點 3 (D2 ' + D3 ' )       |

### <a name="recovery-point-expiration"></a>復原點到期日

每個復原點都有保留期間，如備份原則中所指定。 清除作業會定期進行，並且會清除所有已過期的復原點。

當復原點過期時，就會被刪除或合併。

### <a name="case-1-initial-recovery-point-expires"></a>案例1：初始復原點過期

初始復原點到期時，會與下一個增量復原點合併。 增量復原點中覆寫的所有資料區塊都會刪除，併合並其餘部分。 增量備份則會變成初始完整備份。 讓我們來看一個範例：

- 在初始備份期間建立的 *復原點 1* 具有 VM 的完整備份。
- 當 *復原點 1* 到期時， *復原點 2* 是下一次完整備份。
- 封鎖 D1 會與 *復原點 2* 合併，而 D2 會被刪除，因為在 *復原點 2* 中會覆寫區塊2中的資料。 這項變更會被視為區塊 D2」。
- 除非在下一次備份之前對其進行任何變更，否則區塊 D1 會保留在連續復原點中。

![第一個案例](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>案例2：在增量復原點到期前

- 如果恢復 *點 2* 在 *復原點 1* 之前過期，復原點 *2* 的資料會與下一個可用的復原點合併： *復原點 3* 。 因此封鎖 D3 會與 *復原點 3* 合併。
- *復原點 1* 仍是具有區塊 D1 和 D2 的完整備份。

![第二個案例](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>案例3：隨選復原點過期

在此範例中，排程 (每日備份) 原則排定為以 *n* 天保留期限執行。  如果在下次排定的備份之前的第四天觸發隨選備份，且其保留期限指定為10天，則它仍會是增量備份。 復原點 ( *隨選 RP1* ) 將在 *復原點 3* 和 *復原點 4* 之前建立。  在第14天結束時，隨選復原點 ( *隨選 RP1* ) 已過期，且將會與下一個可用的復原點合併。 仍存在於伺服器上的資料區塊會合並，而變更的資料區塊 (覆寫或刪除) 會從過期的復原點中刪除。

![第三個案例](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>原則變更在復原點上的影響

修改原則時，會同時套用至新的和現有的復原點。 如需詳細資訊，請參閱 [復原點上原則變更的影響](backup-architecture.md#impact-of-policy-change-on-recovery-points)。

### <a name="impact-of-stop-protection-on-recovery-points"></a>停止保護復原點的影響

有兩種方式可停止保護 VM：

- **停止保護並刪除備份資料。** 此選項將會停止所有未來的備份作業以保護您的 VM，並刪除所有復原點。 如果已啟用虛 [刪除](backup-azure-security-feature-cloud.md) ，刪除的資料將會保留14天。 針對虛刪除狀態的專案，不會產生費用。 您可以在14天的期間內刪除資料。 如果未啟用虛刪除，資料將會立即清除，您將無法還原 VM 或使用 [ **繼續備份** ] 選項。
- **停止保護並保留備份資料。** 此選項將會停止所有未來的備份作業以保護您的 VM。 不過，Azure 備份服務將永遠保留已備份的復原點。 您將需要付費以將復原點保留在保存庫中 (如需詳細資料) ，請參閱 [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/) 。 如有需要，您將能夠還原 VM。 如果您決定繼續 VM 保護，則可以使用 [ **繼續備份** ] 選項。 繼續備份之後，會將保留規則套用至到期時間點。 您也可以使用 [  **刪除備份資料** ] 選項來刪除已備份的資料。

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>刪除 VM 而不停止保護的影響

刪除沒有停止保護的 VM 會對復原點產生影響，而且是不想要的情況。 最好先停止備份，再刪除虛擬機器。 因為資源不存在，所以排定的備份將會失敗，並出現 [VMNotFoundV2 錯誤](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found)。 復原點會依據保留原則定期清除，但虛擬機器的最後一個複本將永遠保持不變，並據此向您收費。 視您的案例而定，您有下列兩個選項：

- **選項1：** 使用任何復原點來還原 VM。 如果您想要復原已刪除的 VM，請使用相同的名稱在相同的資源群組中進行還原。 如果您將還原的 VM 保護至相同的保存庫，則會自動連接現有的復原點。
- **選項2：** 移至 [復原服務保存庫]，並停止 [刪除資料] 的保護。

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>針對已虛刪除狀態的專案，已過期復原點的影響

如果已針對復原服務保存庫啟用虛 [刪除](backup-azure-security-feature-cloud.md) ，則過期的復原點會維持在虛刪除狀態，而且不會清除。 當復原點處於虛刪除狀態時，不會產生任何費用。

### <a name="impact-of-churn-on-backup-performance"></a>流失對備份效能的影響

假設 VM 的總儲存體是 8 TB，而變換率為5%。 然後，對應的增量備份儲存體會是 8 TB 的5%，也就是 0.4 TB。 較高的變換層級對應于後續增量備份的後端儲存體。 流失會影響備份效能。 變換愈高、備份程式會變慢，並提高後端儲存體的耗用量。

若要瞭解流失對備份效能的影響，請查看此案例：

|虛擬機器  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|資料磁碟數量    | 4 (A1、A2、A3、A4)         | 4 (B1、B2、B3、B4)         |  4 (C1、C2、C3、C4)        |
|每個磁片的大小   |      4 TB   | 4 TB        |  4 TB       |
|備份資料變換    |   A1-4 TB      | B1-1 TB;B2-1 TB <br> B3-1 TB;B4-1 TB  |   C1-2 TB;C4-2 TB      |

備份效能將會依照 order VM2>VM3>VM1。 這是因為流失資料分散在不同的磁片上。 由於磁片的備份會以平行方式執行，因此 VM2 會顯示最佳效能。

## <a name="next-steps"></a>後續步驟

- [Azure 備份的架構與元件](backup-architecture.md)
