---
title: 將服務主體新增至 Azure Analysis Services 管理員角色 |Microsoft Docs
description: 瞭解如何將自動化服務主體新增至 Azure Analysis Services server 系統管理員角色
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212506"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>將服務主體新增至伺服器管理員角色 

 若要將無人看管的 PowerShell 工作自動化，服務主體必須擁有受控 Analysis Services 伺服器的**伺服器管理員**權限。 本文說明如何將服務主體新增至 Azure AS 伺服器上的伺服器管理員角色。 您可以使用 SQL Server Management Studio 或 Resource Manager 範本來執行此動作。
 
> [!NOTE]
> 對於使用 Azure PowerShell Cmdlet 的伺服器作業，服務主體也必須屬於[Azure 角色型存取控制（RBAC）](../role-based-access-control/overview.md)中資源的**擁有**者角色。 

## <a name="before-you-begin"></a>開始之前
完成這項工作前，您必須在 Azure Active Directory 中註冊服務主體。

[建立服務主體 - Azure 入口網站](../active-directory/develop/howto-create-service-principal-portal.md)   
[建立服務主體 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio

您可以使用 SQL Server Management Studio （SSMS）來設定伺服器系統管理員。 若要完成這項工作，您必須擁有 Azure AS 伺服器的[伺服器管理員](analysis-services-server-admins.md)權限。 

1. 在 SSMS 中，連線至 Azure AS 伺服器。
2. 在 [伺服器屬性] >  [安全性] 中，按一下 [新增]。
3. 在 [選取使用者或群組] 中，以名稱搜尋您註冊的應用程式，選取它，然後按一下 [新增]。

    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 確認服務主體帳戶識別碼，然後按一下 [確定]。
    
    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>使用 Resource Manager 範本

您也可以使用 Azure Resource Manager 範本來部署 Analysis Services 伺服器，以設定伺服器管理員。 執行部署的身分識別必須屬於[Azure 角色型存取控制（RBAC）](../role-based-access-control/overview.md)中資源的「**參與者**」角色。

> [!IMPORTANT]
> 服務主體必須使用 `app:{service-principal-client-id}@{azure-ad-tenant-id}`的格式來新增。

下列 Resource Manager 範本會部署 Analysis Services 伺服器，並將指定的服務主體新增至 Analysis Services 系統管理員角色：

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

## <a name="related-information"></a>相關資訊

* [下載 SQL Server PowerShell 模組](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下載 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


