---
title: 為多層式應用程式建立 VNet - Azure CLI 指令碼範例
description: Azure CLI 指令碼範例 - 為多層式應用程式建立虛擬網路。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 39a993324a476f0e1f1d17858ae126742e4f69f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74083852"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>為多層式應用程式建立網路的指令碼範例

此指令碼範例會建立一個具有前端和後端子網路的虛擬網路。 傳送到前端子網路的流量會限制為 HTTP 和 SSH，而傳送到後端子網路的流量則限制為 MySQL 且連接埠為 3306。 執行此指令碼之後，您會有兩部虛擬機器，每個子網路中各有一部，可供您在其中部署 Web 伺服器和 MySQL 軟體。

您可以從 Azure [Cloud Shell](https://shell.azure.com/bash) 或從本機的 Azure CLI 安裝來執行指令碼。 如果是在本機使用 CLI，需要執行 2.0.28 版或更新版本，方可使用該指令碼。 若要尋找已安裝的版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您在本機執行 CLI，則也需要執行 `az login` 以建立與 Azure 的連線。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>範例指令碼


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源：

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、虛擬網路及網路安全性群組。 下表中的每個命令都會連結至命令特定的文件：

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

可以在[虛擬網路 CLI 範例](../cli-samples.md)中找到其他的虛擬網路 CLI 指令碼範例。
