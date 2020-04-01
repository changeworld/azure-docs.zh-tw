---
title: 將服務主體添加到 Azure 分析服務管理員角色 |微軟文件
description: 瞭解如何向 Azure 分析服務伺服器管理員角色添加自動化服務主體
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 925fbbb51ac240b96486a2c0aa09c850a8d164bc
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408636"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>將服務主體新增至伺服器管理員角色 

 若要將無人看管的 PowerShell 工作自動化，服務主體必須擁有受控 Analysis Services 伺服器的**伺服器管理員**權限。 本文說明如何將服務主體新增至 Azure AS 伺服器上的伺服器管理員角色。 您可以使用 SQL 伺服器管理工作室或資源管理器範本執行此操作。

## <a name="before-you-begin"></a>開始之前
完成這項工作前，您必須在 Azure Active Directory 中註冊服務主體。

[建立服務主體 - Azure 門戶](../active-directory/develop/howto-create-service-principal-portal.md)   
[建立服務主體 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio

您可以使用 SQL 伺服器管理工作室 (SSMS) 設定伺服器管理員。 若要完成這項工作，您必須擁有 Azure AS 伺服器的[伺服器管理員](analysis-services-server-admins.md)權限。 

1. 在 SSMS 中，連線至 Azure AS 伺服器。
2. 在 **「伺服器屬性** > **安全**」中,按下「**添加**」。
3. 在 [選取使用者或群組]**** 中，以名稱搜尋您註冊的應用程式，選取它，然後按一下 [新增]****。

    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 確認服務主體帳戶識別碼，然後按一下 [確定]****。
    
    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>使用 Resource Manager 範本

還可以通過使用 Azure 資源管理器範本部署分析服務伺服器來配置伺服器管理員。 執行部署的識別必須屬於[Azure 基於角色的存取控制 (RBAC)](../role-based-access-control/overview.md)中的資源的 **「參與者」** 角色。

> [!IMPORTANT]
> 必須使用格式`app:{service-principal-client-id}@{azure-ad-tenant-id}`添加服務主體。

以下資源管理員樣本部署分析服務伺服器,其中指定的服務主體添加到分析服務管理員角色中:

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

## <a name="using-managed-identities"></a>使用託管識別

還可以將託管標識添加到分析服務管理員清單中。 例如,您可能具有[具有系統分配的託管標識的邏輯應用](../logic-apps/create-managed-service-identity.md),並希望授予它管理分析服務伺服器的能力。

在 Azure 門戶和 API 的大多數部分,託管標識使用其服務主體物件 ID 進行標識。 但是,分析服務要求使用其用戶端 ID 標識它們。 要取得服務主體的客戶端 ID,可以使用 Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

或者,您可以使用 PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

然後,您可以將此用戶端 ID 與租戶 ID 結合使用,將託管標識添加到分析服務管理員清單中,如上所述。

## <a name="related-information"></a>相關資訊

* [下載 SQL Server PowerShell 模組](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下載 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


