---
title: 連接到 Facebook
description: 使用 Azure Logic Apps，將管理 Facebook 時間軸和頁面的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665797"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 管理您的 Facebook 時間軸和頁面

連線到 Facebook 並張貼在動態時報上、取得頁面摘要等等。 您可以利用 Facebook 來：

* 根據您從 Facebook 所取得的資料，來建置您的商務流程。 
* 在接收到新貼文時使用觸發程序。
* 使用會張貼到您的動態時報、取得頁面摘要等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當您的動態時報上有新貼文時，您可以取得該貼文，然後把它推送到您的 Twitter 摘要。 

您現在可以開始建立邏輯應用程式，請參閱[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-facebook"></a>建立至 Facebook 的連線

當您將這個連接器新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Facebook。

1. 登入您的 Facebook 帳戶。

2. 選取 [授權] ****，然後允許您的邏輯應用程式連線並使用您的 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>連接器參考

如需如連接器的 OpenAPI （先前為 Swagger）檔案所述的技術詳細資料（例如觸發程式、動作和限制），請參閱[連接器的參考頁面](/connectors/facebook/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)