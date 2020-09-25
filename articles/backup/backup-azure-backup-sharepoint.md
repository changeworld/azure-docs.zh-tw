---
title: 使用 DPM 將 SharePoint 伺服器陣列備份至 Azure
description: 這篇文章概述 SharePoint 伺服器陣列至 Azure 的 DPM/Azure 備份伺服器保護
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254426"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>使用 DPM 將 SharePoint 伺服器陣列備份至 Azure

您可以使用 System Center Data Protection Manager (DPM)，將 SharePoint 伺服器陣列備份到 Microsoft Azure，其方法與備份其他資料來源極為類似。 Azure 備份提供靈活的備份排程來建立每日、每週、每月或每年備份點，並可讓您針對各種備份點執行保留原則選項。 DPM 可讓您儲存本機磁碟複本來快速達成復原時間目標 (RTO)，也可以將複本儲存到 Azure 來進行經濟實惠的長期保留。

使用 DPM 將 SharePoint 備份至 Azure 的程式，與將 SharePoint 備份到本機 DPM 的流程非常類似。 本文說明 Azure 的特定考量。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint 支援的版本與相關保護案例

如需支援的 SharePoint 版本清單以及備份所需的 DPM 版本清單，請參閱 [What can DPM back up?](/system-center/dpm/dpm-protection-matrix#applications-backup) (DPM 可以備份的內容)。

## <a name="before-you-start"></a>開始之前

您需要先確定幾件事，再將 SharePoint 伺服器陣列備份至 Azure。

### <a name="prerequisites"></a>Prerequisites

繼續之前，請確定 [使用 Microsoft Azure 備份來保護工作負載的所有必要條件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 已滿足。 一些滿足必要條件的工作包括︰建立備份保存庫、下載保存庫認證、安裝 Azure 備份代理程式，以及向保存庫註冊 DPM/Azure 備份伺服器。

如需其他必要條件和限制，請參閱 [使用 DPM 的備份 SharePoint](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations) 文章。

## <a name="configure-backup"></a>設定備份

若要備份 SharePoint 伺服器陣列，您要使用 ConfigureSharePoint.exe 設定 SharePoint 保護，然後在 DPM 中建立保護群組。 如需相關指示，請參閱 DPM 檔中的 [設定備份](/system-center/dpm/back-up-sharepoint#configure-backup) 。

## <a name="monitoring"></a>監視

若要監視備份作業，請遵循[監視 DPM 備份](/system-center/dpm/back-up-sharepoint#monitoring)中的指示。

## <a name="restore-sharepoint-data"></a>還原 SharePoint 資料

若要瞭解如何使用 DPM 從磁片還原 SharePoint 專案，請參閱 [還原 sharepoint 資料](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)。

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>使用 DPM 從 Azure 中還原 SharePoint 資料庫

1. 若要復原 SharePoint 內容資料庫，請瀏覽各種復原點 (如上所示)，並選取要還原的復原點。

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. 按兩下 SharePoint 復原點以顯示可用的 SharePoint 目錄資訊。

   > [!NOTE]
   > 由於 SharePoint 伺服器陣列在 Azure 中受長期保留保護，因此 DPM 伺服器上沒有可用的目錄資訊 (元資料)。 如此一來，每當需要復原時間點 SharePoint 內容資料庫，您就需要重新編目 SharePoint 伺服器陣列。
   >
   >
3. 選取 [ **重新編目**]。

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    [雲端重新編目]  狀態視窗隨即會開啟。

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    完成編目後，狀態會變更為 [成功] 。 選取 [關閉]。

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. 選取 **[DPM 復原** ] 索引標籤中顯示的 SharePoint 物件，以取得內容資料庫結構。 以滑鼠右鍵按一下專案，然後選取 [ **復原**]。

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 此時，依照本文前述的復原步驟，從磁碟復原 SharePoint 內容資料庫。

## <a name="switching-the-front-end-web-server"></a>切換前端網頁伺服器

如果您有一部以上的前端網頁伺服器，而且想要切換 DPM 用來保護伺服器陣列的伺服器，請遵循 [切換前端網頁伺服器](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server)的指示。

## <a name="next-steps"></a>後續步驟

* [Azure 備份伺服器和 DPM-常見問題](backup-azure-dpm-azure-server-faq.md)
* [針對 System Center Data Protection Manager 疑難排解](backup-azure-scdpm-troubleshooting.md)
