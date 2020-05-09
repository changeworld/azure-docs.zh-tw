---
title: 使用 Azure Resource Manager 範本來建立自動化帳戶 |Microsoft Docs
description: 您可以使用 Azure Resource Manager 範本來建立 Azure 自動化帳戶。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 19aee9d5fdf3f4a3d74484bb7cb2e609bc2807b4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927851"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立自動化帳戶

您可以使用[Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)在資源群組中建立 Azure 自動化帳戶。 本文提供範例範本，其中：

* 自動建立 Azure 監視器 Log Analytics 工作區。
* 自動建立 Azure 自動化帳戶。
* 將自動化帳戶連結至 Log Analytics 工作區。

此範本不會自動化 Azure 或非 Azure 虛擬機器或解決方案的上架。 

>[!NOTE]
>當您使用 Azure Resource Manager 範本時，不支援建立自動化執行身分帳戶。 若要從入口網站或使用 PowerShell 手動建立執行身分帳戶，請參閱[管理執行身分帳戶](manage-runas-account.md)。

## <a name="api-versions"></a>API 版本

下表列出此範例中使用的資源 API 版本。

| 資源 | 資源類型 | API 版本 |
|:---|:---|:---|
| 工作區 | workspaces | 2017-03-15-preview |
| 自動化帳戶 | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>使用範本之前

如果您選擇在本機安裝和使用 PowerShell，本文需要 Azure PowerShell Az 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。 使用 PowerShell 時，部署會使用[新的-new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果您選擇在本機安裝和使用 Azure CLI，本文會要求您執行2.1.0 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI，此部署會使用[az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

JSON 範本已設定為提示您輸入：

* 工作區的名稱。
* 要在其中建立工作區的區域。
* 自動化帳戶的名稱。
* 要在其中建立帳戶的區域。

會使用 Log Analytics 工作區的預設值來設定範本中的下列參數：

* *sku*預設為在2018年4月定價模型中發行的每 GB 定價層。
* *dataRetention*預設為30天。
* *capacityReservationLevel*預設為 100 GB。

>[!WARNING]
>如果您想要在已加入宣告2018年4月定價模型的訂用帳戶中建立或設定 Log Analytics 工作區，則唯一有效的 Log Analytics 定價層為*PerGB2018*。
>

JSON 範本會為您環境中可能用來做為標準設定的其他參數，指定預設值。 您可以將範本儲存在 Azure 儲存體帳戶中，以便在您的組織中共用存取。 如需使用範本的詳細資訊，請參閱[使用 Resource Manager 範本和 Azure CLI 來部署資源](../azure-resource-manager/templates/deploy-cli.md)。

如果您是 Azure 自動化和 Azure 監視器的新手，請務必瞭解下列設定詳細資料。 當您嘗試建立、設定和使用連結至新自動化帳戶的 Log Analytics 工作區時，它們可以協助您避免錯誤。 

* 請參閱[其他詳細資料](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)，以充分瞭解工作區設定選項，例如存取控制模式、定價層、保留期和容量保留層級。

* 請參閱[工作區](how-to/region-mappings.md)對應，以指定內嵌或參數檔案中支援的區域。 只有特定區域支援連結 Log Analytics 工作區和訂用帳戶中的自動化帳戶。

* 如果您不熟悉 Azure 監視器記錄檔，而且尚未部署工作區，您應該參閱[工作區設計指引](../azure-monitor/platform/design-logs-deployment.md)。 它將協助您瞭解存取控制，並瞭解我們建議您的組織使用的設計實作為策略。

## <a name="deploy-the-template"></a>部署範本

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
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

2. 編輯範本以符合您的需求。 請考慮建立[Resource Manager 參數](../azure-resource-manager/templates/parameter-files.md)檔案，而不是以內嵌值傳遞參數。

3. 將此檔案儲存為 deployAzAutomationAccttemplate 到本機資料夾。

4. 您已準備好部署此範本。 您可以使用 PowerShell 或 Azure CLI。 當系統提示您輸入工作區和自動化帳戶名稱時，請提供在所有 Azure 訂用帳戶中全域唯一的名稱。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    部署可能需要幾分鐘的時間才能完成。 當它發生時，您會看到如下的訊息，其中包含結果。

    ![部署完成時的範例結果](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>後續步驟

現在您已擁有自動化帳戶，您可以建立 runbook 並將手動程式自動化。

* 若要開始使用 PowerShell Runbook，請參閱[建立 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要開始使用 PowerShell 工作流程 runbook，請參閱[建立 Powershell 工作流程 runbook](automation-first-runbook-textual.md)。
* 若要開始使用 Python 2 runbook，請參閱[建立 python runbook](automation-first-runbook-textual-python2.md)。