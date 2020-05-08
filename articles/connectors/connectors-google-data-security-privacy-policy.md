---
title: Google 連接器的資料安全性和隱私權原則
description: 瞭解 Google 安全性和隱私權原則對 Google 連接器的影響，例如 Gmail，Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 590ad6a52d768c7e59d8d97691e146205e43cadd
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628703"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則

自**2020 年5月1日**起，由於 Google 的[資料安全性和隱私權原則](https://www.blog.google/technology/safety-security/project-strobe/)所做的變更，可能會影響使用[Gmail 連接器](https://docs.microsoft.com/connectors/gmail/)的邏輯應用程式工作流程。 如果您的邏輯應用程式使用 Gmail 連接器搭配 Gmail 取用者帳戶（以@gmail.com或@googlemail.com結尾的電子郵件地址），則您的邏輯應用程式只能使用特定[的 Google 核准觸發程式、動作和連接器](#approved-connectors)。 

> [!NOTE]
> 如果您的邏輯應用程式搭配使用 Gmail 連接器與 G Suite 商務帳戶（具有自訂網域的電子郵件地址），則您的邏輯應用程式不會受到影響，且不會限制使用 Gmail 連接器。

## <a name="affected-logic-apps"></a>受影響的邏輯應用程式

如果您有使用 Gmail 連接器的邏輯應用程式，您會收到有關可能受到影響的邏輯應用程式的電子郵件。 不過，自**2020 年6月15日**起，將會停用任何不符合規範的工作流程。 您可以採取下列其中一個動作：

* [遵循本主題中的步驟](#update-affected-workflows)，更新受影響的邏輯應用程式。 您必須建立 Google 用戶端應用程式，以提供在 Gmail 觸發程式或動作中用來進行驗證的用戶端識別碼和用戶端密碼。

* 更新受影響的邏輯應用程式，使其僅使用[Google 核准的連接器](#approved-connectors)，然後再重新啟用已停用的邏輯應用程式。

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google 核准的連接器

在此原則下，當您使用 Gmail 取用者帳戶時，您可以使用 Gmail 連接器，只搭配特定的 Google 核准服務，這可能會有所變更。 我們的工程小組會繼續使用 Google 來新增更多服務到這份清單中。 目前，當您使用 Gmail 取用者帳戶時，以下是您可以在與 Gmail 連接器相同的邏輯應用程式工作流程中使用的 Google 核准觸發程式、動作和連接器：

* Logic Apps 內建觸發程式和動作：批次、控制項、資料作業、日期時間、一般檔案、液體、要求、排程、變數和 XML

* Google 服務： Gmail、Google 行事曆、Google 連絡人、Google 雲端硬碟、Google 試算表和 Google Tasks

* 核准的 Microsoft 服務： Dynamics 365、Excel Online、Microsoft 小組、Office 365、OneDrive 和 SharePoint Online

* 客戶管理的資料來源連接器： FTP、RSS、SFTP、SMTP 和 SQL Server

如需最新資訊，請參閱[Gmail 連接器的技術參考檔](https://docs.microsoft.com/connectors/gmail/)。

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>受影響邏輯應用程式的步驟

如果您必須在邏輯應用程式中搭配 Gmail 取用者帳戶和 Google 未核准連接器使用 Gmail 連接器，您可以在您的企業中建立個人或內部使用的 Google 應用程式。 在此案例中，以下是您需要採取的高階步驟：

1. 使用[GOOGLE API 主控台](https://console.developers.google.com)建立 google 用戶端應用程式。

1. 在 Gmail 連接器中，從您的 Google 用戶端應用程式使用用戶端識別碼和用戶端密碼值。

如需詳細資訊，請參閱[Gmail 連接器的技術參考檔](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。

### <a name="create-google-client-app"></a>建立 Google 用戶端應用程式

若要為您的用戶端應用程式設定專案，請使用[GOOGLE API 主控台 wizard](https://console.developers.google.com/start/api?id=gmail&credential=client_key) ，並遵循指示進行。 或者，如需詳細步驟，請參閱[Gmail 連接器的技術參考檔](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)中的指示。

當您完成時，畫面看起來就像這個範例，不同之處在于您會有自己的**用戶端識別碼**和**用戶端秘密**值，您稍後會在邏輯應用程式中使用它們。

![Google client 應用程式的用戶端識別碼和用戶端密碼](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>在邏輯應用程式中使用用戶端應用程式設定

若要從您的 Gmail 觸發程式或動作中的 Google 用戶端應用程式使用用戶端識別碼和用戶端密碼，請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 如果您要加入新的 Gmail 觸發程式或動作，並建立全新的連線，請繼續進行下一個步驟。 否則，請在 Gmail 觸發程式或動作中，選取 [**變更** > 連線] [**新增**]，例如：

   ![選取 [變更連接] > [加入新的]](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. 提供您的連接資訊，例如：

   ![提供連接資訊](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | 屬性 | 值 | 描述 |
   |----------|-------|-------------|
   | **驗證類型** | **攜帶您自己的應用程式** | 指定您將使用自己的用戶端應用程式進行驗證。 |
   | **用戶端識別碼** | <*用戶端識別碼*> | 來自 Google 用戶端應用程式的用戶端識別碼 |
   | **用戶端密碼** | <*用戶端密碼*> | 來自 Google 用戶端應用程式的用戶端密碼 |
   ||||

1. 當您完成時，請選取 [登**入**]。

   此時會出現一個頁面，顯示您所建立的用戶端應用程式。 如果您使用 Gmail 取用者帳戶，您可能會看到一個頁面，顯示您的用戶端應用程式未經過 Google 驗證，並會提示您先允許存取您的 Google 帳戶。

   ![提示存取您的 Google 帳戶](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. 如有需要，請選取 [**允許**]。

   您現在可以在邏輯應用程式中使用 Gmail 連接器，而不會有任何限制。

## <a name="next-steps"></a>後續步驟

深入瞭解[Gmail 連接器](https://docs.microsoft.com/connectors/gmail/)
