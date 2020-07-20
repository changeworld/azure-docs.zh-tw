---
title: CLI：部署至預備位置
description: 了解如何使用 Azure CLI 將 App Service 應用程式的部署和管理自動化。 此範例說明如何將程式碼部署至預備位置。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 0f535e902bc86ba3c098dd2238766a3bd547781f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057860"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>使用 Azure CLI 建立 App Service 應用程式並將程式碼部署至預備環境

此範例指令碼會在 App Service 中建立應用程式以及稱為「預備」的其他部署位置，然後將範例應用程式部署至「預備」位置。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，需要 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create an app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 建立 App Service 方案。 |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | 建立 App Service 應用程式。 |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az-webapp-deployment-slot-create) | 建立部署位置。 |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | 將 App Service 應用程式與 Git 或 Mercurial 存放庫建立關聯。 |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az-webapp-deployment-slot-swap) | 將指定的部署位置切換到生產環境。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure App Service 文件](../samples-cli.md)中找到其他的 App Service CLI 指令碼範例。
