---
title: 使用 Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: 瞭解如何開始使用 Azure 數位 Twins CLI 並使用。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 34afc732d64a7aa29261989cc790c8a113208d96
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331765"
---
# <a name="use-the-azure-digital-twins-cli"></a>使用 Azure Digital Twins CLI

除了在 Azure 入口網站中管理 Azure 數位 Twins 實例之外，Azure 數位 Twins 還有 **命令列介面 (CLI) ** ，可讓您用來執行服務的大部分主要動作，包括：
* 管理 Azure 數位 Twins 實例
* 管理模型
* 管理數位 twins
* 管理對應項關聯性
* 設定端點
* 管理 [路由](concepts-route-events.md)
*  (Azure RBAC) 透過 Azure 角色型存取控制設定[安全性](concepts-security.md)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>使用 (部署和驗證) 

除了一般管理您的實例之外，CLI 也是部署和驗證的有用工具。
* 控制平面命令可用來讓新實例的部署可重複使用或自動化。
* 您可以使用資料平面命令快速檢查實例中的值，並確認作業如預期般完成。

## <a name="get-the-extension"></a>取得延伸模組

Azure 數位 Twins 命令是 [適用于 Azure CLI 的 Azure IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能的一部分。 您可以在命令集的參考檔中，查看命令的完整清單及其使用方式 `az iot` ： [ *az dt*命令參考](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)。

您可以使用下列步驟，確定您擁有最新版本的擴充功能。 您可以在 [Azure Cloud Shell](../cloud-shell/overview.md) 或 [本機 Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)中執行這些命令。

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>後續步驟

透過參考檔探索 CLI 和其完整的命令集：
* [*az dt* 命令參考](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)