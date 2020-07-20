---
title: CLI：使用流量管理員調整應用程式
description: 了解如何使用 Azure CLI 將 App Service 應用程式的部署和管理自動化。 此範例說明如何使用流量管理員將應用程式調整為全球可用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: fcd82297ae698707503a69d9e47f6ca4241d6db9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80044874"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>使用 Azure CLI 透過高可用性架構將 App Service 應用程式調整為全球可用

此範例指令碼會建立一個資源群組、兩個 App Service 方案、兩個應用程式、一個流量管理員設定檔和兩個流量管理員端點。 完成此練習後，您就會擁有高可用性架構，其會根據最低網路延遲來提供可供全球使用的應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，需要 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立資源群組、App Service 應用程式、流量管理員設定檔及所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 建立 App Service 方案。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 建立 App Service 應用程式。 |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) | 建立 Azure 流量管理員設定檔。 |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) | 新增端點至 Azure 流量管理員設定檔。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure App Service 文件](../samples-cli.md)中找到其他的 App Service CLI 指令碼範例。
