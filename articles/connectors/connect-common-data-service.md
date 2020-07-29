---
title: 連接到 Common Data Service
description: 使用 Azure Logic Apps 建立和管理 Common Data Service 記錄
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: 8cce90a8a65a7f070459e220e6d92ef0be57e909
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284110"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 建立和管理 Common Data Service 中的記錄

您可以使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和[Common Data Service 連接器](/connectors/commondataservice/)，建立自動化的工作流程，以管理[Common Data Service](/powerapps/maker/common-data-service/data-platform-intro)資料庫中的記錄。 這些工作流程可以建立記錄、更新記錄，以及執行其他作業。 您也可以從 Common Data Service 資料庫取得資訊，並讓輸出可供其他動作用於邏輯應用程式。 例如，當您在 Common Data Service 資料庫中更新記錄時，您可以使用 Office 365 Outlook 連接器傳送電子郵件。

本文說明如何建立邏輯應用程式，以在每次建立新的潛在客戶記錄時建立工作記錄。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [Common Data Service 環境](/power-platform/admin/environments-overview)，這是您的組織儲存、管理及共用商務資料和 Common Data Service 資料庫的空間。 如需詳細資訊，請參閱這些資源：<p>

  * [瞭解：開始使用 Common Data Service](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power 平臺-環境總覽](/power-platform/admin/environments-overview)

* 有關[如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)和邏輯應用程式的基本知識，而您想要在其中存取 Common Data Service 資料庫中的記錄。 若要使用 Common Data Service 觸發程式來啟動邏輯應用程式，您需要空白邏輯應用程式。 如果您不熟悉 Azure Logic Apps，請參閱[快速入門：使用 Azure Logic Apps 建立您的第一個工作流程](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-common-data-service-trigger"></a>新增 Common Data Service 觸發程式

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

在此範例中，新增在建立新記錄時所引發的 Common Data Service 觸發程式。

1. 在 [Azure 入口網站](https://portal.azure.com)中，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在搜尋方塊中，輸入 `common data service`。 在此範例中，請在 [觸發程序] 清單下選取此觸發程序：**建立記錄時**

   ![選取 [建立記錄時] 觸發程式](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. 若出現提示，請登入 Common Data Service。

1. 在觸發程式中，提供您想要監視新的「潛在客戶」記錄之環境的相關資訊，例如：

   ![要監視之環境的觸發資訊](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | 屬性 | 必要 | 說明 |
   |----------|----------|-------------|
   | **環境** | 是 | 要監視的環境，例如「Fabrikam 銷售生產」。 如需詳細資訊，請參閱[Power Platform-環境總覽](/power-platform/admin/environments-overview)。 |
   | **實體名稱** | 是 | 要監視的實體，例如「潛在客戶」 |
   | **範圍** | 是 | 建立新記錄的來源，例如您業務單位中的使用者或組織中的任何使用者。 這個範例會使用「業務單位」。 |
   ||||

## <a name="add-common-data-service-action"></a>新增 Common Data Service 動作

現在新增一個 Common Data Service 動作，為新的「潛在客戶」記錄建立工作記錄。

1. 在 [建立**記錄時**] 觸發程式下，選取 [**新增步驟**]。

1. 在搜尋方塊中，輸入 `common data service`。 從 [動作] 清單中，選取此動作：**建立新記錄**

   ![選取 [建立新記錄] 動作](./media/connect-common-data-service/select-create-new-record-action.png)

1. 在動作中，提供您要在其中建立新工作記錄之環境的相關資訊。 如果有的話，其他屬性也會根據您為此動作選取的實體顯示，例如：

   ![要在其中建立記錄之環境的動作資訊](./media/connect-common-data-service/create-new-record-action-details.png)

   | 屬性 | 必要 | 說明 |
   |----------|----------|-------------|
   | **組織名稱** | 是 | 您想要建立記錄的環境，在您的觸發程式中不一定要是相同的環境，但在此範例中為「Fabrikam 銷售生產」 |
   | **實體名稱** | 是 | 要在其中建立記錄的實體，例如「Tasks」 |
   | **主旨** | 是，根據此範例中選取的實體而定 | 這項工作的目標簡短描述 |
   ||||

   1. 在 [**主旨**] 屬性中，輸入此文字並加上尾端空格：

      `Follow up with new lead:`

   1. 將指標放在 [主旨] 方塊**內部，讓**動態內容清單保持可見。
   
   1. 在清單中，從 [**建立記錄時**] 區段中，選取您想要包含在工作記錄中的觸發程式輸出，例如：

      ![選取要在工作記錄中使用的觸發程式輸出](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | 觸發程式輸出 | 說明 |
      |----------------|-------------|
      | **First Name** | 潛在客戶記錄中用來做為工作記錄中主要連絡人的名字 |
      | **姓氏** | 要做為工作記錄中主要連絡人之潛在客戶記錄的姓氏 |
      | **說明** | 要包含在工作記錄中的其他輸出，例如電子郵件地址和公司電話號碼 |
      |||

   當您完成時，動作可能會如下列範例所示：

   ![已完成「建立新記錄」動作](./media/connect-common-data-service/finished-create-record-action-details.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]。

1. 若要手動啟動邏輯應用程式，請在設計工具工具列上，選取 [**執行**]。 若要測試您的邏輯應用程式，請建立新的「潛在客戶」記錄。

## <a name="trigger-only-on-updated-attributes"></a>只在更新的屬性上觸發

針對在更新記錄時執行的觸發程式，例如**當記錄更新動作時**，您可以使用篩選屬性，讓邏輯應用程式只有在更新指定的屬性時才會執行。 這項功能可協助您防止不必要的邏輯應用程式執行。

1. 在觸發程式的 [**加入新的參數**] 清單中，選取 [**屬性篩選**]。

   ![新增屬性篩選器屬性](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. 針對每個 [**屬性篩選] 專案**，選取您想要監視更新的屬性，例如：

   ![新增屬性篩選器屬性](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>根據篩選準則列出記錄

針對傳回記錄的動作（例如 [**清單記錄**] 動作），您可以使用 ODATA 查詢，根據指定的篩選準則傳回記錄。 例如，您的動作清單只會列出作用中帳戶的記錄。

1. 在動作中，開啟 [**加入新的參數**] 清單，然後選取 [**篩選查詢**] 屬性。

   ![加入 [篩選查詢] 屬性](./media/connect-common-data-service/list-records-action-filter-query.png)

1. 在 [**篩選查詢**] 屬性中，現在會出現在動作中，輸入此 ODATA 篩選查詢：`statuscode eq 1`

   ![輸入用於篩選記錄的 ODATA 篩選查詢](./media/connect-common-data-service/list-records-action-filter-query-value.png)

如需 `$filter` 系統查詢選項的詳細資訊，請參閱[Common Data Service 篩選結果](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)。

## <a name="list-records-based-on-an-order"></a>依據訂單列出記錄

針對傳回記錄的動作（例如 [**清單記錄**] 動作），您可以使用以指定順序傳回記錄的 ODATA 查詢，這會根據動作傳回的記錄而有所不同。 例如，您可以讓動作根據帳戶名稱列出記錄。

1. 在動作中，開啟 [**加入新的參數**] 清單，然後選取 [ **Order By** ] 屬性。

   ![新增 "Order by" 屬性](./media/connect-common-data-service/list-records-action-order-by.png)

1. 在現在出現于動作中的 [ **Order By** ] 屬性中，輸入此 ODATA 篩選查詢：`name`

   ![輸入用於排序記錄的 ODATA 篩選查詢](./media/connect-common-data-service/list-records-action-order-by-value.png)

如需 `$orderby` 系統查詢選項的詳細資訊，請參閱[Common Data Service 順序結果](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)。

## <a name="field-data-types"></a>欄位資料類型

無論您是手動輸入值，或從觸發程式或動作中的欄位動態內容清單中選取值，值的資料類型都必須符合欄位的必要資料類型。

下表描述一些欄位類型，以及這些欄位的值所需的資料類型。

| 欄位 | 資料類型 | 描述 |
|-------|-----------|-------------|
| 文字欄位 | 單行文字 | 需要一行文字或具有 text 資料類型的動態內容，例如下列屬性： <p><p>- **描述** <br>- **Category** |
| 整數位段 | 整數 | 需要整數或具有整數資料類型的動態內容，例如下列屬性： <p><p>- **完成百分比** <br>- **期限** |
| 日期欄位 | 日期及時間 | 需要以 MM/DD/YYY 格式的日期或具有 date 資料類型的動態內容，例如下列屬性： <p><p>- **建立于** <br>- **開始日期** <br>- **實際開始** <br>- **實際結束** <br>- **到期日** |
| 參考另一個實體記錄的欄位 | 主索引鍵 | 同時需要記錄識別碼（例如 GUID）和查閱類型，這表示動態內容清單中的值將無法使用，例如下列屬性： <p><p>- **擁有**者：必須是有效的使用者識別碼或小組記錄識別碼。 <br>- **擁有者類型**：必須分別是查閱類型 `systemusers` ，例如或 `teams` 。 <p><p>- **關於**：必須是有效的記錄識別碼，例如帳戶識別碼或連絡人記錄識別碼。 <br>- **關於類型**：必須分別是查閱類型，例如 `accounts` 或 `contacts` 。 <p><p>- **Customer**：必須是有效的記錄識別碼，例如帳戶識別碼或連絡人記錄識別碼。 <br>- **客戶類型**：必須是查閱類型，例如 `accounts` 或 `contacts` 。 |
||||

此範例顯示 [**建立新記錄**] 動作如何建立與其他實體記錄相關聯的新「工作」記錄，特別是使用者記錄和客戶紀錄。 動作會使用符合相關屬性之預期資料類型的值，來指定這些實體記錄的識別碼和查閱類型。

* 根據**擁有**者屬性（指定使用者識別碼）和**擁有者類型**屬性（指定查閱類型），此動作會將 `systemusers` 新的 "Tasks" 記錄與特定使用者產生關聯。

* 根據 [**關於**] 屬性（指定記錄識別碼）和 [**關於類型**] 屬性（指定 `accounts` 查閱類型），此動作會將新的 "Tasks" 記錄與特定帳戶產生關聯。

![建立與 Id 和查閱類型相關聯的「工作」記錄](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>連接器參考

如需以連接器的 Swagger 描述為基礎的技術資訊，例如觸發程式、動作、限制和其他詳細資料，請參閱[連接器的參考頁面](/connectors/commondataservice/)。

## <a name="next-steps"></a>後續步驟

* 了解 [Azure Logic Apps 的其他連接器](../connectors/apis-list.md)
