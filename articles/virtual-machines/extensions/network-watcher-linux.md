---
title: 適用于 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能
description: 使用虛擬機器擴充功能在 Linux 虛擬機器上部署網路監看員代理程式。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 476241ad3d4077f2da0c513e3c989218701232ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81255748"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>適用於 Linux 的網路監看員代理程式虛擬機器擴充功能

## <a name="overview"></a>總覽

[Azure 網路監看員](/azure/network-watcher/)是網路效能的監視、診斷和分析服務，可讓您監視 Azure 網路。 Azure 虛擬機器 (VM) 上某些網路監看員功能 (如依需求擷取網路流量) 及其他進階功能，均需要網路監看員代理程式虛擬機器擴充功能。

本文詳述適用於 Linux 的網路監看員代理程式虛擬機器擴充功能所支援的平台和部署選項。 安裝代理程式不會讓虛擬機器中斷或需要重新開機。 您可以將延伸模組部署到您部署的虛擬機器中。 若虛擬機器是由 Azure 服務所部署，請檢查服務文件以判斷它是否允許在虛擬機器中安裝延伸模組。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

下列 Linux 散發套件可設定網路監看員代理程式擴充功能：

| 散發 | 版本 |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 和 8 |
| Red Hat | 6 和 7 |
| Oracle Linux | 6.8+ 和 7 |
| SUSE Linux Enterprise Server | 11 和 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5+ 和 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>網際網路連線

網路監看員代理程式的某些功能需要虛擬機器連線到網際網路。 若沒有建立傳出的連線，網路監看員代理程式的某些功能可能會有問題或者無法使用。 有關需要代理程式的網路監看員功能詳細資訊，請參閱[網路監看員文件](/azure/network-watcher/)。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示網路監看員代理程式擴充功能的結構描述。 擴充功能不會要求 (或支援) 任何使用者提供的設定。 擴充功能會依賴其預設設定。

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| 類型 | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>範本部署

您可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 若要部署網路監看員代理程式擴充功能，請在範本中使用上述的 json 結構描述。

## <a name="azure-classic-cli-deployment"></a>Azure 傳統 CLI 部署

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

以下範例會將網路監看員代理程式 VM 擴充功能，部署到透過傳統部署模型部署的現有 VM：

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

以下範例會將網路監看員代理程式 VM 擴充功能，部署到透過資源管理員部署的現有 VM：

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>疑難排解和支援

### <a name="troubleshooting"></a>疑難排解

您可以使用 Azure 入口網站或 Azure CLI 擷取有關擴充功能部署狀態的資料。

下列範例會使用 Azure CLI，顯示透過資源管理員部署之 VM 的 NetworkWatcherAgentLinux 延伸模組部署狀態：

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以參閱[網路監看員檔](/azure/network-watcher/)，或與[MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 azure 專家聯絡。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
