---
title: 使用分頁獲取更多專案或記錄
description: 將暫停設置為超過 Azure 邏輯應用中連接器操作的預設頁面大小限制
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792118"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>在 Azure 邏輯應用中使用分頁獲取更多資料、專案或記錄

在[Azure 邏輯應用中](../logic-apps/logic-apps-overview.md)使用連接器操作檢索資料、項或記錄時，可能會獲取的結果集太大，以至於操作不會同時返回所有結果。 通過某些操作，結果數可能會超過連接器的預設頁面大小。 在此情況下，動作只會傳回結果的第一頁。 例如，SQL Server 連接器的 **"獲取行"** 操作的預設頁大小為 2048，但可能因其他設置而異。

某些操作允許您打開*分頁*設置，以便邏輯應用可以檢索到 pagination 限制的更多結果，但在操作完成後將這些結果作為單個消息返回。 使用分頁時，必須指定*閾值*，即希望操作返回的目標結果數。 該操作檢索結果，直到達到指定的閾值。 當專案總數小於指定的閾值時，操作將檢索所有結果。

打開分頁設置會根據連接器的頁面大小檢索結果頁。 此行為意味著有時，您可能會獲得比指定閾值更多的結果。 例如，當使用 SQL Server**獲取行**操作時，該操作支援分頁設置：

* 操作的預設頁面大小為每頁 2048 條記錄。
* 假設您有 10，000 條記錄，並將 5000 條記錄指定為最小記錄。
* Pagination 獲取記錄頁，因此為了至少獲取指定的最小值，操作返回 6144 條記錄（3 頁 x 2048 條記錄），而不是 5000 條記錄。

下面是一個清單，其中只有一些連接器，您可以在其中超過特定操作的預設頁面大小：

* [Azure Blob 存儲](https://docs.microsoft.com/connectors/azureblob/)
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

* 邏輯應用和要打開分形的操作。 如果沒有邏輯應用，請參閱[快速入門：創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>打開分形

要確定操作是否支援邏輯應用設計器中的分頁，請檢查 **"分頁**"設置的操作設置。 此示例演示如何在 SQL Server 的 **"獲取行"** 操作中打開分頁。

1. 在操作的右上角，選擇橢圓 （**...）** 按鈕，然後選擇 **"設置**"。

   ![打開操作的設置](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果操作支援分形，則該操作將顯示 **"分頁"** 設置。

1. 將 **"暫停"** 設置從 **"打開"** 更改為"**打開**"。 在 **"閾值"** 屬性中，為希望操作返回的目標結果數指定整數值。

   ![指定要返回的最小結果數](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 準備就緒時，請選擇 [完成]****。

## <a name="workflow-definition---pagination"></a>工作流定義 - 分頁

當您為支援此功能的操作啟用分頁時，邏輯應用的工作流定義包括`"paginationPolicy"`該屬性以及該操作`"minimumItemCount"``"runtimeConfiguration"`屬性中的屬性，例如：

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
