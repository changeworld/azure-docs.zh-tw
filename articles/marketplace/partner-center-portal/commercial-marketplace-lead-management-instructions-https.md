---
title: 使用 HTTPS 的 Microsoft 商業 marketplace 潛在客戶管理
description: 設定 HTTPS 端點的 Microsoft 商業 marketplace 潛在客戶管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 1c3337e970fdbb22cb1ed88f105d5e7798a68f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133743"
---
# <a name="configure-lead-management-by-using-an-https-endpoint"></a>使用 HTTPS 端點來設定潛在客戶管理

>[!NOTE]
>這些指示中使用的「電源自動化」連接器需要付費的訂用帳戶，才能自動進行電源自動化。 依照本文中的指示進行之前，請確定您已考慮這一點。

如果合作夥伴中心未明確支援您的客戶關係管理（CRM）系統來接收 Microsoft AppSource 和 Azure Marketplace 潛在客戶，您可以使用電源自動化中的 HTTPS 端點來處理這些潛在客戶。 有了 HTTPS 端點，這些潛在客戶可以用電子郵件通知的形式送出，或者可以寫入支援電源自動化的 CRM 系統。 本文中的指示會逐步引導您完成基本程式，以使用電源自動化來建立新流程，這會產生您將在發佈入口網站中針對 [**潛在客戶管理** > ] [**HTTPS 端點 URL** ] 欄位輸入的 HTTP POST url。 另外也包含如何使用稱為[Postman](https://www.getpostman.com/downloads/)的工具來測試流程的指示（可從線上取得）。

## <a name="create-a-flow-by-using-power-automate"></a>使用電源自動化建立流程

1. 開啟 [[自動啟動](https://flow.microsoft.com/)網頁]。 選取 [登入]  。 如果您還沒有帳戶，請選取 [**免費註冊**] 以建立免費的電源自動化帳戶。

1. 登入，然後選取功能表中的 [**我的流程**]。

1. 選取 [ **+ 自動化--從空白**]。

    ![我的流程 + 自動化--從空白](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. 在 [**組建自動化流程**] 視窗中，選取 [**略過**]。 

    ![建立自動化流程視窗 [略過] 按鈕](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

1. 在 [**搜尋連接器和觸發**程式] 欄位中，輸入尋找要求連接器的**要求**。
1. 在 [觸發程序]**** 底下，選取 [收到 HTTP 要求時]****。 

    ![觸發程式功能表](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

1. 在 [**收到 HTTP 要求時**] 視窗中，將下列 JSON 架構複製並貼到 [**要求本文 json 架構**] 文字方塊中。 Microsoft 會使用此架構來包含您的潛在客戶資料。

    ![要求主體 JSON 架構文字方塊](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

您可以使用稱為[Postman](https://app.getpostman.com/app/download/win64)的工具（可在線上下載），測試所有專案是否如預期般運作。 此工具適用于 Windows。 

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

1. 移至供應專案的**供應專案設定**頁面。
1. 選取 [**潛在客戶管理**] 區段下的 **[連接]** 。
1. 在 [連線**詳細資料**] 快顯視窗中，選取 [ **HTTPS 端點**] 作為 [**潛在客戶目的地**]。 將您先前步驟所建立的流程中的 HTTP POST URL 貼入 [ **HTTPS 端點 URL** ] 欄位。
1. 在 [**連絡人電子郵件**] 底下，輸入公司人員在收到新的潛在客戶時應接收電子郵件通知的電子郵件地址。 您可以提供多個電子郵件，方法是以分號分隔。
1. 選取 [確定]  。

若要確定您已成功連接到潛在客戶目的地，請選取 [**驗證**] 按鈕。 如果成功，您會在潛在客戶目的地中擁有測試潛在客戶。

>[!NOTE] 
>您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。

當產生潛在客戶時，Microsoft 會將潛在客戶傳送至流程。 潛在客戶會路由傳送至您所設定的 CRM 系統或電子郵件地址。

![潛在客戶管理連接按鈕](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![連接詳細資料的潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![連接詳細資料連絡人電子郵件](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

