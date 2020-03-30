---
title: HTTPS 端點 |Azure 應用商店
description: 設定 HTTPS 端點的潛在客戶管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: dsindona
ms.openlocfilehash: cb6ef173e97a7c2bbd7d7cad5e5074b1f2d0f066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288592"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 端點設定潛在客戶管理

您可以使用 HTTPS 端點處理 Azure Marketplace 和 AppSource 潛在客戶。 這些潛在客戶可以寫入客戶關係管理 (CRM) 系統或以電子郵件通知形式傳送出去。 本文介紹如何使用[Microsoft Flow](https://powerapps.microsoft.com/automate-processes/)自動化服務配置潛在客戶管理。

## <a name="create-a-flow-using-microsoft-flow"></a>使用 Microsoft Flow 建立流程

1. 開啟 [Flow](https://flow.microsoft.com/) 網頁。 選取 [登入]**** 或選取 [免費註冊]**** 以建立免費的 Flow 帳戶。

2. 登入並選取功能表列上的 [我的流程]****。

    ![我的流程](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. 選取 [+從空白建立]****。

    ![從空白建立](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. 選取 [從空白建立]****。

    ![從空白建立](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. 在 [搜尋連接器與觸發程序]**** 欄位中，輸入「要求」以尋找要求連接器。
6. 在 [觸發程序]**** 底下，選取 [收到 HTTP 要求時]****。 

    ![選取 HTTP 要求接收觸發程序](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. 使用下列其中一個步驟來設定**要求本文 JSON 結構描述**：

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

為產品/服務配置潛在客戶管理資訊時，為**潛在客戶目標**選擇**HTTPS 終結點**，並粘貼到上一步中複製的 HTTP POST URL 中。  

![新增動態內容](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

產生潛在客戶時，Microsoft 會將潛在客戶傳送至 Flow；如此一來，這些資訊就會路由傳送至您所設定的 CRM 系統或電子郵件地址。

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

您可以複製和編輯下列 JSON 範例，作為 MS Flow 中的測試。

### <a name="json-example"></a>JSON 範例

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>後續步驟

如果您已經完成這個操作，在 Cloud Partner 入口網站中設定客戶[潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)。
