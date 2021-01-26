---
title: Azure CLI 指令碼部署範例
description: 如何使用 Azure 命令列介面 (CLI) 在 Azure 中建立安全的 Service Fabric Linux 叢集。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 675f6b9ac3ebb05539432b5febc279275f9de7f6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785764"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>在 Azure 中建立安全的 Service Fabric Linux 叢集

此命令會建立自我簽署的憑證，然後將其加入金鑰保存庫並在本機下載憑證。  新的憑證會用來在部署叢集時保護叢集。  您也可以使用現有的憑證，而不用建立新的。  無論是哪一方法，憑證的主體名稱必須與您用來存取 Service Fabric 叢集的網域相符。 必須如此相符，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 TLS。 您無法從 CA 取得 `.cloudapp.azure.com` 網域的 TLS/SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

視需要安裝 [Azure CLI](/en-us/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組、叢集和所有相關資源。

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az sf cluster create](/cli/azure/sf/cluster) | 建立新的 Service Fabric 叢集。  |

## <a name="next-steps"></a>後續步驟

您可以在 [Service Fabric CLI 範例](../samples-cli.md)中找到適用於 Azure Service Fabric 的其他 Service Fabric CLI 範例。
