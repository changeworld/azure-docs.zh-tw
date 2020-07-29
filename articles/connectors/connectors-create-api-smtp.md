---
title: 從 Azure Logic Apps 連接到 SMTP
description: 藉由使用 Azure Logic Apps，讓透過 SMTP (簡易郵件傳輸通訊協定) 帳戶傳送電子郵件的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283940"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>使用 Azure Logic Apps 從您的 SMTP 帳戶傳送電子郵件

您可以使用 Azure Logic Apps 和簡易郵件傳輸通訊協定 (SMTP) 連接器，建立自動化的工作和工作流程，以便從您的 SMTP 帳戶傳送電子郵件。 您也可以讓其他動作使用 SMTP 動作的輸出。 例如，在您的 SMTP 傳送電子郵件之後，您可以使用 Slack 連接器，在 Slack 中通知您的小組。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您的 SMTP 帳戶和使用者認證

  您的認證會授權邏輯應用程式建立連線並存取 SMTP 帳戶。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 SMTP 帳戶的邏輯應用程式。 若要使用 SMTP 動作，請使用觸發程序來啟動邏輯應用程式，例如 Salesforce 觸發程序 (如果您有 Salesforce 帳戶)。

  例如，您可以使用「記錄建立時機」Salesforce 觸發程序來啟動邏輯應用程式。 
  此觸發程序會在每次於 Salesforce 中建立新記錄 (例如潛在客戶) 時引發。 
  然後，您可以使用 SMTP [傳送電子郵件]**** 動作，來追蹤此觸發程序。 這樣一來，在建立新記錄時，邏輯應用程式會從您的 SMTP 帳戶，傳送與新記錄相關的電子郵件。

## <a name="connect-to-smtp"></a>連接到 SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 請在想要新增 SMTP 動作的最後一個步驟底下，選擇 [新增步驟]****。 

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 
   選擇顯示的加號 ( **+** )，然後選取 [新增動作]。

1. 在搜尋方塊中，輸入 "smtp" 作為篩選條件。 在動作清單底下，選取您想要的動作。

1. 出現提示時，請提供這項連線資訊：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **連接名稱** | 是 | 與 SMTP 伺服器的連線名稱 | 
   | **SMTP 伺服器位址** | 是 | SMTP 伺服器的位址 | 
   | **使用者名稱** | 是 | SMTP 帳戶的使用者名稱 | 
   | **密碼** | 是 | SMTP 帳戶的密碼 | 
   | **SMTP 伺服器連接埠** | 否 | SMTP 伺服器上您想要使用的特定連接埠 | 
   | **要啟用 SSL 嗎？** | 否 | 開啟或關閉 TLS/SSL 加密。 | 
   |||| 

1. 為您選取的動作提供必要的詳細資料。 

1. 儲存您的邏輯應用程式，或繼續建置邏輯應用程式的工作流程。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，例如連接器的 Swagger 檔案所描述的觸發程式、動作和限制，請參閱[連接器的參考頁面](/connectors/smtpconnector/)。

> [!NOTE]
> 對於[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式，此連接器的 ise 標記版本會使用[ISE 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
