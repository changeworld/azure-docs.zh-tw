---
title: 使用 Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: 瞭解如何開始使用 Azure 數位 Twins CLI 並使用。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2c642b2441d1f30c31e707a237732e028f548ac5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298176"
---
# <a name="use-the-azure-digital-twins-cli"></a>使用 Azure Digital Twins CLI

除了在 Azure 入口網站中管理 Azure 數位 Twins 實例之外，Azure 數位 Twins 還有 **命令列介面 (CLI) ** ，可讓您用來執行服務的大部分主要動作，包括：
* 管理 Azure 數位 Twins 實例
* 管理模型
* 管理數位 twins
* 管理對應項關聯性
* 設定端點
* 管理 [路由](concepts-route-events.md)
* 透過以角色為基礎的存取控制來設定 [安全性](concepts-security.md) (RBAC) 

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>使用 (部署和驗證) 

除了一般管理您的實例之外，CLI 也是部署和驗證的有用工具。
* 控制平面命令可用來讓新實例的部署可重複使用或自動化。
* 您可以使用資料平面命令快速檢查實例中的值，並確認作業如預期般完成。

## <a name="get-the-extension"></a>取得延伸模組

Azure 數位 Twins 命令是 [適用于 Azure CLI 的 Azure IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能的一部分。 您可以將這些命令的參考檔視為 `az iot` 命令集： [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)的一部分來查看。

您可以使用下列步驟，確定您擁有最新版本的擴充功能。 您可以在 [Azure Cloud Shell](../cloud-shell/overview.md) 或 [本機 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)中執行這些命令。

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>接下來的步驟

如需 CLI 命令的替代方法，請參閱如何使用 Api 和 Sdk 管理 Azure 數位 Twins 實例：
* [*How to：使用 Azure 數位 Twins Api 和 Sdk*](how-to-use-apis-sdks.md)
