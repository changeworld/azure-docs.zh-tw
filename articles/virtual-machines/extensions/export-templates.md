---
title: 匯出包含 VM 擴充功能的 Azure 資源群組
description: 匯出包含虛擬機器擴充功能的 Resource Manager 範本。
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 529024256b8b3f6da7146787d3a37c59a13b5bd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86494746"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>匯出包含 VM 擴充功能的資源群組

Azure 資源群組可以匯出到之後會重新部署的新 Resource Manager 範本中。 匯出程序會解譯現有的資源，並且在部署導致類似的資源群組時，建立 Resource Manager 範本。 針對包含虛擬機器擴充功能的資源群組使用資源群組匯出選項時，必須考慮幾個項目，例如擴充功能相容性和受保護的設定。

此文件詳述資源群組匯出程序的運作方式 (關於處理虛擬機器擴充功能)，包括支援的擴充功能清單以及有關處理受保護資料的詳細資訊。

## <a name="supported-virtual-machine-extensions"></a>支援的虛擬機器擴充功能

有許多虛擬機器擴充功能可以使用。 並非所有擴充功能都可以使用「自動化指令碼」功能匯出至 Resource Manager 範本。 如果不支援虛擬機器擴充功能，則必須以手動方式將它放回已匯出的範本。

下列擴充功能可以使用自動化指令碼功能匯出。

> Acronis 備份，Acronis 備份 Linux，Bg 資訊，BMC CMT 代理程式 Linux，BMC CMT 代理程式 Windows，Chef 用戶端，自訂腳本，自訂腳本擴充功能，適用于 Linux 的自訂腳本，Datadog Linux 代理程式，Datadog Windows 代理程式，Docker 延伸模組，DSC 延伸模組，Dynatrace Linux，Dynatrace Windows，HPE 安全性應用程式 Defender，IaaS 反惡意程式碼，IaaS 診斷，Linux Chef 用戶端、Site 24x7 Linux Server、Site 全天候 Windows Server、Trend 微 DSA、Trend 微 DSA Linux、適用于 Linux 的 VM 存取、適用于 Linux 的 vm 存取、vm 快照集、VM 快照集 Linux

## <a name="export-the-resource-group"></a>匯出資源群組

若要將資源群組匯出至可重複使用的範本中，請完成下列步驟︰

1. 登入 Azure 入口網站
2. 在 [中樞] 功能表上，按一下 [資源群組]
3. 從清單中選取目標資源群組
4. 在 [資源群組] 刀鋒視窗中，按一下 [自動化指令碼]

![匯出範本](./media/export-templates/template-export.png)

Azure Resource Manager 自動化指令碼會產生 Resource Manager 範本、參數檔案以及數個範例部署指令碼，例如 PowerShell 和 Azure CLI。 此時，您可以使用 [下載] 按鈕來下載已匯出的範本、將它新增為範本程式庫的新範本，或使用 [部署] 按鈕來重新部署。

## <a name="configure-protected-settings"></a>設定受保護的設定

許多 Azure 虛擬機器擴充功能都包含受保護的設定組態，以加密敏感性資料，例如認證及組態字串。 使用自動化指令碼無法匯出受保護的設定。 如有需要，必須將受保護的設定重新插入已匯出的範本中。

### <a name="step-1---remove-template-parameter"></a>步驟 1 - 移除範本參數

匯出資源群組時，系統會建立單一範本參數，以提供值給匯出的受保護設定。 您可移除此參數。 若要移除此參數，請瀏覽參數清單並刪除看似此 JSON 範例的參數。

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>步驟 2 - 取得受保護的設定屬性

因為每個受保護的設定都有一組必要的屬性，所以必須蒐集這些屬性的清單。 在 [GitHub 上的 Azure Resource Manager 結構描述](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)中可以找到每個受保護設定組態的參數。 這個結構描述只包含本文件的概觀一節中所列擴充功能的參數集。 

從結構描述儲存機制中，搜尋所需的擴充功能，例如 `IaaSDiagnostics`。 一旦找到擴充功能 `protectedSettings` 物件，請記下每個參數。 在 `IaasDiagnostic` 擴充功能的範例中，必要參數為 `storageAccountName``storageAccountKey` 和 `storageAccountEndPoint`。

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>步驟 3 - 重新建立受保護的組態

在匯出的範本上，搜尋 `protectedSettings` 並以包含必要擴充功能參數和值取代匯出受保護的設定物件。，針對每個新的。

在 `IaasDiagnostic` 擴充功能的範例中，新的受保護設定組態會如以下範例所示︰

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

最終的擴充功能資源會如以下 JSON 範例所示︰

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

如果使用範本參數來提供屬性值，則需建立這些參數。 建立受保護設定值的範本參數時，請務必使用 `SecureString` 參數類型，以便確保敏感值的安全。 如需使用參數的詳細資訊，請參閱[編寫 Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)。

在 `IaasDiagnostic` 擴充功能的範例中，Resource Manager 範本的 parameters 區段中會建立下列參數。

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

此時，可以使用任何範本部署方法來部署範本。
