---
title: 利用分頁取得更多項目或記錄
description: 在 Azure Logic Apps 中設定分頁，以超過連接器動作的預設頁面大小限制
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090258"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用分頁來取得更多資料、項目或記錄

當使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 中的連接器動作來擷取資料、項目或記錄時，取得的結果集可能很大，而導致動作無法同時傳回所有結果。 使用某些動作時，結果數目可能會超過連接器的預設頁面大小。 在此情況下，動作只會傳回結果的第一頁。 例如，SQL Server 連接器 [取得資料列] 動作的預設頁面大小為 2048，但可能會根據其他設定而有所不同。

某些動作可供開啟「分頁」設定，讓邏輯應用程式可擷取更多結果直到分頁限制，但在動作完成時，會以單一訊息傳回這些結果。 當使用分頁時，則必須指定「閾值」，這是您想要讓動作傳回的目標結果數目。 動作會擷取結果，直到達到指定的閾值。 當項目總數小於指定的閾值時，動作會擷取所有結果。

開啟分頁設定會根據連接器的頁面大小來擷取結果頁面。 此行為表示，有時候您可能會取得比所指定閾值更多的結果。 例如，使用 SQL Server 的 [取得資料列] 動作來支援分頁設定時：

* 動作的預設頁面大小為每頁 2048 筆記錄。
* 假設您有 10,000 筆記錄，並指定 5000 筆記錄作為下限。
* 分頁會取得記錄頁面，因此為了至少達到指定的下限，動作會傳回 6144 筆記錄 (3 頁 x 2048 筆記錄)，而不是 5000 筆記錄。

以下列出可超過特定動作預設頁面大小的其中一些連接器：

* [Azure Blob 儲存體](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Oracle 資料庫](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 邏輯應用程式，以及想要開啟分頁的動作。 如果沒有邏輯應用程式，請參閱[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>開啟分頁

若要判斷動作是否支援邏輯應用程式設計工具中的分頁，請檢查 [分頁] 設定的動作設定。 此範例示範如何在 SQL Server 的 [取得資料列] 動作中開啟分頁。

1. 在動作的右上角選擇省略符號 ( **...** ) 按鈕，然後選取 [設定]。

   ![開啟動作的設定](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果動作支援分頁，則動作會顯示 [分頁] 設定。

1. 將 [分頁] 設定從 [關閉] 變更為 [開啟]。 在 [閾值] 屬性中指定一個整數值，以表示想要讓動作傳回的目標結果數目。

   ![指定要傳回的結果數目下限](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 準備就緒時，請選擇 [完成]。

## <a name="workflow-definition---pagination"></a>工作流程定義 - 分頁

當為支援這項功能的動作開啟分頁時，邏輯應用程式的工作流程定義會在該動作的 `"runtimeConfiguration"` 屬性中包含 `"paginationPolicy"` 屬性及 `"minimumItemCount"` 屬性，例如：

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>取得支援

如有任何問題，請瀏覽 [Microsoft 問與答的 Azure Logic Apps 問題頁面](/answers/topics/azure-logic-apps.html)。
