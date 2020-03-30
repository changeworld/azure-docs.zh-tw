---
title: 連接到框
description: 使用 Azure 邏輯應用自動執行在 Box 中創建和管理檔的任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666766"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用在 Box 中創建和管理檔

本文說明如何使用 Box 連接器，從邏輯應用程式內部在 Box 中建立和管理檔案。 這樣一來，您就可以建立邏輯應用程式，將用於管理檔案和進行其他動作的工作和工作流程自動化，例如：

* 根據您從 Box 所取得的資料，來建置您的商務流程。

* 在建立或更新檔案時觸發自動化的工作和工作流程。

* 運行複製檔或刪除檔的操作。

  當這些動作取得回應時，就能讓輸出可供其他動作使用。 
  舉例來說，當 Box 中的某個檔案變更時，您可以使用 Office 365 在電子郵件中傳送該檔案。

## <a name="prerequisites"></a>Prerequisites

* [Box 帳戶](https://www.box.com/home)

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您要在其中存取 Box 帳戶的邏輯應用程式。 若要使用 Box 觸發程序啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。
如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md)。

## <a name="connector-reference"></a>連接器參考

有關連接器的 OpenAPI（以前是斯瓦格）檔所述的技術詳細資訊（如觸發器、操作和限制），請參閱[連接器的參考頁](/connectors/box/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)