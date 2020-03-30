---
title: 從 Azure 邏輯應用連接到 SMTP
description: 藉由使用 Azure Logic Apps，讓透過 SMTP (簡易郵件傳輸通訊協定) 帳戶傳送電子郵件的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 60acd128495176cd0a90418c61edf53bdcd88e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647586"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>使用 Azure Logic Apps 從您的 SMTP 帳戶傳送電子郵件

您可以使用 Azure Logic Apps 和簡易郵件傳輸通訊協定 (SMTP) 連接器，建立自動化的工作和工作流程，以便從您的 SMTP 帳戶傳送電子郵件。 您也可以讓其他動作使用 SMTP 動作的輸出。 例如，在您的 SMTP 傳送電子郵件之後，您可以使用 Slack 連接器，在 Slack 中通知您的小組。 如果您是邏輯應用的新增功能，請查看什麼是[Azure 邏輯應用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您的 SMTP 帳戶和使用者認證

  您的認證會授權邏輯應用程式建立連線並存取 SMTP 帳戶。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 SMTP 帳戶的邏輯應用程式。 若要使用 SMTP 動作，請使用觸發程序來啟動邏輯應用程式，例如 Salesforce 觸發程序 (如果您有 Salesforce 帳戶)。

  例如，您可以使用「記錄建立時機」**** Salesforce 觸發程序來啟動邏輯應用程式。 
  此觸發程序會在每次於 Salesforce 中建立新記錄 (例如潛在客戶) 時引發。 
  然後，您可以使用 SMTP [傳送電子郵件]**** 動作，來追蹤此觸發程序。 這樣一來，在建立新記錄時，邏輯應用程式會從您的 SMTP 帳戶，傳送與新記錄相關的電子郵件。

## <a name="connect-to-smtp"></a>連接到 SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 請在想要新增 SMTP 動作的最後一個步驟底下，選擇 [新增步驟]****。 

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 
   選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 "smtp" 作為篩選條件。 在動作清單底下，選取您想要的動作。

1. 出現提示時，請提供這項連線資訊：

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **連接名稱** | 是 | 與 SMTP 伺服器的連線名稱 | 
   | **SMTP 伺服器位址** | 是 | SMTP 伺服器的位址 | 
   | **使用者名** | 是 | SMTP 帳戶的使用者名稱 | 
   | **密碼** | 是 | SMTP 帳戶的密碼 | 
   | **SMTP 伺服器連接埠** | 否 | SMTP 伺服器上您想要使用的特定連接埠 | 
   | **啟用 SSL？** | 否 | 開啟或關閉 SSL 加密。 | 
   |||| 

1. 為您選取的動作提供必要的詳細資料。 

1. 儲存您的邏輯應用程式，或繼續建置邏輯應用程式的工作流程。

## <a name="connector-reference"></a>連接器參考

有關此連接器的更多技術詳細資訊，例如連接器的 Swagger 檔所述的觸發器、操作和限制，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/smtpconnector/)。

> [!NOTE]
> 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)