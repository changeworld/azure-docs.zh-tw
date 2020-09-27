---
title: Google 連接器的資料安全性和隱私權原則
description: 瞭解 Google security 和隱私權原則對 Google 連接器（例如 Gmail）在 Azure Logic Apps 的影響
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400752"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則

自 **2020 年5月1日**起，由於 Google 的 [資料安全性和隱私權原則](https://www.blog.google/technology/safety-security/project-strobe/) 所做的變更，可能會影響使用 [Gmail 連接器](/connectors/gmail/)的邏輯應用程式工作流程。 如果您的邏輯應用程式搭配 Gmail 取用者帳戶使用 Gmail 連接器 (以或) 結尾的電子郵件地址 @gmail.com @googlemail.com ，您的邏輯應用程式只能使用特定 [的 Google 核准觸發程式、動作和連接器](#approved-connectors)。

> [!NOTE]
> 如果您的邏輯應用程式搭配使用 Gmail 連接器與 G Suite 商務帳戶 (電子郵件地址與自訂網域) ，您的邏輯應用程式不會受到影響，且不會限制使用 Gmail 連接器。

## <a name="affected-logic-apps"></a>受影響的邏輯應用程式

如果您有使用 Gmail 連接器的邏輯應用程式，您會收到一封有關可能受影響的邏輯應用程式的電子郵件。 不過，自 **2020 年6月 15**日起，將會停用任何不符合規範的工作流程。 您可以採取下列其中一種動作：

* [遵循本主題中的步驟](#update-affected-workflows)來更新受影響的邏輯應用程式。 您必須建立 Google 用戶端應用程式，以提供您在 Gmail 觸發程式或動作中用來驗證的用戶端識別碼和用戶端密碼。

* 更新受影響的邏輯應用程式，使其只在您重新啟用已停用的邏輯應用程式之前，使用 [Google 核准的連接器](#approved-connectors) 。

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google 核准的連接器

在此原則下，當您使用 Gmail 取用者帳戶時，您可以只搭配特定的 Google 核准服務使用 Gmail 連接器，這些服務可能會變更。 我們的工程團隊會繼續使用 Google 將更多服務新增至這份清單。 現在，當您使用 Gmail 取用者帳戶時，以下是您可以在與 Gmail 連接器相同的邏輯應用程式工作流程中使用的 Google 核准觸發程式、動作和連接器：

* Logic Apps 內建觸發程式和動作：批次、控制項、資料作業、日期時間、一般檔案、液體、要求、排程、變數和 XML

  未由 Google 核准的內建觸發程式和動作，例如 HTTP、Azure Functions、Azure Logic Apps 和其他專案，讓邏輯應用程式不符合 Gmail 連接器的規範，因為應用程式可以從任何地方傳送或接收資料。

* Google 服務： Gmail、Google Calendar、Google Contacts、Google Drive、Google sheet 和 Google 工作

* 核准的 Microsoft 服務： Dynamics 365、Excel Online、Microsoft 小組、Microsoft 365、OneDrive 和 SharePoint Online

* 適用于客戶管理的資料來源的連接器： FTP、RSS、SFTP、SMTP 和 SQL Server

## <a name="non-compliant-examples"></a>不符合規範的範例

以下是使用 Gmail 連接器的一些範例，其中包含不是由 Google 核准的內建觸發程式和動作或受管理的連接器：

* 此邏輯應用程式會使用 Gmail 連接器搭配 HTTP 內建觸發程式：

  ![不符合規範的邏輯應用程式-範例1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  邏輯應用程式也會使用已核准的 Google 行事曆連接器。

* 此邏輯應用程式會使用 Gmail 連接器搭配 Azure Blob 儲存體連接器：

  ![不符合規範的邏輯應用程式-範例2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* 此邏輯應用程式會使用 Gmail 連接器搭配 Twitter 連接器：

  ![不符合規範的邏輯應用程式-範例3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

如需最新資訊，請參閱 [Gmail 連接器的技術參考檔](/connectors/gmail/)。

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>受影響邏輯應用程式的步驟

如果您必須在邏輯應用程式中使用 gmail 連接器搭配 Gmail 取用者帳戶和 Google 未核准的連接器，您可以建立自己的 Google 應用程式，以便在企業中進行個人或內部用途。 在此案例中，以下是您必須採取的高階步驟：

1. 使用 [GOOGLE API 主控台](https://console.developers.google.com)建立 google 用戶端應用程式。

1. 在您的 Gmail 連接器中，使用來自 Google 用戶端應用程式的用戶端識別碼和用戶端秘密值。

如需詳細資訊，請參閱 [Gmail 連接器的技術參考檔](/connectors/gmail/#authentication-and-bring-your-own-application)。

### <a name="create-google-client-app"></a>建立 Google 用戶端應用程式

若要設定用戶端應用程式的專案，請使用 [GOOGLE API 主控台嚮導](https://console.developers.google.com/start/api?id=gmail&credential=client_key) ，並遵循指示進行。 或者，如需詳細步驟，請參閱 [Gmail 連接器技術參考檔](/connectors/gmail/#authentication-and-bring-your-own-application)中的指示。

當您完成時，您的畫面看起來會像這個範例，但您會有自己的 **用戶端識別碼** 和 **用戶端秘密** 值（稍後在邏輯應用程式中使用）。

![Google 用戶端應用程式的用戶端識別碼和用戶端密碼](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>在邏輯應用程式中使用用戶端應用程式設定

若要在 Gmail 觸發程式或動作中使用 Google 用戶端應用程式的用戶端識別碼和用戶端密碼，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 如果您要加入新的 Gmail 觸發程式或動作，並建立全新的連線，請繼續下一個步驟。 否則，請在 Gmail 觸發程式或動作中，選取 [**變更連接**  >  **新增**]，例如：

   ![選取 [變更連接] > [新增]](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. 提供您的連接資訊，例如：

   ![提供連線資訊](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **驗證類型** | **攜帶您自己的應用程式** | 指定您將使用自己的用戶端應用程式進行驗證。 |
   | **用戶端識別碼** | <*client-ID*> | 來自 Google 用戶端應用程式的用戶端識別碼 |
   | **用戶端祕密** | <*client-secret*> | 來自 Google 用戶端應用程式的用戶端密碼 |
   ||||

1. 當您完成時，請選取 [登 **入**]。

   隨即出現一個頁面，其中顯示您所建立的用戶端應用程式。 如果您使用 Gmail 取用者帳戶，您可能會看到一個頁面，其中顯示 Google 未驗證用戶端應用程式，並提示您先允許存取您的 Google 帳戶。

   ![提示您存取您的 Google 帳戶](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. 如有必要，請選取 [ **允許**]。

   您現在可以在邏輯應用程式中使用 Gmail 連接器，而不受限制。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Gmail 連接器](/connectors/gmail/)

