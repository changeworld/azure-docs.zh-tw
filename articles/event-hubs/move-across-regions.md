---
title: 將 Azure 事件中心命名空間移動到其他區域 |微軟文件
description: 本文介紹如何將 Azure 事件中心命名空間從當前區域移動到其他區域。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606804"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>將 Azure 事件中心命名空間移至其他區域
在各種方案中,您希望將現有事件中心命名空間從一個區域移動到另一個區域。 例如,您可能希望建立具有相同配置用於測試的命名空間。 作為[災難恢復規劃](event-hubs-geo-dr.md#setup-and-failover-flow)的一部分,您可能還需要在另一個區域中創建輔助命名空間。

> [!NOTE]
> 本文介紹如何為現有事件中心命名空間匯出 Azure 資源管理器範本,然後使用該範本在另一個區域中創建具有相同配置設置的命名空間。 但是,此過程不會移動尚未處理的事件。 在刪除事件之前,您需要從原始命名空間處理事件。

## <a name="prerequisites"></a>Prerequisites

- 確保目標區域支援您的帳戶使用的服務和功能。
- 對於預覽功能,請確保訂閱已為目標區域列入白名單。
- 如果為命名空間中的事件中心啟用**了捕獲功能**,則在行動事件中心命名空間之前,移動[Azure 儲存或 Azure 資料儲存庫第 2 代](../storage/common/storage-account-move.md)或 Azure[資料湖儲存第 1 代](../data-lake-store/data-lake-store-migration-cross-region.md)帳戶。 還可以按照與本文中描述的步驟類似的步驟將同時包含存儲和事件中心命名空間的資源組移動到其他區域。 
- 如果事件中心命名空間位於**事件中心群集**中,則在經歷本文中的步驟之前,在**目標區域**[中創建專用群集](event-hubs-dedicated-cluster-create-portal.md)。 

## <a name="prepare"></a>準備
要開始,匯出資源管理器範本。 此範本包含描述事件中心命名空間的設置。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選擇**所有資源**,然後選擇事件中心命名空間。

3. 選擇>**設定** > **匯出樣本**。

4. 在 **「匯出範本」** 頁中選擇 **「下載**」。

    ![下載資源管理員範本](./media/move-across-regions/download-template.png)

5. 找到從門戶下載的 .zip 檔案,然後解壓縮該檔案到您選擇的資料夾。

   此 zip 檔包含 .json 檔,其中包含用於部署範本的範本和文稿。


## <a name="move"></a>移動

部署範本以在目標區域中創建事件中心命名空間。 


1. 在 Azure 門戶中,選擇 **「創建資源**」。。

2. 在 [搜尋 Marketplace]**** 中，輸入**範本部署**，然後按 **ENTER**。

3. 選擇**樣本部署**。

4. 選取 [建立]  。

5. 選取 [在編輯器中組建您自己的範本]****。

6. 選擇 **「載入檔**」,然後按照說明載入您在最後一節中下載的**template.json**檔。

7. 選擇 **「儲存**」以保存範本。 

8. 在 **「自訂部署」** 頁上,按照以下步驟操作: 

    1. 選擇 Azure**訂閱**。 

    2. 選取現有的**資源群組**，或建立一個群組。 如果源命名空間位於事件中心群集中,請選擇目標區域中包含群集的資源組。 

    3. 選擇目標**位置**或區域。 如果選擇了現有資源組,則此設置為唯讀。 

    4. 在 **「設定」** 部分中,執行以下步驟:
    
        1. 輸入新的**命名空間名稱**。 

            ![部署資源管理員樣本](./media/move-across-regions/deploy-template.png)

        2. 如果源命名空間位於**事件中心群集**中,請輸入**資源組**和**事件中心群集**的名稱作為**外部 ID**的一部分。 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 如果命名空間中的事件中心使用儲存帳戶捕獲事件,請指定`StorageAccounts_<original storage account name>_external`欄位的資源組名稱和儲存帳戶。 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. 選取 [我同意上方所述的條款及條件]**** 核取方塊。 
    
    6. 現在,**選擇「選擇購買**」以啟動部署過程。 

## <a name="discard-or-clean-up"></a>丟棄或清除
部署後,如果要重新開始,可以刪除**目標事件中心命名空間**,並重複本文的[「準備](#prepare)和[移動](#move)」部分中描述的步驟。

要提交變更並完成事件中心命名空間的移動,請刪除**源事件中心命名空間**。 在刪除命名空間之前,請確保處理了命名空間中的所有事件。 

要使用 Azure 門戶移除事件中心命名空間(來源或目標):

1. 在 Azure 門戶頂端的搜尋視窗中,鍵入**事件中心**,並從搜尋結果中選擇**事件中心**。 在清單中可以看到事件中心命名空間。

2. 選擇要刪除的目標命名空間,然後從工具列中選擇 **「刪除**」 。 

    ![刪除命名空間 - 按鈕](./media/move-across-regions/delete-namespace-button.png)

3. 在 **「刪除資源**+ 」 頁上,驗證所選資源,並通過鍵入**yes**確認刪除,然後選擇 **「刪除**」。 

## <a name="next-steps"></a>後續步驟

在本教學中,您將 Azure 事件中心命名空間從一個區域移動到另一個區域並清理了來源資源。  要瞭解有關在 Azure 中在區域和災難恢復之間移動資源的詳細資訊,請參閱:


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
