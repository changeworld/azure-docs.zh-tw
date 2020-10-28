---
title: 使用 Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: 瞭解如何開始使用 Azure 數位 Twins CLI 並使用。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5e4c49e7aea05b6f430860eb6975713f59ad8080
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635978"
---
# <a name="use-the-azure-digital-twins-cli"></a>使用 Azure Digital Twins CLI

除了在 Azure 入口網站中管理您的 Azure 數位 Twins 實例之外，Azure 數位 Twins 還有一個 **命令集，可 [Azure CLI](/cli/azure/what-is-azure-cli)讓** 您用來執行服務的大部分主要動作，包括：
* 管理 Azure 數位 Twins 實例
* 管理模型
* 管理數位 twins
* 管理對應項關聯性
* 設定端點
* 管理 [路由](concepts-route-events.md)
*  (Azure RBAC) 透過 Azure 角色型存取控制設定[安全性](concepts-security.md)

命令集稱為 **az dt** ，屬於 [適用于 Azure CLI 的 Azure IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能的一部分。 您可以在命令集的參考檔中，查看命令的完整清單及其使用方式 `az iot` ： [ *az dt* 命令參考](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)。

## <a name="uses-deploy-and-validate"></a>使用 (部署和驗證) 

除了一般管理您的實例之外，CLI 也是部署和驗證的有用工具。
* 控制平面命令可用來讓新實例的部署可重複使用或自動化。
* 您可以使用資料平面命令快速檢查實例中的值，並確認作業如預期般完成。

## <a name="get-the-command-set"></a>取得命令集

Azure 數位 Twins 命令是 azure [iot 擴充功能的一部分，適用于適用于 Azure CLI (azure-iot) 的](https://github.com/Azure/azure-iot-cli-extension)，請遵循下列步驟，以確定您具有 `azure-iot` **az dt** 命令的最新擴充功能。

### <a name="cli-version-requirements"></a>CLI 版本需求

如果您使用 Azure CLI 搭配 PowerShell，則擴充套件會要求您的 Azure CLI 版本必須是 **2.3.1** 或更新版本。

您可以使用下列 CLI 命令來檢查您的 Azure CLI 版本：
```azurecli
az --version
```

如需有關如何將 Azure CLI 安裝或更新至較新版本的指示，請參閱 [*安裝 Azure CLI*](/cli/azure/install-azure-cli)。

### <a name="get-the-extension"></a>取得延伸模組

您可以使用下列步驟，確定您擁有最新版本的 `azure-iot` 擴充功能。 您可以在 [Azure Cloud Shell](../cloud-shell/overview.md) 或 [本機 Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)中執行這些命令。

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>後續步驟

透過參考檔探索 CLI 和其完整的命令集：
* [*az dt* 命令參考](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)