---
title: 關於 Azure 檔案共享備份
description: 瞭解如何在復原服務保管庫中備份 Azure 檔案共用
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396274"
---
# <a name="about-azure-file-share-backup"></a>關於 Azure 檔案共享備份

Azure 檔案共享備份是一種基於雲的本機備份解決方案,可保護雲中的數據,並消除本地備份解決方案中涉及的其他維護開銷。 Azure 備份服務與 Azure 檔同步順利整合,並允許您集中檔共享數據以及備份。 此簡單、可靠和安全的解決方案使您能夠通過幾個簡單步驟配置企業檔共用的保護,並保證在發生任何災難時可以恢復數據。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 檔案共享備份的主要優勢

* 零基礎結構:無需部署即可配置檔共享的保護。
* 內建管理功能:您可以計劃備份並指定所需的保留期,而無需增加數據修剪的開銷。
* 即時還原:Azure 檔案共享備份使用檔共用快照,因此只能選擇要立即還原的檔。
* 警報和報告:您可以為備份和還原失敗配置警報,並使用 Azure 備份提供的報告解決方案來獲取有關檔共享備份的見解。

## <a name="architecture"></a>架構

![Azure 檔案共享備份體系結構](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>備份過程的工作原理

1. 設定 Azure 檔案共享備份的第一步是創建恢復服務保管庫。 保管庫為您提供了跨不同工作負載配置的備份的整合檢視。

2. 創建保管庫后,Azure 備份服務將發現可以向保管庫註冊的存儲帳戶。 您可以選擇託管要保護的檔案共享的儲存帳戶。

3. 選擇存儲帳戶后,Azure 備份服務將列出存儲帳戶中存在的檔案共用集,並將它們的名稱存儲在管理層目錄中。

4. 然後根據您的要求配置備份策略(計劃和保留),然後選擇要備份的文件共用。 Azure 備份服務在控制平面中註冊計劃以執行計劃備份。

5. 根據指定的策略,Azure 備份計劃程式在計劃的時間觸發備份。 作為該作業的一部分,使用檔共用 API 創建檔案共用快照。 只有快照 URL 儲存在中繼資料儲存中。

    >[!NOTE]
    >檔案共享數據不會傳輸到備份服務,因為備份服務創建和管理屬於存儲帳戶的快照。

6. 可以從源檔共用上可用的快照還原 Azure 檔共享內容(單個檔或完整共用)。 觸發操作後,將從元數據存儲中檢索快照 URL,並將數據從源快照列出並傳輸到您選擇的目標檔共用。

7. 備份和還原作業監視數據推送到 Azure 備份監視服務。 這允許您在單個儀錶板中監視檔共用的雲備份。 此外,您還可以在備份運行狀況受到影響時配置警報或電子郵件通知。 電子郵件通過 Azure 電子郵件服務發送。

## <a name="backup-costs"></a>備份成本

Azure 檔案共享備份是基於快照的解決方案,根據[此處](https://azure.microsoft.com/pricing/details/storage/files/)提到的定價詳細資訊,快照產生的存儲費用與 Azure 檔使用方式一起計費。

但是,利用備份解決方案的受保護實例費用根據[「為 Azure 檔案備份」](https://azure.microsoft.com/pricing/details/backup/)部分中描述的定價模型。 目前,實際價格已僅針對美國中西部進行了更新。 對於其他地區,確切的價格將很快更新與一些區域變化,但使用相同的定價模型。

>[!NOTE]
>在預覽期間,沒有"受保護的實例費用",您將僅根據[此處](https://azure.microsoft.com/pricing/details/storage/files/)提到的定價收取快照費用。

## <a name="next-steps"></a>後續步驟

* 瞭解如何備份[Azure 檔案分享](backup-afs.md)
* 尋找[有關備份 Azure 檔案問題的答案](backup-azure-files-faq.md)
