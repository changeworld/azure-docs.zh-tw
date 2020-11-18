---
title: 與 Office 365 Outlook 整合
description: 使用 Azure Logic Apps 將在 Office 365 Outlook 中管理電子郵件、連絡人和行事曆的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 9caf69a7f78c7872f0a5f8a2ed07bdc749a29023
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682990"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 在 Office 365 Outlook 中管理電子郵件、連絡人和行事曆

您可以使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和 [Office 365 Outlook 連接器](/connectors/office365connector/)，建立自動化的工作和工作流程，以藉由建立邏輯應用程式來管理您的工作或學校帳戶。 例如，您可以將這些工作自動化：

* 取得、傳送和回復電子郵件。
* 在行事曆上排程會議。
* 新增及編輯連絡人。

您可以使用任何觸發程式來啟動工作流程，例如，當新的電子郵件送達時、行事曆專案更新時，或在不同服務（例如 Salesforce）中發生事件時。 您可以使用回應觸發程式事件的動作，例如傳送電子郵件或建立新的行事曆事件。

## <a name="prerequisites"></a>先決條件

* 您用來登入 [公司或學校帳戶](https://www.office.com/)的 Outlook 帳戶。 如果您有 @outlook.com 或 @hotmail.com 帳戶，請改用 [Outlook.com 連接器](../connectors/connectors-create-api-outlook.md) 。 若要使用不同的使用者帳戶（例如服務帳戶）連接到 Outlook，請參閱 [使用其他帳戶連接](#connect-using-other-accounts)。

* Azure 帳戶和訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 您要在其中存取 Outlook 帳戶的邏輯應用程式。 若要使用 Office 365 Outlook 觸發程式來啟動工作流程，您需要有 [空白的邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要將 Office 365 Outlook 動作新增至您的工作流程，您的邏輯應用程式必須已經有觸發程式。

## <a name="add-a-trigger"></a>新增觸發程序

[觸發](../logic-apps/logic-apps-overview.md#logic-app-concepts)程式是在邏輯應用程式中啟動工作流程的事件。 此範例邏輯應用程式會使用「輪詢」觸發程式，根據指定的間隔和頻率，檢查電子郵件帳戶中是否有任何更新的行事曆事件。

1. 在 [Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在搜尋方塊中，輸入 `office 365 outlook` 作為篩選條件。 此範例 **會選取即將開始的活動即將開始**。
   
   ![選取觸發程式來啟動邏輯應用程式](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 如果您的 Outlook 帳戶沒有使用中的連線，系統會提示您登入並建立該連線。 若要使用不同的使用者帳戶（例如服務帳戶）連接到 Outlook，請參閱 [使用其他帳戶連接](#connect-using-other-accounts)。 否則，請提供觸發程式屬性的資訊。

   > [!NOTE]
   > 除非您變更登入認證，否則您的連線不會在撤銷前過期。 如需詳細資訊，請參閱 [Azure Active Directory 中可設定的權杖存留期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)。

   此範例會選取觸發程式檢查的行事曆，例如：

   ![設定觸發程式的屬性](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 在觸發程式中，設定 **頻率** 和 **間隔** 值。 若要加入其他可用的觸發程式屬性（例如 **時區**），請從 [ **加入新的參數** ] 清單中選取這些屬性。

   例如，如果您想要讓觸發程式每隔15分鐘檢查行事曆，請將 **頻率** 設定為 [ **分鐘**]，並將 [ **間隔** ] 設定為 `15` 。 

   ![設定觸發程式的頻率和間隔](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 在設計工具的工具列上，選取 [儲存]  。

現在新增會在觸發程式引發之後執行的動作。 例如，您可以新增 Twilio **傳送訊息** 動作，這會在行事曆事件于15分鐘內開始時傳送文字。

## <a name="add-an-action"></a>新增動作

[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是由邏輯應用程式中的工作流程所執行的作業。 此範例邏輯應用程式會在 Office 365 Outlook 中建立新的連絡人。 您可以使用另一個觸發程式或動作的輸出來建立連絡人。 例如，假設您的邏輯應用程式 **在建立記錄時** 使用 Dynamics 365 觸發程式。 您可以新增 Office 365 Outlook **建立連絡人** 動作，並使用 SalesForce 觸發程式的輸出來建立新的連絡人。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 若要將動作新增為工作流程中的最後一個步驟，請選取 [ **新增步驟**]。 

   若要在步驟之間新增動作，請將指標移至這些步驟之間的箭頭上。 選擇出現的加號 ( **+** )，然後選取 [新增動作]。

1. 在搜尋方塊中，輸入 `office 365 outlook` 作為篩選條件。 此範例會選取 [ **建立連絡人**]。

   ![選取要在邏輯應用程式中執行的動作](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 如果您的 Outlook 帳戶沒有使用中的連線，系統會提示您登入並建立該連線。 若要使用不同的使用者帳戶（例如服務帳戶）連接到 Outlook，請參閱 [使用其他帳戶連接](#connect-using-other-accounts)。 否則，請提供動作屬性的資訊。

   > [!NOTE]
   > 除非您變更登入認證，否則您的連線不會在撤銷前過期。 如需詳細資訊，請參閱 [Azure Active Directory 中可設定的權杖存留期](../active-directory/develop/active-directory-configurable-token-lifetimes.md)。

   此範例會選取動作建立新連絡人的 [連絡人] 資料夾，例如：

   ![設定動作的屬性](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   若要加入其他可用的動作屬性，請從 [ **加入新的參數** ] 清單中選取這些屬性。

1. 在設計工具的工具列上，選取 [儲存]  。

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>使用其他帳戶連接

如果您嘗試使用與目前登入 Azure 的帳戶不同的帳戶來連接到 Outlook，您可能會收到 [單一登入 (SSO) ](../active-directory/manage-apps/what-is-single-sign-on.md) 錯誤。 當您使用一個帳戶登入 Azure 入口網站，但使用不同的帳戶來建立連線時，就會發生此問題。 邏輯應用程式設計工具預期會使用已登入 Azure 的帳戶。 若要解決此問題，您有下列選項：

* 將其他帳戶設定為邏輯應用程式資源群組的 **參與者** 。

  1. 在邏輯應用程式的 [資源群組] 功能表上，選取 [ **存取控制] (IAM)**。 使用 **參與者** 角色設定另一個帳戶。 如需詳細資訊，請參閱[使用 Azure 入口網站新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md)。

  1. 如果您使用公司或學校帳戶登入 Azure 入口網站，請登出，然後使用您的其他帳戶重新登入。 您現在可以使用其他帳戶來建立與 Outlook 的連接。

* 設定其他帳戶，讓您的工作或學校帳戶具有「傳送為」許可權。

   如果您有系統管理員許可權，請在服務帳戶的信箱上，使用 [ **傳送** 身分] 或 [ **傳送] 代表** 許可權設定您的工作或學校帳戶。 如需詳細資訊，請參閱 [將信箱許可權授與另一個使用者-系統管理員](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user)說明。 然後，您可以使用您的工作或學校帳戶來建立連接。 現在，在您可以指定傳送者的觸發程式或動作中，您可以使用服務帳戶的電子郵件地址。

   例如，「 **傳送電子郵件** 」動作有一個選擇性參數， **從 (傳送為)**，您可以將它新增至動作，並使用服務帳戶的電子郵件地址做為寄件者。 若要新增此參數，請遵循下列步驟：

   1. 在 [ **傳送電子郵件** ] 動作中，開啟 [ **加入參數** ] 清單，然後選取 [ **從 (傳送])** 參數。

   1. 在動作上出現參數之後，輸入服務帳戶的電子郵件地址。

## <a name="connector-reference"></a>連接器參考

如需此連接器的相關技術詳細資料（例如觸發程式、動作和限制）（如連接器的 Swagger 檔案所述），請參閱 [連接器的參考頁面](/connectors/office365/)。 

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)