---
title: 適用於 Linux 的 Log Analytics 虛擬機器擴充功能
description: 使用虛擬機器擴充功能在 Linux 虛擬機器上部署 Log Analytics 代理程式。
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: d7ebb23cfc0ffdcfcac7ecb4ecbd9b9a665e6130
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029088"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>適用於 Linux 的 Log Analytics 虛擬機器擴充功能

## <a name="overview"></a>概觀

對於雲端和內部部署資產，Azure 監視器記錄提供監視、警示和警示補救功能。 Microsoft 已發佈並支援適用於 Linux 的 Log Analytics 虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Log Analytics 工作區中註冊虛擬機器。 本文件詳述適用於 Linux 的 Log Analytics 虛擬機器擴充功能所支援的平台、組態和部署選項。

>[!NOTE]
>Microsoft Operations Management Suite (OMS) 正在轉換為 Azure 監視器，而適用於 Windows 或 Linux 的 OMS 代理程式屬於此轉換的一部份，之後會將其稱為適用於 Windows 的 Log Analytics 代理程式和適用於 Linux 的 Log Analytics 代理程式。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

關於支援的 Linux 發行版本，如需詳細資訊，請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) 一文。

### <a name="agent-and-vm-extension-version"></a>代理程式和 VM 擴充功能版本
下表針對每個版本提供 Log Analytics VM 擴充功能和 Log Analytics 代理程式套件組合的版本對應。 隨附 Log Analytics 代理程式套件組合版本的版本資訊連結。 版本資訊包含錯誤修正和適用於指定代理程式版本的新功能詳細資料。  

| Log Analytics Linux VM 擴充功能版本 | Log Analytics 代理程式套件組合版本 | 
|--------------------------------|--------------------------|
| 1.13.13 | [1.13.7-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.7-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45) \(英文\)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127) \(英文\)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127) \(英文\)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15) \(英文\)|  

### <a name="azure-security-center"></a>Azure 資訊安全中心

Azure 資訊安全中心會自動佈建 Log Analytics 代理程式，並且將它連線至 Azure 訂用帳戶中 ASC 建立的預設 Log Analytics 工作區。 如果您使用的是 Azure 資訊安全中心，請不要執行此文件中的步驟。 這樣做會覆寫已設定的工作區，並中斷與 Azure 資訊安全中心的連線。

### <a name="internet-connectivity"></a>網際網路連線

適用於 Linux 的 Log Analytics 代理程式擴充功能會要求目標虛擬機器連線到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Log Analytics 代理程式擴充功能的結構描述。 此擴充功能需要來自目標 Log Analytics 工作區的工作區 ID 和工作區金鑰，這些值可於 Azure 入口網站中[在 Log Analytics 工作區尋找](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)。 由於工作區金鑰應視為敏感性資料，因此應儲存在受保護的設定組態中。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。 請注意，**workspaceId** 和 **workspaceKey** 區分大小寫。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>上述結構描述假設會將它放置於範本的根層級。 如果您將它放置於範本中的虛擬機器資源內部，那麼也應該變更 `type` 和 `name` 屬性，如[後續內容](#template-deployment)所述。

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (例如) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例如) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 如果部署一或多部虛擬機器之後需要設定 (例如上架至 Azure 監視器記錄)，就很適合使用範本。 [Azure 快速入門資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)中有一個範例 Resource Manager 範本包含 Log Analytics 代理程式 VM 擴充功能。 

虛擬機器擴充功能的 JSON 設定可以巢狀方式置於虛擬機器資源內部，或放在 Resource Manager JSON 範本的根目錄或最上層。 JSON 設定的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

下列範例假設 VM 擴充功能以巢狀方式置於虛擬機器資源內部。 在巢狀處理擴充資源時，JSON 會放在虛擬機器的 `"resources": []` 物件中。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

將擴充 JSON 置於範本的根目錄時，資源名稱包含父系虛擬機器的參考，而類型可反映巢狀的組態。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將 Log Analytics 代理程式 VM 擴充功能部署到現有的虛擬機器。 請將下方的 *myWorkspaceKey* 值換成您的工作區金鑰，將 *myWorkspaceId* 值換成您的工作區識別碼。 在 Azure 入口網站中的 [進階設定] 下，您可以在 Log Analytics 工作區中找到這些值。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>錯誤碼及其意義

| 錯誤碼 | 意義 | 可能的動作 |
| :---: | --- | --- |
| 9 | 啟用提前呼叫 | [將 Azure Linux 代理程式更新](./update-linux-agent.md)為最新的可用版本。 |
| 10 | VM 已經連線到 Log Analytics 工作區 | 若要將 VM 連線到擴充功能結構描述中所指定的工作區，請在公用設定中將 stopOnMultipleConnections 設定為 false，或是移除此屬性。 針對此 VM 所連線的每個工作區都會向此 VM 計費一次。 |
| 11 | 提供給擴充功能的組態無效 | 依照上述範例來設定部署所需的所有屬性值。 |
| 17 | Log Analytics 套件安裝失敗 | 
| 19 | OMI 套件安裝失敗 | 
| 20 | SCX 套件安裝失敗 |
| 51 | VM 的作業系統上不支援此擴充功能 | |
| 55 | 無法連線至 Azure 監視器服務、遺漏必要套件或 dpkg 套件管理員遭到鎖定| 請確認系統是否有網際網路存取權，或已提供有效的 HTTP Proxy。 此外，請檢查工作區識別碼的正確性，並確認已安裝 curl 和 tar 公用程式。 |

如需其他疑難排解資訊，請參閱 [Log Analytics-Agent-for-Linux 疑難排解指南](../../azure-monitor/platform/vmext-troubleshoot.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
