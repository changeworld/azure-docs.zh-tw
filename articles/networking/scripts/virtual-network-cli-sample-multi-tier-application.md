---
title: Azure CLI 文稿範例 - 為多層應用程式建立網路
description: Azure CLI 指令碼範例 - 為多層式應用程式建立虛擬網路。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 97e26eaed72bc15ebc46a893d269b05484d8a064
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521547"
---
# <a name="create-a-network-for-multi-tier-applications"></a>為多層式應用程式建立網路

此指令碼範例會建立一個具有前端和後端子網路的虛擬網路。 傳送到前端子網路的流量會限制為 HTTP 和 SSH，而傳送到後端子網路的流量則限制為 MySQL 且連接埠為 3306。 執行此指令碼之後，您將有兩部虛擬機器，每個子網路中各有一部，可供您在其中部署 Web 伺服器和 MySQL 軟體。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>範例指令碼


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、虛擬網路及網路安全性群組。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az network vnet create](/cli/azure/network/vnet) | 建立 Azure 虛擬網路和前端子網路。 |
| [az network subnet create](/cli/azure/network/vnet/subnet) | 建立後端子網路。 |
| [az network public-ip create](/cli/azure/network/public-ip) | 建立公用 IP 位址以從網際網路存取 VM。 |
| [az network nic create](/cli/azure/network/nic) | 建立虛擬網路介面，並將它們連結到虛擬網路的前端和後端子網路。 |
| [az network nsg create](/cli/azure/network/nsg) | 建立與前端和後端子網路關聯的網路安全性群組 (NSG)。 |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |建立對特定子網路允許或封鎖特定連接埠的 NSG 規則。 |
| [az vm create](/cli/azure/vm) | 建立虛擬機器，並將 NIC 連結到每個 VM。 此命令也會指定要使用的虛擬機器映像和系統管理認證。 |
| [az group delete](/cli/azure/group) | 刪除資源群組及其包含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

其他網路 CLI 文稿範例可在[Azure 網路概述文件中](../cli-samples.md)找不到
