---
title: '設定本機角色型存取控制 (Azure API for FHIR 的本機 RBAC) '
description: 本文說明如何設定 Azure API for FHIR，以針對資料平面使用外部 Azure AD 租使用者
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524193"
---
# <a name="configure-local-rbac-for-fhir"></a>設定 FHIR 的本機 RBAC 

本文說明如何將 Azure API for FHIR 設定為使用外部次要 Azure Active Directory 租使用者來管理資料平面存取。 只有當您不能使用與訂用帳戶相關聯的 Azure Active Directory 租使用者時，才使用此模式。

> [!NOTE]
> 如果您的 FHIR 服務資料平面設定為使用與訂用帳戶相關聯的主要 Azure Active Directory 租使用者，請 [使用 AZURE RBAC 來指派資料平面角色](configure-azure-rbac.md)。

## <a name="add-service-principal"></a>新增服務主體

本機 RBAC 可讓您搭配 FHIR 伺服器使用外部 Azure Active Directory 租使用者。 若要允許本機 RBAC 系統檢查此租使用者中的群組成員資格，Azure API for FHIR 在租使用者中必須有服務主體。 此服務主體會自動建立于系結至已部署 Azure API for FHIR 之訂用帳戶的租使用者中，但如果您的租使用者未系結任何訂用帳戶，則租使用者系統管理員將需要使用下列其中一個命令來建立此服務主體：

使用 `Az` PowerShell 模組：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

或者，您可以使用 `AzureAd` PowerShell 模組：

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

或者，您可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>設定本機 RBAC

您可以將 Azure API for FHIR 設定為使用 **驗證** 分頁中的外部或次要 Azure Active Directory 租使用者：

![本機 RBAC 指派](media/rbac/local-rbac-guids.png).

在 [授權單位] 方塊中，輸入有效的 Azure Active Directory 租使用者。 租使用者經過驗證之後，[ **允許的物件識別碼** ] 方塊應該會啟動，而且您可以輸入識別物件識別碼的清單。 這些識別碼可以是的身分識別物件識別碼：

* Azure Active Directory 的使用者。
* Azure Active Directory 服務主體。
* Azure Active directory 安全性群組。

如需詳細資訊，您可以閱讀有關如何 [尋找識別物件識別碼](find-identity-object-ids.md) 的文章。

輸入所需的物件識別碼之後，請按一下 [ **儲存** ] 並等候儲存變更，然後再嘗試使用指派的使用者、服務主體或群組來存取資料平面。

## <a name="caching-behavior"></a>快取行為

Azure API for FHIR 將會快取最多5分鐘的決策。 如果您授與使用者對 FHIR 伺服器的存取權，方法是將它們新增至允許的物件識別碼清單，或從清單中移除它們，您應該預期最多需要五分鐘的時間，才會傳播許可權的變更。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用外部 (次要) Azure Active Directory 租使用者來指派 FHIR 資料平面存取權。 接下來，請瞭解 Azure API for FHIR 的其他設定：
 
>[!div class="nextstepaction"]
>[其他設定 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
