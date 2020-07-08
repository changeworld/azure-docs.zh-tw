---
title: 部署 & 管理 Azure 容器實例
description: 使用 Azure Logic Apps，將在 Azure 容器實例中建立和管理容器部署的工作和工作流程自動化
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76046288"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 部署和管理 Azure 容器實例

使用 Azure Logic Apps 和 Azure 容器實例連接器，您可以設定自動化的工作和工作流程，以部署和管理[容器群組](../container-instances/container-instances-container-groups.md)。 容器實例連接器支援下列動作：

* 建立或刪除容器群組
* 取得容器群組的屬性
* 取得容器群組的清單
* 取得容器實例的記錄

在您的邏輯應用程式中使用這些動作，執行容器工作負載以回應 Logic Apps 觸發程式之類的作業。 您也可以讓其他動作使用容器實例動作的輸出。 

此連接器只會提供動作，因此若要啟動邏輯應用程式，請使用個別的觸發程式，例如**定期**觸發程式，定期執行容器工作負載。 或者，您可能需要在事件（例如 Outlook 電子郵件送達）之後觸發容器群組部署。 

如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)，以及[如何建立和管理容器實例](../container-instances/container-instances-quickstart.md)的基本知識

* 您想要在其中存取容器實例的邏輯應用程式。 若要使用動作，請使用另一個觸發程式來啟動邏輯應用程式，例如**週期**觸發程式。

## <a name="add-a-container-instance-action"></a>新增容器實例動作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 選擇路徑： 

   * 請在想要新增 SMTP 動作的最後一個步驟底下，選擇 [新增步驟]。 

     -或-

   * 請在想要新增動作的步驟之間，將指標移至步驟之間的箭號。 
   選擇顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在搜尋方塊中，輸入「容器實例」作為篩選準則。 在 [動作] 清單底下，選取您想要的 Azure 容器實例連接器動作。

1. 為您的連線提供一個名稱。 

1. 為您選取的動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

  例如，選取 [**建立容器群組**] 並輸入容器群組的屬性，以及群組中的一個或多個容器實例，如下圖所示（部分詳細資料）：

  ![建立容器群組](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>連接器參考

如需有關觸發程式、動作和限制的技術詳細資料（由連接器的 OpenAPI （先前稱為 Swagger）描述所描述），請參閱連接器的[參考頁面](/connectors/aci/)或容器群組[YAML 參考](../container-instances/container-instances-reference-yaml.md)。

## <a name="next-steps"></a>後續步驟

* 請參閱在 Azure 容器實例中執行容器的[範例邏輯應用程式](https://github.com/Azure-Samples/aci-logicapps-integration)，以分析電子郵件或 Twitter 文字的情感

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)