---
title: 使用 Azure 資源管理員樣本建立自動化帳戶 |微軟文件
description: 可以使用 Azure 資源管理器範本創建 Azure 自動化帳戶。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.openlocfilehash: efe51fbada8ac70b24c16a5c7c1e0e91879e5e9f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618681"
---
# <a name="create-automation-account-using-azure-resource-manager-template"></a>使用 Azure 資源管理員樣本建立自動化帳戶

可以使用[Azure 資源管理器範本](../azure-resource-manager/templates/template-syntax.md)在資源組中創建 Azure 自動化帳戶。 本文提供了一個範例樣本,用於自動執行以下操作:

* 創建 Azure 監視器日誌分析工作區。
* 創建 Azure 自動化帳戶。
* 將自動化帳戶連結到日誌分析工作區。

該範本不會自動載入一個或多個 Azure 或非 Azure VM 或解決方案。 

>[!NOTE]
>使用 Azure 資源管理器樣本時,不支援創建"自動運行為帳戶"帳戶。 要從門戶或使用 PowerShell 手動創建"運行為"帳戶,請參閱[管理"作為帳戶運行」。。](manage-runas-account.md)

## <a name="api-versions"></a>API 版本

下表列出此範例中使用的資源 API 版本。

| 資源 | 資源類型 | API 版本 |
|:---|:---|:---|
| 工作區 | workspaces | 2017-03-15-preview |
| 自動化帳戶 | automation | 2015-10-31 | 

## <a name="before-using-the-template"></a>在使用樣本之前

如果選擇在本地安裝和使用 PowerShell,本文需要 Azure PowerShell Az 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。 使用 Azure PowerShell,部署使用[新阿茲資源群組部署](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果選擇在本地安裝和使用 CLI,則本文要求您運行 Azure CLI 版本 2.1.0 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI,這個部署使用[az 群組部署建立](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

JSON 樣本設定為提示您:

* 工作區的名稱
* 在建立工作區的區域
* 自動化帳戶名稱
* 在建立帳號的區域

JSON 範本為可能用作環境中標準配置的其他參數指定預設值。 您可以將範本儲存在 Azure 儲存帳戶中,以便組織中共享存取。 有關使用樣本的詳細資訊,請參考[使用資源管理員樣本和 Azure CLI 部署資源](../azure-resource-manager/templates/deploy-cli.md)。

樣本中的以下參數使用紀錄分析工作區的預設值設定:

* SKU - 預設為在 2018 年 4 月定價模型中發行的全新每 GB 定價層
* 資料保留 - 預設為 30 天
* 容量預留 - 預設值為 100 GB

>[!WARNING]
>如果在已選擇加入 2018 年 4 月全新定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層是 **PerGB2018**。
>

>[!NOTE]
>在使用此範本之前,請查看[其他詳細資訊](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)以完全瞭解工作區配置選項,如存取控制模式、定價層、保留和容量預留級別。 如果您是 Azure 監視器日誌的新增功能,並且尚未部署工作區,則應查看[工作區設計](../azure-monitor/platform/design-logs-deployment.md)指南以瞭解存取控制,並瞭解我們建議組織的設計實現策略。

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
        "sku": {
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
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook which gets all the ARM resources using the Run As Account (Service Principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
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
                    "Name": "[parameters('sku')]",
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
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
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
     ]
    }
    ```

2. 編輯範本以符合您的需求。 請考慮創建[資源管理器參數檔](../azure-resource-manager/templates/parameter-files.md),而不是將參數作為內聯值傳遞。

3. 將此檔保存為部署 AzAutomationAccttemplate.json 到本地資料夾。

4. 您已準備好部署此範本。 可以使用 PowerShell 或 Azure CLI。 當您提示您創建工作區和自動化帳戶名稱時,請提供在所有 Azure 訂閱中全域唯一的名稱。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    部署需要幾分鐘的時間才能完成。 完成後，您會看到類似下列包含結果的訊息：

    ![部署完成時的範例結果](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>後續步驟

現在,您有了自動化帳戶,您可以創建 Runbook 並自動執行手動流程。
