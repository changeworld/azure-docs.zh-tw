---
title: 部署&管理 Azure 容器實例
description: 使用 Azure 邏輯應用自動執行在 Azure 容器實例中創建和管理容器部署的任務和工作流
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046288"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用部署和管理 Azure 容器實例

使用 Azure 邏輯應用和 Azure 容器實例連接器，可以設置部署和管理[容器組的](../container-instances/container-instances-container-groups.md)自動任務和工作流。 容器實例連接器支援以下操作：

* 創建或刪除容器組
* 獲取容器組的屬性
* 獲取容器組的清單
* 獲取容器實例的日誌

在邏輯應用中對回應邏輯應用觸發器運行容器工作負載等任務使用這些操作。 您還可以讓其他操作使用容器實例操作中的輸出。 

此連接器僅提供操作，因此要啟動邏輯應用，請使用單獨的觸發器，例如**定期**觸發器以按常規計畫運行容器工作負載。 或者，您可能需要在 Outlook 電子郵件到達等事件後觸發容器組部署。 

如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 有關如何[創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)以及如何[創建和管理容器實例](../container-instances/container-instances-quickstart.md)的基本知識

* 要訪問容器實例的邏輯應用。 要使用操作，請使用另一個觸發器啟動邏輯應用，例如 **"重複**"觸發器。

## <a name="add-a-container-instance-action"></a>添加容器實例操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 選擇路徑： 

   * 請在想要新增 SMTP 動作的最後一個步驟底下，選擇 [新增步驟]****。 

     -或-

   * 請在想要新增動作的步驟之間，將指標移至步驟之間的箭號。 
   選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜索框中，輸入"容器實例"作為篩選器。 在動作清單下，選擇所需的 Azure 容器實例連接器操作。

1. 為您的連線提供一個名稱。 

1. 為您選取的動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

  例如，選擇 **"創建容器組"** 並輸入容器組和組中的一個或多個容器實例的屬性，如下圖所示（部分詳細資訊）：

  ![建立容器群組](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>連接器參考

有關觸發器、操作和限制的技術詳細資訊（由連接器的 OpenAPI（以前的斯瓦格）描述，請查看連接器的[參考頁](/connectors/aci/)或容器組[YAML 引用](../container-instances/container-instances-reference-yaml.md)。

## <a name="next-steps"></a>後續步驟

* 查看在 Azure 容器實例中運行容器[的示例邏輯應用](https://github.com/Azure-Samples/aci-logicapps-integration)，以分析電子郵件或 Twitter 文本的情緒

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)