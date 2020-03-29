---
title: 從 Azure 邏輯應用連接到銷售人員
description: 使用 Azure Logic Apps 將監視、建立和管理 Salesforce 記錄與作業的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789285"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 來監視、建立及管理 Salesforce 資源

使用 Azure Logic Apps 和 Salesforce 連接器，您即可為 Salesforce 資源 (例如記錄、作業和物件) 建立自動化的工作和工作流程，例如：

* 監視所建立或變更的記錄。 
* 建立、取得和管理作業與記錄，包括插入、更新及刪除動作。

您可以使用 Salesforce 觸發程序，以取得 Salesforce 的回應，並且讓輸出可供其他動作使用。 您可以在邏輯應用程式中使用動作，對 Salesforce 資源執行工作。 如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* [Salesforce 帳戶](https://salesforce.com/)

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 Salesforce 帳戶的邏輯應用程式。 若要開始使用 Salesforce 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Salesforce 動作，請使用其他觸發程序來啟動邏輯應用程式，例如「週期」**** 觸發程序。

## <a name="connect-to-salesforce"></a>連線至 Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 選擇路徑： 

   * 針對空白邏輯應用程式，請在搜尋方塊中輸入 "salesforce" 作為篩選條件。 
   在觸發程序清單底下，選取您想要的觸發程序。 

     -或-

   * 若是現有的邏輯應用程式，請在想要新增動作的步驟底下，選擇 [新增步驟]****。 在搜尋方塊中，輸入「salesforce」作為篩選條件。 在動作清單底下，選取您想要的動作。

1. 如果系統提示您登入 Salesforce，請立即登入並允許存取。

   認證會授權邏輯應用程式對 Salesforce 建立連線，並存取資料。

1. 為您選取的觸發程序或動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="connector-reference"></a>連接器參考

如需觸發程序、動作和限制的技術詳細資訊，它們是由連接器的 OpenAPI (以前稱為 Swagger) 來描述，請檢閱連接器的[參考頁面](/connectors/salesforce/)。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)