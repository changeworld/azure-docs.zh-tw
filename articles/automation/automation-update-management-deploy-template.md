---
title: 使用 Azure 資源管理器範本進行板載更新管理 |微軟文檔
description: 可以使用 Azure 資源管理器範本將 Azure 自動化更新管理解決方案加入。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925796"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>使用 Azure 資源管理器範本的板載更新管理解決方案

可以使用[Azure 資源管理器範本](../azure-resource-manager/templates/template-syntax.md)在資源組中啟用 Azure 自動化更新管理解決方案。 本文提供了一個示例範本，用於自動執行以下操作：

* 創建 Azure 監視器日誌分析工作區。
* 創建 Azure 自動化帳戶。
* 如果尚未連結，則將自動化帳戶連結到日誌分析工作區。
* Azure 自動化更新管理解決方案

該範本不自動載入一個或多個 Azure 或非 Azure VM。

如果您已在訂閱中受支援的區域部署了日誌分析工作區和自動化帳戶，則它們未連結，並且工作區尚未部署更新管理解決方案，使用此範本成功創建連結並部署更新管理解決方案。 

## <a name="api-versions"></a>API 版本

下表列出此範例中使用的資源 API 版本。

| 資源 | 資源類型 | API 版本 |
|:---|:---|:---|
| 工作區 | workspaces | 2017-03-15-preview |
| 自動化帳戶 | automation | 2015-10-31 | 
| 解決方法 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>在使用範本之前

如果選擇在本地安裝和使用 PowerShell，本文需要 Azure PowerShell Az 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。 使用 Azure PowerShell，部署使用[新阿茲資源組部署](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果選擇在本地安裝和使用 CLI，則本文要求您運行 Azure CLI 版本 2.1.0 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI，此部署使用[az 組部署創建](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

JSON 範本配置為提示您：

* 工作區的名稱
* 要在 中創建工作區的區域
* 自動化帳戶的名稱
* 要在 中創建帳戶的區域

JSON 範本為可能用作環境中標準配置的其他參數指定預設值。 您可以將範本存儲在 Azure 存儲帳戶中，以便組織中共用訪問。 有關使用範本的詳細資訊，請參閱[使用資源管理器範本和 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)。

範本中的以下參數使用日誌分析工作區的預設值設置：

* SKU - 預設為在 2018 年 4 月定價模型中發行的全新每 GB 定價層
* 資料保留 - 預設為 30 天

>[!WARNING]
>如果在已選擇加入 2018 年 4 月全新定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 **PerGB2018**。
>

>[!NOTE]
>在使用此範本之前，請查看[其他詳細資訊](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)以完全瞭解工作區配置選項，如存取控制模式、定價層、保留和容量預留級別。 如果您是 Azure 監視器日誌的新增功能，並且尚未部署工作區，則應查看[工作區設計](../azure-monitor/platform/design-logs-deployment.md)指南以瞭解存取控制，並瞭解我們建議組織的設計實現策略。

## <a name="deploy-template"></a>部署範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "pricingTier": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": { 
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. 編輯範本以符合您的需求。

3. 將此檔保存為"部署UMSolutiontemplate.json"到本地資料夾。

4. 您已準備好部署此範本。 可以使用 PowerShell 或 Azure CLI。 當您提示您創建工作區和自動化帳戶名稱時，請提供在所有 Azure 訂閱中全域唯一的名稱。

    **電源外殼**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    部署需要幾分鐘的時間才能完成。 完成後，您會看到類似下列包含結果的訊息：

    ![部署完成時的範例結果](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>後續步驟

現在部署了更新管理解決方案，您可以啟用 VM 進行管理、查看更新評估並部署更新以使其符合要求。

- 從[Azure 自動化帳戶](automation-onboard-solutions-from-automation-account.md)中考慮一個或多個 Azure 電腦，並手動為非 Azure 電腦。

- 對於 Azure 門戶中虛擬機器頁中的單個 Azure VM。 此方案適用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM。

- 通過從 Azure 門戶中的**虛擬機器**頁面選擇多個[Azure VM。](manage-update-multi.md) 