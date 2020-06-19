---
title: 從 Azure Logic Apps 連線至 Yammer
description: 使用 Azure Logic Apps，將在 Yammer 中監視、貼文及管理訊息、摘要等等的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 3023aa72d713dc25351a6e509319e1c4c0ed609a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829659"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>藉由使用 Azure Logic Apps 來監視及管理您的 Yammer 帳戶

您可以使用 Azure Logic Apps 和 Yammer 連接器，建立自動化的工作和工作流程，以便在 Yammer 帳戶中監視及管理訊息、摘要等等，還有其他動作，例如：

* 監視新訊息何時出現在追蹤的摘要和群組中。
* 取得訊息、群組、網路、使用者的詳細資訊等等。
* 貼文以及對訊息按讚。

您可以使用觸發程序，從您的 Yammer 帳戶收到回應，並且讓輸出可供其他動作使用。 您可以使用動作，該動作會使用您的 Yammer 帳戶來執行工作。 您也可以讓其他動作使用 Yammer 動作的輸出。 例如，當新訊息出現在摘要或群組中時，您可以使用 Slack 連接器來共用這些訊息。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您的 Yammer 帳戶和使用者認證

   您的認證會授權邏輯應用程式建立連線並存取 Yammer 帳戶。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 Yammer 帳戶的邏輯應用程式。 若要開始使用 Yammer 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Yammer 動作，請使用其他觸發程序來啟動邏輯應用程式，例如「週期」觸發程序。

## <a name="connect-to-yammer"></a>連線至 Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 選擇路徑： 

   * 針對空白邏輯應用程式，請在搜尋方塊中輸入 "yammer" 作為篩選條件。 
   在觸發程序清單底下，選取您想要的觸發程序。 

     -或-

   * 針對現有的邏輯應用程式： 
   
     * 請在想要新增 SMTP 動作的最後一個步驟底下，選擇 [新增步驟]。 

       -或-

     * 請在想要新增動作的步驟之間，將指標移至步驟之間的箭號。 
     選擇顯示的加號 ( **+** )，然後選取 [新增動作]。
     
       在搜尋方塊中，輸入 "yammer" 作為篩選條件。 
       在動作清單底下，選取您想要的動作。

1. 如果系統提示您登入 Yammer，請立即登入以便允許存取。

1. 為您選取的觸發程序或動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="connector-reference"></a>連接器參考

如需觸發程序、動作和限制的技術詳細資訊，它們是由連接器的 OpenAPI (以前稱為 Swagger) 來描述，請檢閱連接器的[參考頁面](/connectors/yammer/)。

## <a name="get-support"></a>取得支援

* 如有任何問題，請造訪 [Microsoft 的 Azure Logic Apps 問與答頁面](https://docs.microsoft.com/answers/topics/azure-logic-apps.html)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)