---
title: 建立 DNS 區域和網域名稱記錄 - Azure CLI - Azure DNS
description: 此 Azure CLI 指令碼範例會示範如何建立 DNS 區域和網域名稱記錄
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1b0f08132ace5de7108702b61e89517fd28b4673
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494905"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI 指令碼範例：建立 DNS 區域和記錄

此 Azure CLI 指令碼範例會建立 DNS 區域和網域名稱記錄。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、DNS 區域和所有相關資源。

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器、可用性設定組、負載平衡器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | 建立 Azure DNS 區域。 |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | 將 A  記錄新增至 DNS 區域。 |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | 列出 DNS 區域中的所有 A  記錄集。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

