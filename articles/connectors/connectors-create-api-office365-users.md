---
title: 連接到 Office 365 使用者
description: 使用 Azure Logic Apps，將在 Office 365 使用者設定檔中取得及管理設定檔的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666851"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 在 Office 365 使用者中取得及管理設定檔

連接至 Office 365 使用者，以取得設定檔、搜尋使用者等等。 您可以利用 Office 365 使用者來：

* 根據您從 Office 365 使用者所取得的資料，來建置您的商務流程。 
* 使用可取得直屬員工、取得管理員的使用者設定檔等等的動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 例如，取得某人的直屬員工，然後利用此資訊更新 SQL Azure 資料庫。 

您現在可以開始建立邏輯應用程式，請參閱[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-office-365-users"></a>建立至 Office 365 使用者的連線

當您將此連接器新增至邏輯應用程式時，您必須登入您的 Office 365 使用者帳戶，Azure Logic Apps 才能連線到您的帳戶。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

連線建立之後，您需要輸入 Office 365 使用者屬性，像是使用者識別碼。 本文的 **REST API 參考**一節說明這些屬性。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需有關觸發程式、動作和限制的技術詳細資訊（由連接器的 Swagger 描述所描述），請參閱[連接器的參考頁面](/connectors/officeusers/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](apis-list.md)