---
title: 使用 HTTPS 終結點設定潛在顧客管理 |Azure 應用商店
description: 瞭解如何使用 HTTP 終結點來處理 Microsoft AppSource 和 Azure 應用商店潛在顧客。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770205"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 端點設定潛在客戶管理

您可以使用 HTTPS 終結點來處理 Microsoft AppSource 和 Azure 應用商店潛在顧客。 這些潛在顧客可以寫入客戶關係管理 (CRM) 系統,也可以作為電子郵件通知發送。 本文介紹如何使用[Microsoft 電源自動化](https://powerapps.microsoft.com/automate-processes/)自動化服務來配置潛在顧客管理。

## <a name="create-a-flow-using-microsoft-power-automate"></a>使用微軟電源自動功能建立串流

1. 打開[電源自動功能](https://flow.microsoft.com/)網頁。 選取 [登入]**** 或選取 [免費註冊]**** 以建立免費的 Flow 帳戶。

1. 登入並選取功能表列上的 [我的流程]****。
    > [!div class="mx-imgBorder"]
    > ![我的流程](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. 在 **( 新增**) 選擇 =**即時的名稱 。**
    > [!div class="mx-imgBorder"]
    > ![從空白建立](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. 命名您的流,然後在 **「選擇如何觸發此流**」下,選擇 **「何時收到 HTTP 請求**」。。

    > [!div class="mx-imgBorder"]
    > ![選取 HTTP 要求接收觸發程序](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. 單擊流步驟以展開它。

    > [!div class="mx-imgBorder"]
    > ![展開流步驟](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. 使用以下方法之一設定**要求正文 JSON 架構**:

   - 將此文章結尾的 [JSON 結構描述](#json-schema)複製到**要求本文 JSON 結構描述**文字方塊。
   - 選取 [使用範例承載來產生結構描述]****。 在 [輸入或貼上範例 JSON 承載]**** 文字方塊中，貼上 [JSON 範例](#json-example)。 選取 [完成]**** 以建立結構描述。

   >[!Note]
   >此時在流程中您可以連線到 CRM 系統或設定電子郵件通知。

### <a name="to-connect-to-a-crm-system"></a>若要連線到 CRM 系統

1. 選取 [+ 新步驟] ****。
2. 利用建立新記錄的動作，選擇 CRM 系統。 以下螢幕擷取畫面會顯示 [Dynamics 365 - 建立新記錄]**** 作為範例。

    ![建立新的記錄](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. 提供連接器連線輸入的**組織名稱**。 從 [實體名稱]**** 下拉式清單選取 [潛在客戶]****。

    ![選取潛在客戶](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow 會顯示一個表單來提供潛在客戶資訊。 您可以選擇新增動態內容來對應輸入要求中的項目。 下列螢幕擷取畫面顯示 **OfferTitle** 作為範例。

    ![新增動態內容](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. 對應您想要的欄位，然後選取 [儲存]****，儲存您的流程。

6. 在要求中便會建立一個 HTTP POST URL。 複製這個 URL，並使用它作為 HTTPS 端點。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>若要設定電子郵件通知

1. 選取 [+ 新步驟] ****。
2. 在 [選擇動作]**** 底下，選取 [動作]****。
3. 在 [動作]**** 之下，選取 [傳送電子郵件]****。

    ![新增電子郵件動作](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. 在 [傳送電子郵件]**** 中，設定下列必要欄位：

   - **收件人** - 輸入至少一個有效的電子郵件地址。
   - **主旨** - Flow 可讓您選擇新增動態內容，像是以下螢幕擷取畫面中的 **LeadSource**。

     ![使用動態內容新增電子郵件動作](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **本文** - 從動態內容清單中，新增您在電子郵件本文中想要的資訊。 例如，LastName、FirstName、Email 和 Company。

   當您完成設定電子郵件通知時，它看起來會類似以下螢幕擷取畫面中的範例。

   ![新增電子郵件動作](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. 選取 [儲存]**** 完成流程。

6. 在要求中便會建立一個 HTTP POST URL。 複製這個 URL，並使用它作為 HTTPS 端點。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>設定您的供應項目，以便將潛在客戶傳送至 HTTPS 端點

為產品/服務配置潛在顧客管理資訊時,為**潛在顧客目標**選擇**HTTPS 終結點**,並貼上一步中複製的 HTTP POST URL 中。  

![新增動態內容](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

生成潛在顧客時,Microsoft 會將潛在顧客發送到您的 Power 自動流,該流將路由到您配置的 CRM 系統或電子郵寄地址。

## <a name="json-schema-and-example"></a>JSON 結構描述和範例

JSON 測試範例會使用下列結構描述：

### <a name="json-schema"></a>JSON 結構描述

``` json
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
    "Description": {
      "id": "/properties/Description",
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

您可以複製和編輯以下 JSON 範例,以用作流中的測試。

### <a name="json-example"></a>JSON 範例

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

## <a name="next-steps"></a>後續步驟

如果您已經完成這個操作，在 Cloud Partner 入口網站中設定客戶[潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)。
