---
title: 將服務主體新增至 Azure Analysis Services 系統管理員角色 | Microsoft Docs
description: 了解如何將自動化服務主體新增至 Azure Analysis Services 系統管理員角色
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e751d210ab472d82e4abd613d37069a8c591f52
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013928"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>將服務主體新增至伺服器管理員角色 

 若要將無人看管的 PowerShell 工作自動化，服務主體必須擁有受控 Analysis Services 伺服器的**伺服器管理員**權限。 本文說明如何將服務主體新增至 Azure AS 伺服器上的伺服器管理員角色。 您可使用 SQL Server Management Studio 或 Resource Manager 範本來執行此操作。 

> [!NOTE]
> 服務主體必須直接新增至伺服器管理員角色。 將服務主體新增至安全性群組，然後將該安全性群組新增至伺服器管理員角色並不受支援。 

## <a name="before-you-begin"></a>開始之前
完成這項工作前，您必須在 Azure Active Directory 中註冊服務主體。

[建立服務主體 - Azure 入口網站](../active-directory/develop/howto-create-service-principal-portal.md)   
[建立服務主體 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio

您可使用 SQL Server Management Studio (SSMS) 來設定伺服器管理員。 若要完成這項工作，您必須擁有 Azure AS 伺服器的[伺服器管理員](analysis-services-server-admins.md)權限。 

1. 在 SSMS 中，連線至 Azure AS 伺服器。
2. 在 [伺服器屬性] >  [安全性] 中，按一下 [新增]。
3. 在 [選取使用者或群組] 中，以名稱搜尋您註冊的應用程式，選取它，然後按一下 [新增]。

    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 確認服務主體帳戶識別碼，然後按一下 [確定]。
    
    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>使用 Resource Manager 範本

您也可以使用 Azure Resource Manager 範本來部署 Analysis Services 伺服器以設定伺服器管理員。 執行部署的身分識別必須屬於[azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md)中資源的「**參與者**」角色。

> [!IMPORTANT]
> 服務主體必須使用 `app:{service-principal-client-id}@{azure-ad-tenant-id}` 格式來新增。

下列 Resource Manager 範本會部署 Analysis Services 伺服器，並將指定服務主體新增至 Analysis Services 系統管理員角色：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>使用受控識別

您也可以將受控識別新增至 Analysis Services Admins 清單。 例如，您可能有[具有系統指派受控識別的邏輯應用程式](../logic-apps/create-managed-service-identity.md)，並想要為其授與管理 Analysis Services 伺服器的能力。

在 Azure 入口網站和 APO 的大多數部分中，受控識別會使用其服務主體物件識別碼來進行識別。 不過，Analysis Services 需要使用其用戶端識別碼來進行識別。 若要獲得服務主體的用戶端識別碼，您可使用 Azure CLI：

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

或者，您也可以使用 PowerShell：

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

然後您即可使用此用戶端識別碼搭配租用戶識別碼，將受控識別新增至 Analysis Services Admins 清單中，如上所述。

## <a name="related-information"></a>相關資訊

* [下載 SQL Server PowerShell 模組](/sql/ssms/download-sql-server-ps-module)   
* [下載 SSMS](/sql/ssms/download-sql-server-management-studio-ssms)