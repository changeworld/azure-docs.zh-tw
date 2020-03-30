---
title: 連接到 Facebook
description: 使用 Azure 邏輯應用自動執行管理 Facebook 時間表和頁面的任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665797"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用管理 Facebook 時間表和頁面

連線到 Facebook 並張貼在動態時報上、取得頁面摘要等等。 您可以利用 Facebook 來：

* 根據您從 Facebook 所取得的資料，來建置您的商務流程。 
* 在接收到新貼文時使用觸發程序。
* 使用會張貼到您的動態時報、取得頁面摘要等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 舉例來說，當您的動態時報上有新貼文時，您可以取得該貼文，然後把它推送到您的 Twitter 摘要。 

現在可以通過創建邏輯應用開始，請參閱[創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-facebook"></a>建立至 Facebook 的連線

當您將這個連接器新增到邏輯應用程式時，您必須授權邏輯應用程式，使其能夠連線到您的 Facebook。

1. 登錄您的 Facebook 帳戶。

2. 選取 [授權] ****，然後允許您的邏輯應用程式連線並使用您的 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>連接器參考

有關連接器的 OpenAPI（以前是斯瓦格）檔所述的技術詳細資訊（如觸發器、操作和限制），請參閱[連接器的參考頁](/connectors/facebook/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)