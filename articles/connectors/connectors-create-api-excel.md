---
title: 在 Excel 連線中管理資料、工作表和表
description: 使用 Azure 邏輯應用管理 Excel 連線業務或 OneDrive Excel 連線中的工作表和表中的資料
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445874"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>使用 Azure Logic Apps 來管理 Excel Online 資料

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和["Excel 連線業務"](/connectors/excelonlinebusiness/)連接器或[OneDrive](/connectors/excelonline/) Excel 連線連接器，可以基於"Excel 線上業務"或 OneDrive 中的資料創建自動任務和工作流。 此連接器所提供的動作，可協助您處理資料和管理試算表，例如：

* 建立新的工作表和資料表。
* 取得及管理工作表、資料表和資料列。
* 新增單一資料列和索引鍵資料行。

然後，您可以搭配其他服務的動作，使用這些動作的輸出。 例如，如果您使用會每週建立工作表的動作，則可以使用其他動作，藉由使用 Office 365 Outlook 連接器，來傳送確認電子郵件。

如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [商務用 Excel Online](/connectors/excelonlinebusiness/) 和[適用於 OneDrive 的 Excel Online](/connectors/excelonline/) 連接器可以與 Azure Logic Apps 搭配運作，而且與[適用於 PowerApps 的 Excel 連接器](/connectors/excel/)不同。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 適用於您的工作帳戶或個人 Microsoft 帳戶的 [Office 365 帳戶](https://www.office.com/)

  您的 Excel 資料可以是儲存體資料夾中 (例如在 OneDrive 中) 的逗號分隔值 (CSV) 檔案。 
  您也可以使用此相同的 CSV 檔案，與[一般檔案連接器](../logic-apps/logic-apps-enterprise-integration-flatfile.md)搭配使用。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 Excel Online 資料的邏輯應用程式。 此連接器只提供動作，因此若要啟動邏輯應用程式，請選取個別觸發程序，例如**週期**觸發程序。

## <a name="add-excel-action"></a>新增 Excel 動作

1. 在 [Azure 入口網站](https://portal.azure.com)中，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在觸發程序下方，選擇 [新增步驟]****。

1. 在搜尋方塊中，輸入 "excel" 作為篩選條件。 在動作清單底下，選取您想要的動作。

   > [!NOTE]
   > 邏輯應用設計器無法載入具有 100 列或更多列的表。 如果可能，請減少所選表中的列數，以便設計器可以載入表。

1. 如果出現提示，請登錄到 Office 365 帳戶。

   您的認證會授權邏輯應用程式建立與 Excel Online 的連線，並存取資料。

1. 為選取的動作提供必要的詳細資料，並且建置邏輯應用程式的工作流程。

## <a name="connector-reference"></a>連接器參考

有關連接器的 OpenAPI（以前是斯瓦格）檔所述的技術詳細資訊（如觸發器、操作和限制），請參閱以下連接器參考頁：

* [商務用 Excel Online](/connectors/excelonlinebusiness/)
* [適用於 OneDrive 的 Excel Online](/connectors/excelonline/)

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
