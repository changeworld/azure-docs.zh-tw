---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789633"
---
## <a name="prerequisites"></a>必要條件

* [MailChimp](https://www.MailChimp.com/)帳戶 

您必須先授與邏輯應用程式連接到 MailChimp 帳戶的權限，之後才能在邏輯應用程式中使用您的 MailChimp 帳戶。 所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。 

若要授與邏輯應用程式連接到 MailChimp 帳戶的權限，其步驟如下：

1. 若要建立 MailChimp 連線，請在邏輯應用程式設計工具中，選取下拉式清單的 [顯示 Microsoft 受控 API]****，然後在搜尋方塊中輸入「MailChimp」**。 選取您要使用的觸發程序或動作：  
   ![MailChimp 步驟 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. 如果您之前尚未建立任何 MailChimp 連線，系統會提示您提供 MailChimp 認證。 這些認證會用來授與邏輯應用程式連接並存取 MailChimp 帳戶資料的權限：  
   ![MailChimp 步驟 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. 提供您的 MailChimp 使用者名稱和密碼以授與邏輯應用程式權限：  
   ![MailChimp 步驟 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：  
   ![MailChimp 步驟 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)

