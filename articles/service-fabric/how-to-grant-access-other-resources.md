---
title: 授予應用程式對其他 Azure 資源的訪問
description: 本文介紹如何將託管標識啟用的服務結構應用程式存取權限授予支援基於 Azure 活動目錄身份驗證的其他 Azure 資源。
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614788"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>授予服務結構應用程式對 Azure 資源的託管標識存取權限（預覽）

在應用程式可以使用其託管標識訪問其他資源之前，必須授予所訪問的受保護 Azure 資源上的該標識的許可權。 授予許可權通常是 Azure 服務的"控制平面"上的管理操作，它擁有通過 Azure 資源管理器路由的受保護資源，這將強制實施任何適用的基於角色的訪問檢查。

然後，步驟的確切順序將取決於要訪問的 Azure 資源的類型以及用於授予許可權的語言/用戶端。 本文的其餘部分假定分配給應用程式的使用者分配標識，並包含幾個典型示例以方便您，但它絕不是本主題的詳盡參考;有關授予許可權的最新說明，請參閱相應 Azure 服務的文檔。  

## <a name="granting-access-to-azure-storage"></a>授予對 Azure 存儲的存取權限
可以使用 Service Fabric 應用程式的託管標識（在這種情況下由使用者分配）從 Azure 存儲 Blob 檢索資料。 通過以下步驟授予標識 Azure 門戶中所需的許可權：

1. 導航到存儲帳戶
2. 按一下左側面板中的 [存取控制 (IAM)] 連結。
3. （可選）檢查現有訪問：在"查找"控制項中選擇系統或使用者分配的託管標識;從後續結果清單中選擇適當的標識
4. 按一下 " 添加頁面頂部的角色指派"，為應用程式標識添加新的角色指派。
在 [角色] 下，從下拉式清單中，選取 [儲存體 Blob 資料讀取器]。
5. 在下一個下拉下下下，在"分配`User assigned managed identity`對的訪問"下，選擇 。
6. 接下來，請確保 [訂用帳戶] 下拉式清單中已列出適當的訂用帳戶，然後將 [資源群組] 設定為 [所有資源群組]。
7. 在"選擇"下，選擇與服務交換矩陣應用程式對應的 UAI，然後按一下"保存"。

對系統分配的 Service Fabric 託管標識的支援不包括在 Azure 門戶中的集成;因此，對服務結構託管標識的支援不包括在 Azure 門戶中集成。如果應用程式使用系統分配的標識，則必須首先查找應用程式標識的用戶端 ID，然後重複上述步驟，但在"查找"控制項中選擇`Azure AD user, group, or service principal`該選項。

## <a name="granting-access-to-azure-key-vault"></a>授予對 Azure 金鑰保存庫的存取權限
同樣，在訪問存儲時，可以利用 Service Fabric 應用程式的託管標識來訪問 Azure 金鑰保存庫。 在 Azure 門戶中授予存取權限的步驟與上面列出的步驟類似，此處不會重複。 有關差異，請參閱下圖。

![Key Vault 存取原則](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

下面的示例演示了通過範本部署授予對保存庫的存取權限;將下面的程式碼片段添加為範本`resources`元素下的另一個條目。 該示例演示了使用者分配和系統分配的標識類型的存取權限授予 - 選擇適用的標識類型。

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
對於系統分配的託管標識：
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

有關詳細資訊，請參閱[保存庫 - 更新訪問策略](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>後續步驟
* [使用系統分配的託管標識部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者分配的託管標識部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)