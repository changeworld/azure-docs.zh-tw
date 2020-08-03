---
title: 使用 Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: 瞭解如何開始使用和使用 Azure 數位 Twins CLI。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f13dc3e86b21a3f4113a7a7c6f477f239315a27
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499087"
---
# <a name="use-the-azure-digital-twins-cli"></a>使用 Azure Digital Twins CLI

除了在 Azure 入口網站中管理您的 Azure 數位 Twins 實例，Azure 數位 Twins 具有**命令列介面（CLI）** ，可讓您用來執行服務的大部分主要動作，包括：
* 管理 Azure 數位 Twins 實例
* 管理模型
* 管理數位 twins
* 管理對應項關聯性
* 設定端點
* 管理[路由](concepts-route-events.md)
* 透過角色型存取控制（RBAC）設定[安全性](concepts-security.md)

## <a name="uses-deploy-and-validate"></a>使用（部署和驗證）

除了一般管理您的實例之外，CLI 也是用於部署和驗證的公用程式。
* 您可以使用 [控制平面] 命令，讓新實例的部署得以重複或自動化。
* 資料平面命令可以用來快速檢查實例中的值，並確認作業如預期般完成。

## <a name="get-the-extension"></a>取得延伸模組

Azure 數位 Twins 命令是[適用于 Azure CLI 的 Azure IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能的一部分。 您可以在命令集中查看這些命令的參考檔 `az iot` ： [az dt](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)。

您可以使用下列步驟，確定您有最新版本的延伸模組。 您可以在[Azure Cloud Shell](../cloud-shell/overview.md)或[本機 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)中執行這些命令。

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>後續步驟

如需 CLI 命令的替代方法，請參閱如何使用 Api 和 Sdk 管理 Azure 數位 Twins 實例：
* [*作法：使用 Azure 數位 Twins Api 和 Sdk*](how-to-use-apis-sdks.md)
