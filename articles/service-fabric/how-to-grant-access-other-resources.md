---
title: 授與應用程式存取權給其他 Azure 資源
description: 本文說明如何將受管理身分識別功能的 Service Fabric 應用程式存取權授與支援 Azure Active Directory 型驗證的其他 Azure 資源。
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 638176ca2665146a64fb7a6bd83253eba3434a7f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258958"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>將 Service Fabric 應用程式的受控識別存取權授與 Azure 資源 (預覽) 

在應用程式可以使用其受控識別來存取其他資源之前，必須先將許可權授與受保護 Azure 資源上的該身分識別。 授與許可權通常是 Azure 服務的「控制平面」上的管理動作，其擁有透過 Azure Resource Manager 路由傳送的受保護資源，這會強制執行任何適用的角色型存取檢查。

步驟的確切順序將取決於所存取的 Azure 資源類型，以及用來授與許可權的語言/用戶端。 本文的其餘部分假設已指派給應用程式的使用者指派身分識別，並包含數個可方便您使用的典型範例，但它不是本主題的完整參考。如需授與許可權的最新指示，請參閱個別 Azure 服務的檔。  

## <a name="granting-access-to-azure-storage"></a>授與 Azure 儲存體的存取權
在此情況下，您可以使用 Service Fabric 應用程式的受控識別 (使用者指派的) ，從 Azure 儲存體 blob 抓取資料。 使用下列步驟，將 Azure 入口網站中的必要許可權授與身分識別：

1. 流覽至儲存體帳戶
2. 按一下左側面板中的 [存取控制 (IAM)] 連結。
3.  (選擇性) 檢查現有的存取權：在 [尋找] 控制項中選取系統或使用者指派的受控識別;從後續結果清單中選取適當的身分識別
4. 按一下頁面頂端的 [+ 新增角色指派]，為應用程式的身分識別新增新的角色指派。
在 [角色] 下，從下拉式清單中，選取 [儲存體 Blob 資料讀取器]。
5. 在下一個下拉式清單中的 [指派存取權] 底下，選擇 [] `User assigned managed identity` 。
6. 接下來，請確保 [訂用帳戶] 下拉式清單中已列出適當的訂用帳戶，然後將 [資源群組] 設定為 [所有資源群組]。
7. 在 [選取] 底下，選擇對應至 Service Fabric 應用程式的 UAI，然後按一下 [儲存]。

支援系統指派的 Service Fabric 受控識別不包含 Azure 入口網站中的整合;如果您的應用程式使用系統指派的身分識別，您就必須先尋找應用程式身分識別的用戶端識別碼，然後重複上述步驟，但選取 `Azure AD user, group, or service principal` [尋找] 控制項中的選項。

## <a name="granting-access-to-azure-key-vault"></a>授與 Azure Key Vault 的存取權
類似于存取儲存體，您可以利用 Service Fabric 應用程式的受控識別來存取 Azure 金鑰保存庫。 在 Azure 入口網站中授與存取權的步驟類似于上述所列，而且不會在此重複。 請參閱下圖中的差異。

![Key Vault 存取原則](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

下列範例說明如何透過範本部署授與保存庫的存取權;將下列程式碼片段 (s) 如下所示，做為範本元素下的另一個專案 `resources` 。 此範例示範授與使用者指派和系統指派的身分識別類型的存取權，分別選擇適用的身分。

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
而針對系統指派的受控識別：
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

如需詳細資訊，請參閱保存[庫-更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>後續步驟
* [使用系統指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
