---
title: 使用適用于伺服器的 Azure Arc 進行 VM 擴充功能管理
description: 適用于伺服器 (預覽) 的 Azure Arc 可以管理虛擬機器擴充功能的部署，以使用非 Azure Vm 來提供部署後設定和自動化工作。
ms.date: 06/17/2020
ms.topic: conceptual
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.openlocfilehash: eb1de0c2a5b995231c2972fca7279195cbaea9e7
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809624"
---
# <a name="virtual-machine-extension-management-with-azure-arc-for-servers-preview"></a>使用適用于伺服器的 Azure Arc 管理虛擬機器擴充功能 (預覽) 

虛擬機器 (VM) 延伸模組是小型應用程式，可在 Azure Vm 上提供部署後設定和自動化工作。 例如，如果虛擬機器需要軟體安裝、防毒保護，或是要在其中執行指令碼，您可以使用 VM 延伸模組。

適用于伺服器的 azure Arc (預覽) 可讓您將 Azure VM 擴充功能部署到非 Azure Windows 和 Linux Vm，並透過其生命週期簡化混合式機器內部部署、邊緣和其他雲端環境的管理。

## <a name="key-benefits"></a>主要權益

適用于伺服器 (預覽的 Azure Arc) VM 擴充功能支援提供下列主要優點：

* 使用[Azure 自動化狀態設定](../../automation/automation-dsc-overview.md)集中儲存設定，並維護透過 DSC VM 擴充功能啟用的混合式連線機器所需的狀態。

* 在透過 Log Analytics 代理程式 VM 擴充功能啟用[Azure 監視器中](../../azure-monitor/platform/data-platform-logs.md)，收集記錄檔資料以進行分析。 這適用于跨各種來源的資料執行複雜的分析。

* 有了[適用於 VM 的 Azure 監視器](../../azure-monitor/insights/vminsights-overview.md)，會分析 Windows 和 Linux vm 的效能，並監視其進程和其他資源和外部進程的相依性。 這是透過啟用 Log Analytics 代理程式和相依性代理程式 VM 延伸模組來達成。

* 使用自訂腳本擴充功能，在混合式連線電腦上下載並執行腳本。 此擴充功能適用於部署後設定、軟體安裝或其他任何設定/管理工作。

## <a name="availability"></a>可用性

VM 擴充功能僅適用于[支援的區域](overview.md#supported-regions)清單。 請確定您將電腦上架到其中一個區域。

## <a name="extensions"></a>延伸模組

在此預覽中，我們支援 Windows 和 Linux 電腦上的下列 VM 擴充功能。

|延伸模組 |OS |發行者 |其他資訊 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows 自訂腳本擴充功能](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft PowerShell|[Windows PowerShell DSC 延伸模組](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 代理程式 |Windows |Microsoft.EnterpriseCloud.Monitoring |[適用于 Windows 的 Log Analytics VM 擴充功能](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [適用于 Windows 的相依性代理程式虛擬機器擴充功能](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure 擴充功能 |[Linux 自訂腳本擴充功能第2版](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[適用于 Linux 的 PowerShell DSC 擴充功能](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 代理程式 |Linux |Microsoft.EnterpriseCloud.Monitoring |[適用于 Linux 的 Log Analytics VM 擴充功能](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [適用于 Linux 的相依性代理程式虛擬機器擴充功能](../../virtual-machines/extensions/agent-dependency-linux.md) |

VM 擴充功能可以搭配 Azure Resource Manager 範本、Azure 入口網站或 Azure PowerShell 在以 Arc 管理的混合式伺服器上執行， (preview) 。

若要深入瞭解 Azure 已連線的機器代理程式套件，以及有關延伸模組代理程式元件的詳細資料，請參閱[代理程式總覽](agent-overview.md#agent-component-details)。

## <a name="prerequisites"></a>Prerequisites

這項功能取決於您訂用帳戶中的下列 Azure 資源提供者：

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

如果尚未註冊，請遵循[註冊 Azure 資源提供者](agent-overview.md#register-azure-resource-providers)底下的步驟。

適用于 Linux 的 Log Analytics 代理程式 VM 擴充功能需要在目的電腦上安裝 Python 2.x。

### <a name="connected-machine-agent"></a>Connected Machine 代理程式

請確認您的電腦符合 Azure 連線機器代理程式支援的 Windows 和 Linux 作業系統[版本](agent-overview.md#supported-operating-systems)。

這項功能支援的已連線機器代理程式的最小版本為：

* Windows-0.7. x
* Linux-0.8. x

若要將您的電腦升級為所需的代理程式版本，請參閱[升級代理程式](manage-agent.md#upgrading-agent)。

## <a name="enable-extensions-from-the-portal"></a>從入口網站啟用擴充功能

您可以透過 Azure 入口網站，將您的 Arc 用於伺服器 (預覽) 受管理的機器。

1. 在瀏覽器中，移至 [Azure 入口網站](https://aka.ms/arcserver-preview)。

2. 在入口網站中，流覽至 [**機器]-[Azure Arc** ]，並從清單中選取您的混合式機器。

3. 選擇 [**擴充**功能]，然後選取 [**新增**]。 請從可用擴充功能清單選擇您想要的擴充功能，並遵循精靈中的指示。 在此範例中，我們將部署 Log Analytics VM 擴充功能。 

    ![選取所選機器的 VM 延伸模組](./media/manage-vm-extensions/add-vm-extensions.png)

    下列範例示範如何從 Azure 入口網站安裝 Log Analytics VM 擴充功能：

    ![安裝 Log Analytics VM 擴充功能](./media/manage-vm-extensions/mma-extension-config.png)

    若要完成安裝，您必須提供工作區識別碼和主要金鑰。 如果您不熟悉如何尋找此資訊，請參閱[取得工作區識別碼和金鑰](../../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)。

4. 確認所提供的必要資訊之後，請選取 [**建立**]。 隨即會顯示部署的摘要，您可以查看部署的狀態。

>[!NOTE]
>雖然多個擴充功能可以一起批次處理並加以處理，但它們會以序列方式進行安裝。 第一個延伸模組安裝完成後，會嘗試安裝下一個延伸模組。

## <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

VM 擴充功能可以新增至 Azure Resource Manager 範本，並使用範本的部署執行。 使用 (preview) 伺服器的 Arc 支援的 VM 擴充功能，您可以使用 Azure PowerShell 在 Linux 或 Windows 電腦上部署支援的 VM 擴充功能。 下列每個範例都包含範本檔案和參數檔案，其中包含要提供給範本的範例值。

>[!NOTE]
>雖然多個擴充功能可以一起批次處理並加以處理，但它們會以序列方式進行安裝。 第一個延伸模組安裝完成後，會嘗試安裝下一個延伸模組。

### <a name="deploy-the-log-analytics-vm-extension"></a>部署 Log Analytics VM 擴充功能

若要輕鬆部署 Log Analytics 代理程式，請提供下列範例，以在 Windows 或 Linux 上安裝代理程式。

#### <a name="template-file-for-linux"></a>適用于 Linux 的範本檔案

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>適用于 Windows 的範本檔案

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>參數檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

將範本和參數檔案儲存至磁片，並使用部署的適當值來編輯參數檔案。 接著，您可以使用下列命令，在資源群組內的所有已連線電腦上安裝擴充功能。 此命令會使用*TemplateFile*參數來指定範本和*TemplateParameterFile*參數，以指定包含參數和參數值的檔案。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>部署自訂腳本擴充功能

若要使用自訂腳本擴充功能，請提供下列範例，以在 Windows 和 Linux 上執行。 如果您不熟悉自訂腳本延伸模組，請參閱[Windows 的自訂腳本擴充](../../virtual-machines/extensions/custom-script-windows.md)功能或[適用于 Linux 的自訂腳本擴充](../../virtual-machines/extensions/custom-script-linux.md)功能。 搭配混合式機器使用此延伸模組時，您應該瞭解幾個不同的特性：

* 具有 Azure VM 自訂腳本擴充功能的支援作業系統清單不適用於伺服器的 Azure Arc。 您可以在[這裡](agent-overview.md#supported-operating-systems)找到伺服器 Arc 支援的 OSs 清單。

* 關於 Azure 虛擬機器擴展集或傳統 Vm 的設定詳細資料不適用。

* 如果您的電腦需要在外部下載腳本，而且只能透過 proxy 伺服器進行通訊，您必須設定[連線的電腦代理程式](manage-agent.md#update-or-remove-proxy-settings)來設定 proxy 伺服器環境變數。

「自訂指令碼擴充功能」組態會指定指令碼位置和要執行命令等項目。 這項設定是在適用于 Linux 和 Windows 混合式電腦的 Azure Resource Manager 範本中指定。

#### <a name="template-file-for-linux"></a>適用于 Linux 的範本檔案

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>適用于 Windows 的範本檔案

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>參數檔案

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>部署 PowerShell DSC 延伸模組

若要使用 PowerShell DSC 延伸模組，請提供下列範例，以在 Windows 和 Linux 上執行。 如果您不熟悉 PowerShell DSC 延伸模組，請參閱[DSC 延伸模組處理常式總覽](../../virtual-machines/extensions/dsc-overview.md)。 搭配混合式機器使用此延伸模組時，您應該瞭解幾個不同的特性：

* 具有 Azure VM PowerShell DSC 擴充功能的支援作業系統清單不適用於伺服器的 Azure Arc。 您可以在[這裡](agent-overview.md#supported-operating-systems)找到伺服器 Arc 支援的 OSs 清單。

* 如果您的電腦需要在外部下載腳本，而且只能透過 proxy 伺服器進行通訊，您必須設定[連線的電腦代理程式](manage-agent.md#update-or-remove-proxy-settings)來設定 proxy 伺服器環境變數。

#### <a name="template-file-for-linux"></a>適用于 Linux 的範本檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>適用于 Windows 的範本檔案

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>參數檔案

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>相依性代理程式

若要使用 Azure 監視器相依性代理程式擴充功能，請提供下列範例，以在 Windows 和 Linux 上執行。 如果您不熟悉 Dependency agent，請參閱[Azure 監視器代理程式的總覽](../../azure-monitor/platform/agents-overview.md#dependency-agent)。

#### <a name="template-file-for-linux"></a>適用于 Linux 的範本檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>適用于 Windows 的範本檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="troubleshooting"></a>疑難排解

您可以從 Azure 入口網站抓取有關擴充功能部署狀態的資料。

下列疑難排解步驟適用於所有虛擬機器擴充功能。

1. 若要檢查來賓代理程式記錄檔，請查看您的擴充功能是在 for Windows 中布建 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` ，以及下的 Linux 時的活動 `/var/lib/GuestConfig/ext_mgr_logs` 。

2. 如需 Windows 的詳細資訊，請參閱特定擴充功能的延伸模組記錄檔 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` 。 延伸模組輸出會記錄到中 Linux 上安裝的每個延伸模組的檔案 `/var/log/GuestConfig/extension_logs` 。

3. 請參閱延伸模組特定檔的疑難排解章節，以瞭解錯誤碼、已知問題等。您可以在延伸模組總覽的**疑難排解和支援**一節中，找到每個擴充功能的其他疑難排解資訊。 這包括寫入記錄檔之錯誤碼的描述。 延伸模組文章已連結于本文稍早所述的延伸模組[資料表](#extensions)。

4. 查看系統記錄。 檢查是否有其他作業對擴充功能造成干擾，例如，長時間執行且需要以獨佔方式存取套件管理員的其他應用程式安裝。

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-policy.md) 啟用監視等等項目，管理您的機器。

- 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。