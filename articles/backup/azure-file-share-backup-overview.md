---
title: 關於 Azure 檔案共用備份
description: 瞭解如何在復原服務保存庫中備份 Azure 檔案共用
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: e955df7662bd18ed1d1d4ec1f0aa6c9474c5386f
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378078"
---
# <a name="about-azure-file-share-backup"></a>關於 Azure 檔案共用備份

Azure 檔案共用備份是以雲端為基礎的原生備份解決方案，可保護您在雲端中的資料，並消除與內部部署備份解決方案相關的額外維護負荷。 Azure 備份服務可順暢地與 Azure 檔案同步整合，並可讓您將檔案共用資料和備份組中在一起。 這個簡單、可靠且安全的解決方案，可讓您以幾個簡單的步驟來設定企業檔案共用的保護，並確保您可以在任何嚴重損壞的情況下復原資料。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 檔案共用備份的主要優點

* **零基礎結構**：設定檔案共用的保護時不需要部署。
* **自訂保留**：您可以根據您的需求，設定每日/每週/每月/每年保留的備份。
* **內建的管理功能**：您可以排程備份並指定所需的保留期限，而不會造成資料剪除的額外負擔。
* **立即還原**： Azure 檔案共用備份會使用檔案共用快照集，因此您可以只選取您想要立即還原的檔案。
* **警示和報告**：您可以設定備份和還原失敗的警示，並使用 Azure 備份提供的報告解決方案來取得跨檔案共用的備份見解。
* **防止意外刪除檔案共用**： Azure 備份會在保留期限為14天的儲存體帳戶層級上啟用虛 [刪除功能](../storage/files/storage-files-prevent-file-share-deletion.md) 。 即使惡意執行者刪除檔案共用，檔案共用的內容和復原點 (快照集) 仍會保留給可設定的保留期限，讓來源內容和快照的成功和完全復原，而且不會遺失資料。

## <a name="architecture"></a>架構

![Azure 檔案共用備份架構](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>備份程式的運作方式

1. 設定 Azure 檔案共用備份的第一個步驟是建立復原服務保存庫。 保存庫可讓您匯總跨不同工作負載設定的備份。

2. 建立保存庫之後，Azure 備份服務會探索可在保存庫中註冊的儲存體帳戶。 您可以選取裝載您要保護之檔案共用的儲存體帳戶。

3. 選取儲存體帳戶之後，Azure 備份服務會列出存在於儲存體帳戶中的檔案共用集，並將其名稱儲存在管理層目錄中。

4. 然後，您可以根據您的需求設定備份原則 (排程和保留) ，然後選取要備份的檔案共用。 Azure 備份服務會在控制平面中註冊排程，以執行排定的備份。

5. 根據指定的原則，Azure 備份排程器會在排定的時間觸發備份。 在該工作中，會使用檔案共用 API 來建立檔案共用快照集。 只有快照集 URL 會儲存在中繼資料存放區中。

    >[!NOTE]
    >檔案共用資料不會傳送至備份服務，因為備份服務會建立並管理屬於您儲存體帳戶的快照集，且備份不會傳輸至保存庫。

6. 您可以從來源檔案共用上可用的快照集，將 Azure 檔案共用內容還原 (個別檔案或完整共用) 。 觸發作業之後，就會從中繼資料存放區中取出快照集 URL，並將資料從來源快照集列出並傳送至您選擇的目標檔案共用。

7. 如果您使用 Azure 檔案同步，則備份服務會向 Azure 檔案同步服務指出正在還原之檔案的路徑，然後在這些檔案上觸發背景變更偵測處理常式。 任何已變更的檔案都會同步到伺服器端點。 此程式會與對 Azure 檔案共用的原始還原進行平行處理。

8. 備份和還原作業監視資料會推送至 Azure 備份的監視服務。 這可讓您在單一儀表板中監視檔案共用的雲端備份。 此外，您也可以在備份健康情況受到影響時，設定警示或電子郵件通知。 電子郵件會透過 Azure 電子郵件服務傳送。

## <a name="backup-costs"></a>備份成本

Azure 檔案共用備份解決方案有兩個相關成本：

1. **快照儲存體成本**：快照集產生的儲存體費用會根據[此處](https://azure.microsoft.com/pricing/details/storage/files/)所述的定價詳細資料，隨 Azure 檔案儲存體使用量計費

2. **受保護的實例費用**：自2020年9月1日起，客戶將根據 [此處](https://azure.microsoft.com/pricing/details/backup/)所述的定價詳細資料向您收取受保護的實例費用。受保護的實例費用取決於儲存體帳戶中受保護檔案共用的大小總計。

若要取得備份 Azure 檔案共用的詳細估計值，您可以下載詳細的 [Azure 備份定價估算器](https://aka.ms/AzureBackupCostEstimates)。  

## <a name="next-steps"></a>後續步驟

* 了解如何[備份 Azure 檔案共用](backup-afs.md)
* 尋找[有關備份 Azure 檔案儲存體問題](backup-azure-files-faq.md)的解答
