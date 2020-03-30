---
title: 在存儲庫中管理 IoT 隨插即用預覽模型*微軟文檔
description: 如何使用適用于 IoT 門戶的 Azure 認證、Azure CLI 和視覺化工作室代碼管理存儲庫中的裝置功能模型。
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159144"
---
# <a name="manage-models-in-the-repository"></a>管理存儲庫中的模型

IoT 隨插即用預覽模型存儲庫存放裝置功能模型和介面。 存儲庫使模型和介面可由解決方案開發人員發現和消耗。

有三種工具可用於管理存儲庫：

- 為 IoT 門戶認證的 Azure
- Azure CLI
- Visual Studio Code

## <a name="model-repositories"></a>模型存儲庫

有兩種類型的模型存儲庫用於存放裝置功能模型和介面：

- 在[Azure 認證的 IoT 設備目錄中](https://aka.ms/iotdevcat)，有一個_公共存儲庫_存放裝置功能模型和設備的介面。 此存儲庫還存儲由 Microsoft 合作夥伴發佈的[通用介面](./concepts-common-interfaces.md)[、DCM 和介面](./howto-onboard-portal.md)。 要瞭解如何認證設備並將其裝置功能模型添加到公共存儲庫，請參閱教程["認證 IoT 隨插即用裝置](./tutorial-certification-test.md)"。
- 有多個_公司存儲庫_。 當您[登上 Azure 認證的 IoT 門戶時，](./howto-onboard-portal.md)將自動為您的組織創建公司存儲庫。 您可以在開發和測試期間使用公司存儲庫來存放裝置功能模型和介面。

## <a name="azure-certified-for-iot-portal"></a>為 IoT 門戶認證的 Azure

在[IoT](https://preview.catalog.azureiotsolutions.com)的 Azure 認證門戶中，可以完成以下任務：

- [完成 IoT 設備的認證過程](./tutorial-certification-test.md)。
- 查找 IoT 隨插即用裝置功能模型。 您可以使用這些模型[快速構建物聯網就緒設備，並將其與解決方案集成](./quickstart-connect-pnp-device-solution-node.md)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 提供用於管理 IoT 隨插即用公共和公司模型存儲庫中的裝置功能模型和介面的命令。 有關詳細資訊，請參閱 Azure [CLI 的"安裝和使用 Azure IoT 擴展](./howto-install-pnp-cli.md)"指南。

## <a name="visual-studio-code"></a>Visual Studio Code

在視覺化工作室代碼中打開**模型存儲庫**視圖。

1. 打開視覺化工作室代碼，使用**Ctrl_Shift_P**，鍵入並選擇**IoT 隨插即用：打開模型存儲庫**。

1. 您可以選擇**打開公共模型存儲庫**或**打開組織模型存儲庫**。 對於公司模型存儲庫，您需要輸入模型存儲庫連接字串。 您可以使用 [Azure IoT 認證入口網站](https://preview.catalog.azureiotsolutions.com)，在**公司存放庫**的 [連接字串]**** 索引標籤上中找到此連接字串。

1. 新選項卡將打開 **"模型存儲庫"** 視圖。

    使用此視圖可以添加、下載和刪除裝置功能模型和介面。 您可以使用篩選器查找清單中的特定項。

1. 要在公司模型存儲庫和公共模型存儲庫之間切換，請使用**Ctrl_Shift_P，** 鍵入並選擇**IoT 隨插即用：登出模型存儲庫**。 然後再次使用**IoT 隨插即用：打開模型存儲庫**命令。

> [!NOTE]
> 在 VS 代碼中，公共模型存儲庫是唯讀的。 Microsoft 合作夥伴可以在 Azure 認證[IoT 門戶](https://preview.catalog.azureiotsolutions.com)中更新公共存儲庫。

## <a name="next-steps"></a>後續步驟

建議的下一步是學習如何[提交物聯網隨插即用裝置進行認證](tutorial-certification-test.md)。
