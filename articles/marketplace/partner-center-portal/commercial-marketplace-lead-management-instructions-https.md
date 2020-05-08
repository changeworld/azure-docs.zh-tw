---
title: 使用 HTTPS 端點進行潛在客戶管理-Microsoft 商業 marketplace
description: 瞭解如何使用電源自動化和 HTTPS 端點來管理來自 Microsoft AppSource 和 Azure Marketplace 的潛在客戶。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7a4fc57b3be8dd59997ef2bfc9624892cf726160
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790978"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>使用 HTTPS 端點來管理商業 marketplace 潛在客戶

如果合作夥伴中心未明確支援您的客戶關係管理（CRM）系統來接收 Microsoft AppSource 和 Azure Marketplace 潛在客戶，您可以使用[電源自動化](https://powerapps.microsoft.com/automate-processes/)中的 HTTPS 端點來處理這些潛在客戶。 有了 HTTPS 端點，商業 marketplace 潛在客戶就可以電子郵件通知的形式送出，或者可以將它們寫入支援電源自動化的 CRM 系統。

本文說明如何在電源自動化中建立新的流程，以產生您將用來在合作夥伴中心設定潛在客戶的 HTTP POST URL。 它也包含使用[Postman](https://www.getpostman.com/downloads/)測試流程的步驟。

>[!NOTE]
>這些指示中使用的「電源自動化」連接器需要付費的訂用帳戶，才能自動進行電源自動化。 設定此流程之前，請確定您已考慮此情況。

## <a name="create-a-flow-by-using-power-automate"></a>使用電源自動化建立流程

1. 開啟 [[自動啟動](https://flow.microsoft.com/)網頁]。 選取 [登入]  。 如果您還沒有帳戶，請選取 [**免費註冊**] 以建立免費的電源自動化帳戶。

1. 登入，然後選取功能表中的 [**我的流程**]。

    ![登入我的流程](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. 在 [ **+ 新增**] 底下，選取 [ **+ 立即-從空白**]。

    ![我的流程 + 自動化--從空白](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. 為您的流程命名，然後在 **[選擇觸發此流程的方式**] 底下，選取 [**收到 HTTP 要求時**]。

    ![建立自動化流程視窗 [略過] 按鈕](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. 按一下 [流程] 步驟，將它展開。

    ![展開流程步驟](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. 使用下列其中一種方法來設定**要求主體 JSON 架構**：

    - 將 JSON 架構複製到 [**要求本文 Json 架構**] 文字方塊中。
    - 選取 [使用範例承載來產生結構描述]****。 在 [**輸入或貼上範例 json**承載] 文字方塊中，貼上 json 範例。 選取 [完成]**** 以建立結構描述。

    **JSON 結構描述**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON 範例**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>此時在設定中，您可以選擇 [連線到 CRM 系統] 或 [設定電子郵件通知]。 根據您的選擇，遵循其餘的指示。

### <a name="connect-to-a-crm-system"></a>連接到 CRM 系統

1. 選取 [+ 新步驟] ****。
1. 在 [**搜尋連接器] 和 [動作**] 中搜尋，以選擇您選擇的 CRM 系統。 在 [**動作**] 索引標籤上選取它，並提供動作以建立新的記錄。 下列畫面顯示**建立新記錄（Dynamics 365）** 做為範例。

    ![建立新的記錄](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. 提供與 CRM 系統相關聯的**組織名稱**。 從 [**機構名稱**] 下拉式清單中選取 [**潛在客戶**]。

    ![選取潛在客戶](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. [電源自動化] 顯示提供潛在客戶資訊的表單。 您可以選擇新增動態內容來對應輸入要求中的項目。 下列畫面顯示**OfferTitle**範例。

    ![新增動態內容](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. 對應您想要的欄位，然後選取 [**儲存**] 來儲存您的流程。 HTTP POST URL 已建立，並可在 [**收到 HTTP 要求時**] 視窗中存取。 使用複製控制項（位於 HTTP POST URL 右邊）來複製此 URL。 使用複製控制項很重要，如此一來，您就不會錯過整個 URL 的任何部分。 儲存此 URL，因為當您在發佈入口網站中設定潛在客戶管理時，將會需要它。

    ![收到 HTTP 要求時](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>設定電子郵件通知

1. 既然您已完成 JSON 架構，請選取 [ **+ 新增步驟**]。
1. 在 [選擇動作]**** 底下，選取 [動作]****。
1. 在 [**動作**] 索引標籤上，選取 [**傳送電子郵件（Office 365 Outlook）**]。

    >[!NOTE]
    >如果您想要使用不同的電子郵件提供者，請搜尋並選取 [**傳送電子郵件通知（郵件）** ] 做為動作。

    ![新增電子郵件動作](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. 在 [**傳送電子郵件**] 視窗中，設定下列必要欄位：

   - **目標**：輸入至少一個有效的電子郵件地址，將會在其中傳送潛在客戶。
   - 主旨 **：電源**自動化提供您新增動態內容的選項，如下列畫面所示的**LeadSource** 。 從輸入功能變數名稱開始。 然後從快顯視窗中選取 [動態內容挑選清單]。 

        >[!NOTE] 
        > 當您新增功能變數名稱時，可以在每個名稱後面加上冒號（:)然後選取**Enter**以建立新的資料列。 加入功能變數名稱之後，您就可以從動態挑選清單新增每個相關聯的參數。

        ![使用動態內容新增電子郵件動作](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **主體**：從動態內容挑選清單中，在電子郵件的本文中新增您想要的資訊。 例如，使用 LastName、FirstName、Email 和 Company。 當您完成設定電子郵件通知時，它看起來會像下列畫面中的範例。


       ![電子郵件通知範例](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. 選取 [儲存]**** 完成流程。 HTTP POST URL 已建立，並可在 [**收到 HTTP 要求時**] 視窗中存取。 使用複製控制項（位於 HTTP POST URL 右邊）來複製此 URL。 使用此控制項很重要，因為您不會錯過整個 URL 的任何部分。 儲存此 URL，因為當您在發佈入口網站中設定潛在客戶管理時，將會需要它。

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>測試

您可以使用[Postman](https://app.getpostman.com/app/download/win64)來測試您的設定。 線上下載 Postman 適用于 Windows。 

1. 啟動 Postman，然後選取 [**新增** > **要求**] 以設定您的測試控管。 

   ![設定測試控管的要求](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. 填寫 [**儲存要求**] 表單，然後儲存至您所建立的資料夾。

   ![儲存要求表單](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. 從下拉式清單中選取 [ **POST** ]。 

   ![測試我的流程](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. 從您在電源自動化中建立的流程貼上 HTTP POST URL，其中會顯示 [**輸入要求 URL**]。

   ![貼上 HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. 回到 [[電源自動化](https://flow.microsoft.com/)]。 從 [電源自動化] 功能表列前往 [我的**流程**]，尋找您所建立的流程來傳送潛在客戶。 選取流程名稱旁的省略號以查看更多選項，然後選取 [**編輯**]。


1. 選取右上角的 [**測試**]，選取 [**我將執行觸發程式動作**]，然後選取 [**測試**]。 您會在畫面頂端看到測試已啟動的指示。

   ![我要執行觸發程式動作選項](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. 返回您的 Postman 應用程式，然後選取 [**傳送**]。

   ![傳送按鈕](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. 返回您的流程並檢查結果。 如果所有專案都如預期般運作，您會看到一則訊息，指出流程已成功。

   ![檢查結果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. 您也應該會收到一封電子郵件。 檢查您的電子郵件收件匣。 

    >[!NOTE] 
    >如果您沒有看到來自測試的電子郵件，請檢查您的垃圾郵件及垃圾郵件資料夾。 在下列畫面中，您會注意到設定電子郵件通知時所新增的欄位標籤。 如果這是從您的供應專案產生的實際潛在客戶，您也會在本文和主旨行中看到潛在客戶連絡人的實際資訊。

   ![收到的電子郵件](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>設定您的供應項目，以便將潛在客戶傳送至 HTTPS 端點

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時，請遵循下列步驟。

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 選取您的供應專案，然後移至 [**供應專案設定**] 索引標籤。

1. 在 [**潛在客戶管理**] 區段下，選取 **[連線]**。 
    ![潛在客戶管理連接按鈕](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

1. 在 [連線**詳細資料**] 快顯視窗中，選取 [ **HTTPS 端點**] 作為 [**潛在客戶目的地**]。 將您先前步驟所建立的流程中的 HTTP POST URL 貼入 [ **HTTPS 端點 URL** ] 欄位。
    ![連接詳細資料連絡人電子郵件](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. 在 [**連絡人電子郵件**] 底下，輸入公司人員在收到新的潛在客戶時應接收電子郵件通知的電子郵件地址。 您可以提供多個電子郵件，方法是以分號分隔。

1. 選取 [確定]  。

若要確定您已成功連接到潛在客戶目的地，請選取 [**驗證**] 按鈕。 如果成功，您會在潛在客戶目的地中擁有測試潛在客戶。

>[!NOTE] 
>您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。

當產生潛在客戶時，Microsoft 會將潛在客戶傳送至流程。 潛在客戶會路由傳送至您所設定的 CRM 系統或電子郵件地址。
