---
title: 使用 Azure Resource Manager 建立 & 刪除使用者指派的受控識別
description: 如何使用 Azure Resource Manager 來建立和刪除使用者指派之受控識別的逐步指示。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 896c1909c8cc920e53fc2f43fbc4dbca41ad7db4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014229"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>使用 Azure Resource Manager 來建立、列出及刪除使用者指派的受控識別


適用于 Azure 資源的受控識別會在 Azure Active Directory 中提供具有受控識別的 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您會使用 Azure Resource Manager 建立使用者指派的受控身分識別。

無法使用 Azure Resource Manager 範本列出及刪除使用者指派的受控識別。  請參閱下列文章，以建立及列出使用者指派的受控識別：

- [列出使用者指派的受控識別](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [刪除使用者指派的受控識別](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱[系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="template-creation-and-editing"></a>範本建立和編輯

如同 Azure 入口網站和指令碼，Azure Resource Manager 範本可讓您部署由 Azure 資源群組所定義之新的或已修改的資源。 範本編輯和部署有幾個選項可用 (在本機和入口網站)，包括：

- 使用 [Azure Marketplace 的自訂範本](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或是以現有的一般或 [快速入門範本](https://azure.microsoft.com/documentation/templates/)作為基礎。
- 衍生自現有的資源群組，方法是從[原始部署](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)，或從[部署的目前狀態](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)匯出範本。
- 使用本機 [JSON 編輯器 (例如 VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳和部署。
- 使用 Visual Studio 的 [Azure 資源群組專案](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)來建立和部署範本。 

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別 

若要建立使用者指派的受控識別，您的帳戶需要[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

若要建立使用者指派的受控識別，請使用下列範本。 使用您自己的值取代 `<USER ASSIGNED IDENTITY NAME>` 值：

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>後續步驟

如需如何使用 Azure Resource Manager 範本將使用者指派之受控識別指派至 Azure VM 的相關資訊，請參閱[使用範本對 Azure VM 設定 Azure 資源的受控識別](qs-configure-template-windows-vm.md)。


 
