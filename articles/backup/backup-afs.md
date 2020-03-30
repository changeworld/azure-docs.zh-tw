---
title: 在 Azure 入口網站中備份 Azure 檔案共用
description: 瞭解如何使用 Azure 門戶在恢復服務保存庫中備份 Azure 檔共用
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938044"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>在恢復服務保存庫中備份 Azure 檔共用

本文介紹如何使用 Azure 門戶備份[Azure 檔共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

在本文中，您將學會如何：

* 建立復原服務保存庫。
* 發現檔共用並配置備份。
* 運行按需備份作業以創建還原點。

## <a name="prerequisites"></a>Prerequisites

* 標識或與承載檔共用的存儲帳戶位於同一區域中的[恢復服務保存庫](#create-a-recovery-services-vault)。
* 確保檔共用存在於[受支援的存儲帳戶類型](#limitations-for-azure-file-share-backup-during-preview)之一中。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>預覽期間的 Azure 檔案共用備份限制

Azure 檔案共用的備份處於預覽狀態。 支援一般用途 v1 和一般用途 v2 儲存體帳戶中的 Azure 檔案共用。 以下是備份 Azure 檔共用的限制：

* 支援在具有[區域冗余存儲](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)（ZRS） 複製的存儲帳戶中備份 Azure 檔共用，目前僅限於[這些區域](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)。
* Azure 備份當前支援配置 Azure 檔共用的每日計畫備份一次。
* 每天的排程備份次數上限為 1 次。
* 每天的隨選備份次數上限為 4 次。
* 使用存儲帳戶上的[資源鎖](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，以防止意外刪除恢復服務保存庫中的備份。
* 不要刪除 Azure 備份創建的快照。 刪除快照可能會導致復原點丟失或還原失敗。
* 不要刪除受 Azure 備份保護的檔共用。 當前解決方案刪除 Azure 備份在刪除檔共用後獲取的所有快照，因此所有還原點都將丟失。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改存儲複製

預設情況下，保存庫使用[異地冗余存儲 （GRS）。](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)

* 如果保存庫是您的主要備份機制，我們建議您使用 GRS。
* 您可以將[本地冗余存儲 （LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)用作低成本選項。

要修改存儲複製類型，

1. 在新保存庫中，在 **"設置"** 部分下選擇 **"屬性**"。

1. 在 **"屬性"** 頁上，在 **"備份配置**"下，選擇 **"更新**"。

1. 選擇存儲複製類型，然後選擇 **"保存**"。

    ![更新備份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 設置保存庫並包含備份項後，無法修改存儲複製類型。 如果要執行此操作，則需要重新創建保存庫。
>

## <a name="discover-file-shares-and-configure-backup"></a>發現檔共用並配置備份

1. 在[Azure 門戶](https://portal.azure.com/)中，打開要用於備份檔案共用的恢復服務保存庫。

1. 在**恢復服務保存庫**儀表板中，選擇 **+備份**。

   ![復原服務保存庫](./media/backup-afs/recovery-services-vault.png)

    a. 在 **"備份目標**"中，設置**工作負荷在何處運行？** **Azure**

    ![選擇 Azure 檔共用作為備份目標](./media/backup-afs/backup-goal.png)

    b.  在"**要備份什麼？"** 中，從下拉清單中選擇**Azure 檔共用**。

    c.  選擇 **"備份"** 以在保存庫中註冊 Azure 檔共用副檔名。

    ![選擇"備份"將 Azure 檔共用與保存庫關聯](./media/backup-afs/register-extension.png)

1. 選擇 **"備份**"後，將打開 **"備份"** 窗格，並提示您從發現的支援存儲帳戶清單中選擇存儲帳戶。 它們要麼與此保存庫關聯，要麼與保存庫位於同一區域中，但尚未與任何恢復服務保存庫關聯。

   ![選取儲存體帳戶](./media/backup-afs/select-storage-account.png)

1. 從已發現的存儲帳戶清單中，選擇一個帳戶，然後選擇 **"確定**"。 Azure 搜索存儲帳戶中可以備份的檔共用。 如果您最近添加了檔共用，但清單中看不到它們，請留出一些時間來顯示檔共用。

    ![發現檔共用](./media/backup-afs/discovering-file-shares.png)

1. 從 **"檔共用"** 清單中，選擇要備份的一個或多個檔共用。 選取 [確定]****。

1. 選擇檔共用後，"**備份"** 功能表將切換到 **"備份"策略**。 在此功能表中，選擇現有備份策略或創建新備份策略。 然後選擇 **"啟用備份**"。

    ![選取備份原則](./media/backup-afs/select-backup-policy.png)

設置備份策略後，將在計畫的時間拍攝檔共用的快照。 復原點也會保留在所選時間段內。

## <a name="create-an-on-demand-backup"></a>建立隨選備份

有時，您可能希望在備份策略中計畫的時間之外生成備份快照或復原點。 生成按需備份的常見原因是在配置備份策略後。 根據備份策略中的計畫，拍攝快照可能需要數小時或數天。 若要在備份原則參與之前保護您的資料，請起始隨選備份。 在計畫更改檔共用之前，通常需要創建按需備份。

### <a name="create-a-backup-job-on-demand"></a>按需創建備份作業

1. 打開用於備份檔案共用的恢復服務保存庫。 在 **"概述"** 窗格中，在 **"受保護專案**"部分下選擇 **"備份專案**"。

   ![選擇備份專案](./media/backup-afs/backup-items.png)

1. 選擇 **"備份專案**"後，"**概述"** 窗格旁邊將顯示一個列出所有**備份管理類型**的新窗格。

   ![備份管理類型清單](./media/backup-afs/backup-management-types.png)

1. 從 **"備份管理類型"** 清單中，選擇**Azure 存儲（Azure 檔）。** 您將看到所有檔共用的清單以及使用此保存庫備份的相應存儲帳戶。

   ![Azure 存儲（Azure 檔）備份項](./media/backup-afs/azure-files-backup-items.png)

1. 從 Azure 檔共用清單中，選擇所需的檔共用。 將顯示 **"備份專案**"詳細資訊。 在 **"備份專案"** 功能表上，立即選擇 **"備份**"。 由於此備份作業是按需的，因此沒有與復原點關聯的保留原則。

   ![立即選擇備份](./media/backup-afs/backup-now.png)

1. 將打開 **"立即備份"** 窗格。 指定您想要保留復原點的最後一天。 按需備份的最大保留期最長為 10 年。

   ![選擇保留日期](./media/backup-afs/retention-date.png)

1. 選擇 **"確定"** 以確認運行的按需備份作業。

1. 監視門戶通知，以跟蹤備份作業運行完成情況。 您可以在保存庫儀表板中監視作業進度。 選擇**正在進行的****備份作業** > 。

## <a name="next-steps"></a>後續步驟

了解如何：
* [還原 Azure 檔共用](restore-afs.md)
* [管理 Azure 檔共用備份](manage-afs-backup.md)
