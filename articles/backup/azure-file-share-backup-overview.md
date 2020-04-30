---
title: 關於 Azure 檔案共用備份
description: 瞭解如何在復原服務保存庫中備份 Azure 檔案共用
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c79100724b882c0682c86070ee74a8726d6b049f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105738"
---
# <a name="about-azure-file-share-backup"></a>關於 Azure 檔案共用備份

Azure 檔案共用備份是以雲端為基礎的原生備份解決方案，可保護您在雲端中的資料，並排除與內部部署備份解決方案相關的額外維護負荷。 Azure 備份服務會順暢地與 Azure 檔案同步整合，並可讓您將檔案共用資料和備份組中在一起。 這個簡單、可靠且安全的解決方案可讓您透過幾個簡單的步驟來設定企業檔案共用的保護，並保證您可以在發生任何嚴重損壞狀況時復原資料。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 檔案共用備份的主要優點

* 零基礎結構：不需要部署，即可設定檔案共用的保護。
* 自訂保留：您可以根據需求，設定每日/每週/每月/每年保留的備份。
* 內建管理功能：您可以排程備份並指定所需的保留期限，而不需要額外的資料剪除負擔。
* 立即還原： Azure 檔案共用備份會使用檔案共用快照集，因此您可以只選取您想要立即還原的檔案。
* 警示和報告：您可以設定備份和還原失敗的警示，並使用 Azure 備份提供的報告解決方案，取得跨檔案共用之備份的深入解析。

## <a name="architecture"></a>架構

![Azure 檔案共用備份架構](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>備份程式的運作方式

1. 設定 Azure 檔案共用備份的第一個步驟是建立復原服務保存庫。 保存庫可讓您匯總不同工作負載之間所設定的備份。

2. 建立保存庫之後，Azure 備份服務會探索可以向保存庫註冊的儲存體帳戶。 您可以選取裝載您要保護之檔案共用的儲存體帳戶。

3. 選取儲存體帳戶之後，Azure 備份服務會列出儲存體帳戶中存在的一組檔案共用，並將其名稱儲存在管理層目錄中。

4. 然後，根據您的需求設定備份原則（排程和保留），然後選取要備份的檔案共用。 Azure 備份服務會在控制平面中註冊排程，以執行排定的備份。

5. 根據指定的原則，Azure 備份排程器會在排定的時間觸發備份。 在該作業中，會使用檔案共用 API 建立檔案共用快照集。 只有快照集 URL 會儲存在中繼資料存放區中。

    >[!NOTE]
    >檔案共用資料不會傳輸到備份服務，因為備份服務會建立並管理屬於您儲存體帳戶的快照集，而且備份不會傳輸至保存庫。

6. 您可以從來源檔案共用上可用的快照集還原 Azure 檔案共用內容（個別檔案或完整共用）。 觸發作業之後，就會從中繼資料存放區抓取快照集 URL，並列出資料，並從來源快照集傳送到您選擇的目標檔案共用。

7. 備份和還原作業監視資料會推送至 Azure 備份監視服務。 這可讓您在單一儀表板中監視檔案共用的雲端備份。 此外，您也可以在備份健全狀況受到影響時，設定警示或電子郵件通知。 電子郵件是透過 Azure 電子郵件服務傳送。

## <a name="backup-costs"></a>備份成本

目前您只需要支付快照集的費用，因為 Azure 檔案共用備份是以快照集為基礎的解決方案。 快照集所產生的儲存體費用會根據[此處](https://azure.microsoft.com/pricing/details/storage/files/)所述的定價詳細資料，以 Azure 檔案儲存體使用量計費。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[備份 Azure 檔案共用](backup-afs.md)
* 尋找[有關備份 Azure 檔案儲存體問題](backup-azure-files-faq.md)的解答
