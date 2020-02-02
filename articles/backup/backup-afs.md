---
title: 在 Azure 入口網站中備份 Azure 檔案共用
description: 瞭解如何使用 Azure 入口網站來備份復原服務保存庫中的 Azure 檔案共用
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938044"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>備份復原服務保存庫中的 Azure 檔案共用

本文說明如何使用 Azure 入口網站來備份[Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

在本文中，您將學會如何：

* 建立復原服務保存庫。
* 探索檔案共用並設定備份。
* 執行隨選備份作業以建立還原點。

## <a name="prerequisites"></a>必要條件

* 在與裝載檔案共用的儲存體帳戶相同的區域中，識別或建立復原[服務保存庫](#create-a-recovery-services-vault)。
* 請確定檔案共用存在於其中一種支援的[儲存體帳戶類型](#limitations-for-azure-file-share-backup-during-preview)中。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>預覽期間的 Azure 檔案共用備份限制

Azure 檔案共用的備份處於預覽狀態。 支援一般用途 v1 和一般用途 v2 儲存體帳戶中的 Azure 檔案共用。 以下是備份 Azure 檔案共用的限制：

* 在具有[區域冗余儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)（ZRS）複寫的儲存體帳戶中，支援備份 Azure 檔案共用目前僅限於[這些區域](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)。
* Azure 備份目前支援設定 Azure 檔案共用的每日排程一次備份。
* 每天的排程備份次數上限為 1 次。
* 每天的隨選備份次數上限為 4 次。
* 在儲存體帳戶上使用[資源鎖定](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)，以防止在您的復原服務保存庫中意外刪除備份。
* 請勿刪除 Azure 備份所建立的快照集。 刪除快照集可能會導致復原點遺失或還原失敗。
* 請勿刪除受 Azure 備份保護的檔案共用。 目前的解決方案會刪除檔案共用刪除之後 Azure 備份所建立的所有快照集，因此所有還原點都會遺失。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改儲存體複寫

根據預設，保存庫會使用[異地多餘儲存體（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保存庫是您的主要備份機制，我們建議您使用 GRS。
* 您可以使用[本機多餘的儲存空間（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)做為低成本的選項。

若要修改儲存體複寫類型：

1. 在新的保存庫中，選取 [**設定**] 區段下的 [**屬性**]。

1. 在 [**屬性**] 頁面的 [**備份**設定] 底下，選取 [**更新**]。

1. 選取儲存體複寫類型，然後選取 [**儲存]。**

    ![更新備份設定](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 設定保存庫並包含備份專案之後，即無法修改儲存體複寫類型。 如果您想要這樣做，您必須重新建立保存庫。
>

## <a name="discover-file-shares-and-configure-backup"></a>探索檔案共用並設定備份

1. 在  [Azure 入口網站](https://portal.azure.com/)中，開啟您要用來備份檔案共用的復原服務保存庫。

1. 在復原**服務保存庫**儀表板中，選取 [ **+ 備份**]。

   ![復原服務保存庫](./media/backup-afs/recovery-services-vault.png)

    a. 在 [**備份目標**] 中，將**工作負載**的執行位置設定為**Azure**。

    ![選擇 Azure 檔案共用作為備份目標](./media/backup-afs/backup-goal.png)

    b.  在 [**您要備份什麼？** ] 中，從下拉式清單中選取 [ **Azure 檔案共用**]。

    c.  選取 [**備份**] 以在保存庫中註冊 Azure 檔案共用延伸模組。

    ![選取 [備份]，將 Azure 檔案共用與保存庫建立關聯](./media/backup-afs/register-extension.png)

1. 選取 [**備份**] 之後，[**備份**] 窗格隨即開啟，並提示您從已探索支援的儲存體帳戶清單中選取儲存體帳戶。 它們會與此保存庫相關聯，或存在於與保存庫相同的區域中，但尚未與任何復原服務保存庫相關聯。

   ![選取儲存體帳戶](./media/backup-afs/select-storage-account.png)

1. 從探索到的儲存體帳戶清單中選取帳戶，然後選取 **[確定]** 。 Azure 會在儲存體帳戶中搜尋可備份的檔案共用。 如果您最近新增了檔案共用，但在清單中看不到它們，請等待一些時間讓檔案共用出現。

    ![探索檔案共用](./media/backup-afs/discovering-file-shares.png)

1. 從 [檔案**共用**] 清單中，選取一或多個您想要備份的檔案共用。 選取 [確定]。

1. 在您選擇檔案共用之後，[**備份**] 功能表會切換到 [**備份原則**]。 從這個功能表中，選取現有的備份原則，或建立一個新的。 然後選取 [**啟用備份**]。

    ![選取備份原則](./media/backup-afs/select-backup-policy.png)

設定備份原則之後，就會在排定的時間執行檔案共用的快照集。 復原點也會在所選期間內保留。

## <a name="create-an-on-demand-backup"></a>建立隨選備份

有時候，您可能會想要在備份原則中排定的時間以外，產生備份快照集或復原點。 在您設定備份原則之後，通常會產生隨選備份的常見原因。 根據備份原則中的排程，在建立快照集之前，可能需要數小時或數天的時間。 若要在備份原則參與之前保護您的資料，請起始隨選備份。 在您對檔案共用進行規劃的變更之前，通常需要建立隨選備份。

### <a name="create-a-backup-job-on-demand"></a>視需要建立備份作業

1. 開啟您用來備份檔案共用的復原服務保存庫。 在 [**總覽**] 窗格中，選取 [**受保護的專案**] 區段下的 [**備份專案**]。

   ![選取備份專案](./media/backup-afs/backup-items.png)

1. 選取 [**備份專案**] 之後，會出現一個新的窗格，其中列出所有**備份管理類型**，並顯示在 [**總覽**] 窗格旁。

   ![備份管理類型的清單](./media/backup-afs/backup-management-types.png)

1. 從 [**備份管理類型**] 清單中，選取 [ **Azure 儲存體（Azure 檔案儲存體）** ]。 您會看到所有檔案共用的清單，以及使用此保存庫備份的對應儲存體帳戶。

   ![Azure 儲存體（Azure 檔案儲存體）備份專案](./media/backup-afs/azure-files-backup-items.png)

1. 從 Azure 檔案共用清單中，選取您想要的檔案共用。 **備份專案**詳細資料隨即出現。 在 [**備份專案**] 功能表上，選取 [**立即備份**]。 因為此備份作業為「隨選」，所以沒有與復原點相關聯的保留原則。

   ![選取 [立即備份]](./media/backup-afs/backup-now.png)

1. 此時會開啟 [**立即備份**] 窗格。 指定您想要保留復原點的最後一天。 針對隨選備份，您最多可以保留10年。

   ![選擇保留日期](./media/backup-afs/retention-date.png)

1. 選取 **[確定]** 以確認執行的隨選備份作業。

1. 監視入口網站通知以追蹤備份作業執行完成。 您可以在保存庫儀表板中監視作業進度。 選取 [**備份作業** > **進行中**]。

## <a name="next-steps"></a>後續步驟

了解如何：
* [還原 Azure 檔案共用](restore-afs.md)
* [管理 Azure 檔案共用備份](manage-afs-backup.md)
