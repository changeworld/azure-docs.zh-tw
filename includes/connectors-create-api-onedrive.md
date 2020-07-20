---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789684"
---
## <a name="prerequisites"></a>必要條件

* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帳戶 

您必須先授權邏輯應用程式連線到您的 OneDrive 帳戶，才能在該邏輯應用程式中使用您的 OneDrive 帳戶。  您可以在 Azure 入口網站上，從邏輯應用程式內輕鬆完成此操作。 

使用以下步驟授權邏輯應用程式連接到 OneDrive 帳戶的權限：

1. 建立邏輯應用程式。 在 Logic Apps 設計工具中，從下拉式清單中選取 [顯示 Microsoft 受控 API]****，然後在搜尋方塊中輸入 "onedrive"。 選取其中一個觸發程序或動作︰  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 如果您之前尚未對 OneDrive 建立任何連線，系統會提示您使用 OneDrive 認證來登入：  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. 選取 [登入]****，然後輸入您的使用者名稱和密碼。 選取 [登**入**]：  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    這些認證會用來授權邏輯應用程式連線到您的 OneDrive 帳戶，以及存取該帳戶中的資料。 
4. 選取 [是]**** 以授權邏輯應用程式使用您的 OneDrive 帳戶︰  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 請注意，已建立連線。 現在，請繼續進行您邏輯應用程式中的其他步驟：  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

