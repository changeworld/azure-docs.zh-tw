---
title: 將範本設定為使用虛擬機器擴展集上的受控識別-Azure AD
description: 使用 Azure Resource Manager 範本在虛擬機器擴展集上設定 Azure 資源受控識別的逐步指示。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77425556"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>使用範本在 Azure 虛擬機器擴展上設定 Azure 資源的受控識別

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。

在本文中，您將了解如何使用 Azure Resource Manager 部署範本，在 Azure 虛擬機器擴展集上執行以下 Azure 資源受控識別作業：
- 在 Azure 虛擬機器擴展集上啟用和停用系統指派的受控識別
- 在 Azure 虛擬機器擴展集上新增和移除使用者指派的受控識別

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱[系統指派和使用者指派受控識別之間的差異](overview.md#how-does-the-managed-identities-for-azure-resources-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本文中的管理作業，您的帳戶需要下列 Azure 角色型存取控制指派：

    > [!NOTE]
    > 不需要其他 Azure AD 目錄角色指派。

    - [虛擬機器參與者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可建立虛擬機器擴展集，並從虛擬機器擴展集啟用和移除系統和/或使用者指派的受控識別。
    - [受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可建立使用者指派的受控識別。
    - [受控識別操作員](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可為虛擬機器擴展集指派和移除使用者指派的受控識別。

## <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

如同 Azure 入口網站和指令碼，[Azure Resource Manager](../../azure-resource-manager/management/overview.md) 範本可讓您部署由 Azure 資源群組所定義的最新或已修改資源。 範本編輯和部署有幾個選項可用 (在本機和入口網站)，包括：

   - 使用 Azure Marketplace 中的[自訂範本](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或以現有的通用或[快速入門範本](https://azure.microsoft.com/documentation/templates/)作為基礎。
   - 衍生自現有的資源群組，方法是從[原始部署](../../azure-resource-manager/templates/export-template-portal.md)，或從[部署的目前狀態](../../azure-resource-manager/templates/export-template-portal.md)匯出範本。
   - 使用本機 [JSON 編輯器 (例如 VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳和部署。
   - 使用 Visual Studio 的 [Azure 資源群組專案](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)來建立和部署範本。  

不論您選擇的選項，在初始部署和重新部署期間，範本的語法都相同。 在新的或現有 VM 上啟用 Azure 資源受控識別，是以相同的方式來完成。 此外，根據預設，Azure Resource Manager 會對部署採取[累加式更新](../../azure-resource-manager/templates/deployment-modes.md)。

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

在本節中，您將使用 Azure Resource Manager 範本以啟用和停用系統指派的受控識別。

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>建立虛擬機器擴展集或現有的虛擬機器擴展集時，啟用系統指派的受控識別

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。
2. 若要啟用系統指派的受控識別，請將範本載入到編輯器、在 resources 區段中找出感興趣的 `Microsoft.Compute/virtualMachinesScaleSets` 資源，然後在與 `"type": "Microsoft.Compute/virtualMachinesScaleSets"` 屬性相同的層級上新增 `identity`。 使用下列語法：

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> 您可以選擇性地在範本的`extensionProfile`元素中指定 Azure 資源的受控識別虛擬機器擴展集擴充功能。 此步驟是選擇性的，因為您也可以使用 Azure Instance Metadata Service (IMDS) 識別端點以擷取權杖。  如需詳細資訊，請參閱[從 VM 擴充功能遷移至 AZURE IMDS 以進行驗證](howto-migrate-vm-extension.md)。


4. 當您完成時，應該將下列區段新增至範本的資源區段，而且應該像下面這樣：

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集停用系統指派的受控識別

如果您的虛擬機器擴展集不再需要系統指派的受控識別：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

2. 在[編輯器](#azure-resource-manager-templates)中載入範本，然後在 `resources` 區段找出想要的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 若您的虛擬機器只有系統指派的受控識別，您可以將識別類型變更為 `None` 予以停用。

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 版**

   如果您的 apiVersion 為 `2018-06-01` 且您的 VM 同時具有系統與使用者指派的受控識別，請從識別類型中移除 `SystemAssigned`，並保留 `UserAssigned` 以及 userAssignedIdentities 字典值。

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 版**

   若您的 apiVersion 為 `identityIds` 且您的虛擬機器擴展集同時具有系統與使用者指派的受控識別，請從識別類型中移除 `2017-12-01`，並保留 `SystemAssigned` 以及使用者指派受控識別的 `UserAssigned` 陣列。



   下列範例示範如何從沒有使用者指派受控識別的虛擬機器擴展集中移除系統指派的受控識別：

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>使用者指派的受控識別

在本節中，您會使用 Azure Resource Manager 範本，將使用者指派的受控識別指派給虛擬機器擴展集。

> [!Note]
> 若要使用 Azure Resource Manager 範本建立使用者指派的受控識別，請參閱[建立使用者指派的受控識別](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)。

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>將使用者指派的受控識別指派給虛擬機器擴展集

1. 在 `resources` 元素下新增下列項目，以將使用者指派的受控識別指派給您的虛擬機器擴展集。  請務必將 `<USERASSIGNEDIDENTITY>` 取代為您建立的使用者指派受控識別名稱。

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 版**

   如果您的 apiVersion 為 `2018-06-01`，則使用者指派的受控識別會以 `userAssignedIdentities` 字典格式儲存，而 `<USERASSIGNEDIDENTITYNAME>` 值必須儲存在您範本 `variables` 區段內所定義的變數中。

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01 版**

   如果您的 `apiVersion` 為 `2017-12-01` 或更早版本，則使用者指派的受控識別會儲存於 `identityIds` 陣列中，而 `<USERASSIGNEDIDENTITYNAME>` 值必須儲存在您範本 variables 區段內所定義的變數中。

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> 您可以選擇性地在範本的`extensionProfile`元素中指定 Azure 資源的受控識別虛擬機器擴展集擴充功能。 此步驟是選擇性的，因為您也可以使用 Azure Instance Metadata Service (IMDS) 識別端點以擷取權杖。  如需詳細資訊，請參閱[從 VM 擴充功能遷移至 AZURE IMDS 以進行驗證](howto-migrate-vm-extension.md)。

3. 完成之後，您的範本看起來應該如下所示：

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 版**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集移除使用者指派的受控識別

如果您的虛擬機器擴展集不再需要使用者指派的受控識別：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶。

2. 在[編輯器](#azure-resource-manager-templates)中載入範本，然後在 `resources` 區段找出想要的 `Microsoft.Compute/virtualMachineScaleSets` 資源。 若您的虛擬機器擴展集只有使用者指派的受控識別，您可以將識別類型變更為 `None` 予以停用。

   下列範例示範如何從沒有系統指派受控識別的虛擬機器中移除所有使用者指派的受控識別：

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 版**

   若要從虛擬機器擴展集移除單一使用者指派的受控識別，請從 `userAssignedIdentities` 字典中移除它。

   如果您有系統指派的受控識別，則將它保存在 `identity` 值下方的 `type` 值中。

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01 版**

   若要從虛擬機器擴展集移除單一使用者指派的受控識別，請從 `identityIds` 陣列中移除它。

   如果您有系統指派的受控識別，則將它保存在 `identity` 值下方的 `type` 值中。

## <a name="next-steps"></a>後續步驟

- [適用于 Azure 資源的受控識別總覽](overview.md)。
