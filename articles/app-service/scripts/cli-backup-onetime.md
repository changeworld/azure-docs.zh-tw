---
title: CLI：備份應用程式
description: 了解如何使用 Azure CLI 將 App Service 應用程式的部署和管理自動化。 此範例說明如何備份應用程式。
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: cca0f927c6824642f11254743f3bdf275ca7e5d9
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006849"
---
# <a name="back-up-an-app-using-cli"></a>使用 CLI 備份應用程式

此範例指令碼會在 App Service 中建立應用程式及其相關資源，然後為其建立一次性備份。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，需要 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [`az storage account create`](/cli/azure/storage/account#az-storage-account-create) | 建立儲存體帳戶。 |
| [`az storage container create`](/cli/azure/storage/container#az-storage-container-create) | 建立 Azure 儲存體容器。 |
| [`az storage container generate-sas`](/cli/azure/storage/container#az-storage-container-generate-sas) | 產生 Azure 儲存體容器的 SAS 權杖。  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | 建立 App Service 方案。 |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | 建立 App Service 應用程式。 |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup#az-webapp-config-backup-create) | 建立 App Service 應用程式的備份。 |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | 取得 App Service 應用程式的備份清單。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure App Service 文件](../samples-cli.md)中找到其他的 App Service CLI 指令碼範例。
