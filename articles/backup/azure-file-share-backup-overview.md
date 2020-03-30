---
title: 關於 Azure 檔共用備份
description: 瞭解如何在恢復服務保存庫中備份 Azure 檔共用
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78386716"
---
# <a name="about-azure-file-share-backup"></a>關於 Azure 檔共用備份

Azure 檔共用備份是一種基於雲的本機備份解決方案，可保護雲中的資料，並消除本地備份解決方案中涉及的其他維護開銷。 Azure 備份服務與 Azure 檔同步順利集成，並允許您集中檔共用資料以及備份。 此簡單、可靠和安全的解決方案使您能夠通過幾個簡單步驟配置企業檔共用的保護，並保證在發生任何災難時可以恢復資料。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 檔共用備份的主要優勢

* 零基礎結構：無需部署即可設定檔共用的保護。
* 內置管理功能：您可以計畫備份並指定所需的保留期，而無需增加資料修剪的開銷。
* 即時還原：Azure 檔共用備份使用檔共用快照，因此只能選擇要立即還原的檔。
* 警報和報告：您可以為備份和還原失敗配置警報，並使用 Azure 備份提供的報告解決方案來獲取有關檔共用備份的見解。

## <a name="architecture"></a>架構

![Azure 檔共用備份體系結構](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>備份過程的工作原理

1. 配置 Azure 檔共用備份的第一步是創建恢復服務保存庫。 保存庫為您提供了跨不同工作負載配置的備份的整合視圖。

2. 創建保存庫後，Azure 備份服務將發現可以向保存庫註冊的存儲帳戶。 您可以選擇託管要保護的檔共用的存儲帳戶。

3. 選擇存儲帳戶後，Azure 備份服務將列出存儲帳戶中存在的檔共用集，並將它們的名稱存儲在管理層目錄中。

4. 然後根據您的要求配置備份策略（計畫和保留），然後選擇要備份的檔共用。 Azure 備份服務在控制平面中註冊計畫以執行計畫備份。

5. 根據指定的策略，Azure 備份計畫程式在計畫的時間觸發備份。 作為該作業的一部分，使用檔共用 API 創建檔共用快照。 只有快照 URL 存儲在中繼資料存儲中。

    >[!NOTE]
    >檔共用資料不會傳輸到備份服務，因為備份服務創建和管理屬於存儲帳戶的快照。

6. 可以從原始檔案共用上可用的快照還原 Azure 檔共用內容（單個檔或完整共用）。 觸發操作後，將從中繼資料存儲中檢索快照 URL，並將資料從源快照列出並傳輸到您選擇的目的檔案共用。

7. 備份和還原作業監視資料推送到 Azure 備份監視服務。 這允許您在單個儀表板中監視檔共用的雲備份。 此外，您還可以在備份運行狀況受到影響時配置警報或電子郵件通知。 電子郵件通過 Azure 電子郵件服務發送。

## <a name="next-steps"></a>後續步驟

* 瞭解如何備份[Azure 檔共用](backup-afs.md)
* 查找[有關備份 Azure 檔問題的答案](backup-azure-files-faq.md)
