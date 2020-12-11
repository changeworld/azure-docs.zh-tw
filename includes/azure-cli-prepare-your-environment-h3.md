---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000042"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>備妥環境以使用 Azure CLI

- 使用 Bash 環境的 [Azure Cloud Shell](../articles/cloud-shell/quickstart.md)。

   [![內嵌啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)   
- 如果想要，請[安裝](/cli/azure/install-azure-cli) Azure CLI 以執行 CLI 參考命令。
   - 如果您使用的是本機安裝，請使用 [az login](/cli/azure/reference-index#az-login) 命令，透過 Azure CLI 來登入。  請遵循您終端機上顯示的步驟，完成驗證程序。  如需其他登入選項，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli)。
  - 出現提示時，請在第一次使用時安裝 Azure CLI 擴充功能。  如需延伸模組詳細資訊，請參閱[使用 Azure CLI 延伸模組](/cli/azure/azure-cli-extensions-overview)。
  - 執行 [az version](/cli/azure/reference-index?#az_version) 以尋找已安裝的版本和相依程式庫。 若要升級至最新版本，請執行 [az upgrade](/cli/azure/reference-index?#az_upgrade)。