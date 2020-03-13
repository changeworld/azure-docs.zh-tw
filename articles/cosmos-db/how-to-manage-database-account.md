---
title: 了解如何在 Azure Cosmos DB 中管理資料庫帳戶
description: 瞭解如何使用 Azure 入口網站、PowerShell、CLI 和 Azure Resource Manager 範本來管理 Azure Cosmos DB 資源
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247380"
---
# <a name="manage-an-azure-cosmos-account"></a>管理 Azure Cosmos 帳戶

本文說明如何使用 Azure 入口網站、Azure PowerShell、Azure CLI 和 Azure Resource Manager 範本管理 Azure Cosmos 帳戶的各項工作。

## <a name="create-an-account"></a>建立帳戶

### <a id="create-database-account-via-portal"></a>Azure 入口網站

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

請參閱[使用 Azure CLI 建立 Azure Cosmos DB 帳戶](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

請參閱[使用 Powershell 建立 Azure Cosmos DB 帳戶](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager 範本

此 Azure Resource Manager 範本會建立一個 Azure Cosmos 帳戶，適用于設定了兩個區域的 SQL API，以及用來選取一致性層級、自動容錯移轉和多宿主的選項。 若要部署此範本，請在讀我檔案頁面的[建立 Azure Cosmos 帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)中，按一下 [部署至 Azure]

## <a name="addremove-regions-from-your-database-account"></a>在資料庫帳戶中新增/移除區域

### <a id="add-remove-regions-via-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 移至 Azure Cosmos 帳戶，然後開啟 [全域複寫資料] 功能表。

1. 若要新增區域，請選取地圖上具有 **+** 標籤、且與您所需的區域相對應的六邊形。 或者，若要新增區域，請選取 [+ 新增區域] 選項，然後從下拉式功能表中選擇區域，藉以新增區域。

1. 若要移除區域，請選取具有核取記號的藍色六邊形，以清除地圖中的一或多個區域。 或者，選取右側區域旁邊的「垃圾桶」(🗑) 圖示。

1. 若要儲存變更，請選取 [確定]。

   ![新增或移除區域功能表](./media/how-to-manage-database-account/add-region.png)

在單一區域寫入模式中，您無法移除寫入區域。 您必須先容錯移轉至不同的區域，才能刪除目前的寫入區域。

在多重區域寫入模式中，只要您有至少一個區域，即可新增或移除任何區域。

### <a id="add-remove-regions-via-cli"></a>Azure CLI

請參閱[使用 Azure CLI 新增或移除區域](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

請參閱[使用 Powershell 新增或移除區域](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>設定多重寫入區域

### <a id="configure-multiple-write-regions-portal"></a>Azure 入口網站

開啟 [全域複寫資料] 索引標籤，並選取 [啟用] 以啟用多重區域寫入。 啟用多重區域寫入後，您目前在帳戶上擁有的所有讀取區域都將成為讀取和寫入區域。

![Azure Cosmos 帳戶設定多重主機的螢幕擷取畫面](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

請參閱[使用 Azure CLI 啟用多重寫入區域](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

請參閱[使用 Powershell 啟用多重寫入區域](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager 範本

帳戶可以從單一主機移轉至多重主機，只要部署用來建立帳戶的 Resource Manager 範本並設定 `enableMultipleWriteLocations: true` 即可。 下列 Azure Resource Manager 範本是一個最低限度範本，將會為已啟用兩個區域和多重寫入位置的 SQL API 部署 Azure Cosmos 帳戶。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>啟用 Azure Cosmos 帳戶的自動容錯移轉

[自動容錯移轉] 選項可讓 Azure Cosmos DB 在一個區域變得無法使用時，容錯移轉至具有最高容錯移轉優先順序的區域，而不需要使用者動作。 自動容錯移轉啟用時，可以修改區域的優先順序。 帳戶必須具有兩個或更多區域，才能啟用自動容錯移轉。

### <a id="enable-automatic-failover-via-portal"></a>Azure 入口網站

1. 從 Azure Cosmos 帳戶，開啟 [全域複寫資料] 窗格。

2. 在窗格頂端，選取 [自動容錯移轉]。

   ![全域複寫資料功能表](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在 [自動容錯移轉] 窗格中，確定 [啟用自動容錯移轉] 設定為 [開啟]。 

4. 選取 [儲存]。

   ![自動容錯移轉入口網站功能表](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

請參閱[使用 Azure CLI 啟用自動容錯移轉](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

請參閱[使用 Powershell 啟用自動容錯移轉](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>設定 Azure Cosmos 帳戶的容錯移轉屬性

Cosmos 帳戶設定自動容錯移轉後，可以變更區域的容錯移轉優先順序。

> [!IMPORTANT]
> 帳戶設定了自動容錯移轉時，您無法修改寫入區域 (容錯移轉優先順序為零)。 若要變更寫入區域，您必須停用自動容錯移轉，並執行手動容錯移轉。

### <a id="set-failover-priorities-via-portal"></a>Azure 入口網站

1. 從 Azure Cosmos 帳戶，開啟 [全域複寫資料] 窗格。

2. 在窗格頂端，選取 [自動容錯移轉]。

   ![全域複寫資料功能表](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在 [自動容錯移轉] 窗格中，確定 [啟用自動容錯移轉] 設定為 [開啟]。

4. 若要修改容錯移轉優先順序，請透過當您暫留其上時出現在資料列左側的三個點拖曳讀取區域。

5. 選取 [儲存]。

   ![自動容錯移轉入口網站功能表](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

請參閱[使用 Azure CLI 設定容錯移轉優先順序](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

請參閱[使用 Powershell 設定容錯移轉優先順序](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>在 Azure Cosmos 帳戶上執行手動容錯移轉

> [!IMPORTANT]
> Azure Cosmos 帳戶必須已設定手動容錯移轉，這項作業才會成功。

執行手動容錯移轉的程序，包括將帳戶的寫入區域 (容錯移轉優先順序 = 0) 變更為帳戶設定的另一個區域。

> [!NOTE]
> 多重主機帳戶無法以手動方式容錯移轉。 如果應用程式使用 Azure Cosmos SDK，此 SDK 將會偵測變得無法使用的區域，然後自動重新導向至下一個最接近的區域 (如果 SDK 中使用多路連接 API)。

### <a id="enable-manual-failover-via-portal"></a>Azure 入口網站

1. 移至 Azure Cosmos 帳戶，然後開啟 [全域複寫資料] 功能表。

2. 在功能表的頂端，選取 [手動容錯移轉]。

   ![全域複寫資料功能表](./media/how-to-manage-database-account/replicate-data-globally.png)

3. 在 [手動容錯移轉] 功能表上，選取新的寫入區域。 選取核取方塊，表示您了解此選項會變更您的寫入區域。

4. 若要觸發容錯移轉，請選取 [確定]。

   ![手動容錯移轉入口網站功能表](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

請參閱[使用 Azure CLI 觸發手動容錯移轉](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

請參閱[使用 Powershell 觸發手動容錯移轉](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>後續步驟

如需關於如何管理 Azure Cosmos 帳戶以及資料庫和容器的詳細資訊和範例，閱讀下列文章：

* [使用 Azure PowerShell 管理 Azure Cosmos DB](manage-with-powershell.md)
* [使用 Azure CLI 管理 Azure Cosmos DB](manage-with-cli.md)
