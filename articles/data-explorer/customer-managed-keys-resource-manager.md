---
title: 使用 Azure 資源管理器範本在 Azure 資料資源管理器中配置客戶管理金鑰
description: 本文介紹如何使用 Azure 資源管理器範本在 Azure 資料資源管理器中配置客戶管理的資料金鑰加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297877"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>使用 Azure 資源管理器範本配置客戶託管金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure 資源管理器範本](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰配置加密

在本節中，使用 Azure 資源管理器範本配置客戶管理的金鑰。 預設情況下，Azure 資料資源管理器加密使用 Microsoft 管理的金鑰。 在此步驟中，將 Azure 資料資源管理器群集配置為使用客戶管理的金鑰，並指定要與群集關聯的鍵。

可以使用 Azure 門戶或使用 PowerShell 部署 Azure 資源管理器範本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>更新金鑰版本

創建金鑰的新版本時，需要更新群集才能使用新版本。 首先，打電話`Get-AzKeyVaultKey`獲取最新版本的金鑰。 然後更新群集的金鑰保存庫屬性以使用金鑰的新版本，如[使用客戶管理的金鑰配置加密](#configure-encryption-with-customer-managed-keys)所示。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料資源管理器群集](security.md)
* [為 Azure 資料資源管理器群集配置託管標識](managed-identities.md)
* 通過在靜態啟用加密來保護[Azure 資料資源管理器 - Azure 門戶中的群集](manage-cluster-security.md)。
* [使用 C 配置客戶託管金鑰#](customer-managed-keys-csharp.md)

