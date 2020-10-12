---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87040249"
---
## <a name="prerequisites"></a>必要條件

* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帳戶 

您必須先授權 Logic Apps 連接到 Azure 入口網站中的 OneDrive 帳戶，才能搭配 Logic Apps 使用您的 OneDrive 帳戶。

遵循下列步驟以授權 Logic Apps 連接到您的 OneDrive 帳戶：  

1. 登入 Azure 入口網站。 

1. 在 [ **Azure 服務**] 下，選取 [ **Logic Apps**]。 然後，從清單中選取邏輯應用程式的名稱。

1. 在邏輯應用程式的功能表上，選取 [**開發工具**] 下的 [**邏輯應用程式設計**工具]。

1. 在 Logic Apps 設計工具中，從下拉式清單中選取 [ **顯示 Microsoft 受控 api** ]，然後在 [搜尋] 方塊中輸入 *OneDrive* 。 選取要使用的觸發程式或動作：

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示要新增的 OneDrive API 動作清單。](./media/connectors-create-api-onedrive/onedrive-1.png)

2. 如果您之前尚未建立任何 OneDrive 連線，請依照提示使用您的 OneDrive 認證登入：  

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示 OneDrive API 的登入提示。](./media/connectors-create-api-onedrive/onedrive-2.png)

3. 選取 [登入]****，然後輸入您的使用者名稱和密碼。 選取 [登 **入**]： 

   ![OneDrive API 授權 Microsoft 帳戶登入頁面的螢幕擷取畫面。](./media/connectors-create-api-onedrive/onedrive-3.png)   

    這些認證會用來授權邏輯應用程式存取 OneDrive 帳戶中的資料。 

4. 選取 [是]**** 以授權邏輯應用程式使用您的 OneDrive 帳戶︰  

   ![Logic Apps Microsoft 帳戶授權的螢幕擷取畫面，其中顯示允許的動作。](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. 此連接現在會列在步驟中。 選取 [儲存]，然後繼續建立邏輯應用程式。 

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示具有 OneDrive API 連接的動作編輯器。](./media/connectors-create-api-onedrive/onedrive-5.png)
