---
title: 使用分頁取得更多專案或記錄
description: 在 Azure Logic Apps 中設定分頁，使其超過連接器動作的預設頁面大小限制
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792118"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>在 Azure Logic Apps 中使用分頁來取得更多資料、專案或記錄

當您使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)中的連接器動作來抓取資料、專案或記錄時，您可能會得到結果集很大，以致動作不會同時傳回所有結果。 在某些動作中，結果數目可能會超過連接器的預設頁面大小。 在此情況下，動作只會傳回結果的第一頁。 例如，SQL Server 連接器的 [**取得資料列**] 動作的預設頁面大小為2048，但可能會根據其他設定而有所不同。

某些動作可讓您開啟*分頁*設定，讓邏輯應用程式可以抓取更多的結果，直到分頁限制，但動作完成時，會以單一訊息的形式傳回這些結果。 當您使用分頁時，您必須指定*臨界*值，這是您想要讓動作傳回的目標結果數目。 動作會抓取結果，直到達到您指定的閾值。 當您的專案總數小於指定的臨界值時，此動作會抓取所有結果。

開啟分頁設定會根據連接器的頁面大小來抓取結果頁面。 這種行為表示，有時候您可能會得到比指定的閾值更多的結果。 例如，當使用支援分頁設定的 SQL Server**取得資料列**] 動作時：

* 動作的預設頁面大小為每頁2048筆記錄。
* 假設您有10000筆記錄，並指定5000筆記錄做為最小值。
* 分頁會取得記錄的頁面，因此若要取得至少指定的最小值，動作會傳回6144筆記錄（3頁 x 2048 筆記錄），而不是5000筆記錄。

以下是只有一些連接器的清單，您可以在其中超過特定動作的預設頁面大小：

* [Azure Blob 儲存體](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle 資料庫](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 邏輯應用程式，以及您要開啟分頁的動作。 如果您沒有邏輯應用程式，請參閱[快速入門：建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>開啟分頁

若要判斷某個動作是否支援邏輯應用程式設計工具中的分頁，請檢查該動作的設定是否為 [**分頁**] 設定。 這個範例示範如何在 SQL Server 的 [**取得資料列**] 動作中開啟分頁。

1. 在動作的右上角，選擇省略號（**...**）按鈕，然後選取 [**設定**]。

   ![開啟動作的 [設定]](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果動作支援分頁，此動作會顯示**分頁**設定。

1. 將**分頁**設定從 [**關閉**] 變更為 [**開啟**]。 在 [**閾值**] 屬性中，為您想要讓動作傳回的目標結果指定一個整數值。

   ![指定要傳回的結果數目下限](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 準備就緒時，請選擇 [完成]****。

## <a name="workflow-definition---pagination"></a>工作流程定義-分頁

當您開啟支援這項功能的動作分頁時，邏輯應用程式的工作流程定義會在`"paginationPolicy"`該動作的`"runtimeConfiguration"`屬性`"minimumItemCount"`中包含屬性以及屬性，例如：

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

如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
