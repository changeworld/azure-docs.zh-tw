---
title: 包含檔案
description: 包含檔案
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: c0f717e26534151585a51badf4bb1fe1ba58eb00
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970515"
---
## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一種管理實體，可儲存一段時間內建立的復原點，並提供介面來執行備份相關作業。 其中包括採取隨選備份、執行還原，以及建立備份原則。

若要建立復原服務保存庫，請依照下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。

1. 在左側功能表上，選取 [所有服務]  。

    ![選取所有服務](./media/backup-create-rs-vault/click-all-services.png)

1. 在 [所有服務]  對話方塊中，輸入 *Recovery Services*。 資源清單會根據您的輸入進行篩選。 在資源清單中，選取 [復原服務保存庫]  。

    ![輸入並選擇復原服務保存庫](./media/backup-create-rs-vault/all-services.png)

    隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。

1. 在 [復原服務保存庫]  儀表板上，選取 [新增]  。

    ![新增復原服務保存庫](./media/backup-create-rs-vault/add-button-create-vault.png)

    [復原服務保存庫]  對話方塊隨即開啟。 提供 [名稱]  、[訂用帳戶]  、[資源群組]  和 [位置]  的值。

    ![設定復原服務保存庫](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**：輸入可識別保存庫的易記名稱。 該名稱必須是 Azure 訂用帳戶中唯一的名稱。 指定的名稱至少要有 2 個字元，但不能超過 50 個字元。 名稱開頭必須是字母，且只能包含字母、數字和連字號。
   - 訂用帳戶  ：選擇要使用的訂用帳戶。 如果您是唯一一個訂用帳戶的成員，就會看到該名稱。 如果您不確定要使用哪個訂用帳戶，請使用預設 (建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
   - **資源群組**：使用現有的資源群組或建立新群組。 若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]  ，然後從下拉式清單中選取資源。 若要建立新的資源群組，請選取 [新建]  ，然後輸入名稱。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../articles/azure-resource-manager/management/overview.md)。
   - **位置**：選取保存庫的地理區域。 若要建立保存庫來保護任何資料來源，保存庫「必須」  與資料來源位於相同的區域中。

      > [!IMPORTANT]
      > 如果不確定您資料來源的位置，請關閉對話方塊。 在入口網站中，移至您的資源清單。 如果您在多個區域中有資料來源，請為每個區域建立復原服務保存庫。 請在第一個位置建立保存庫，然後再建立另一個位置的保存庫。 不需要指定用來儲存備份資料的儲存體帳戶。 復原服務保存庫和 Azure 備份會自動處理該事宜。
      >
      >

1. 當您準備好建立復原服務保存庫時，選取 [建立]  。

    ![建立復原服務保存庫](./media/backup-create-rs-vault/click-create-button.png)

    建立復原服務保存庫可能需要一些時間。 請監視入口網站右上角 [通知]  區域中的狀態通知。 保存庫建立之後，就可以在 [復原服務保存庫] 的清單中看到。 如果您沒有看到保存庫，請選取 [重新整理]  。

     ![重新整理備份保存庫的清單](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> 我們強烈建議您在保存庫中設定備份之前，先檢查**儲存體複寫類型**和**安全性設定**的預設值。 如需詳細資訊，請參閱[設定儲存體冗余](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)一節。
