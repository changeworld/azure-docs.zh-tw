---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102523"
---
## <a name="prerequisites"></a>必要條件

* [商務用 OneDrive](https://OneDrive.com)帳戶 

您必須先授權 Logic Apps 以在 Azure 入口網站中連線到您的商務用 OneDrive 帳戶，才可以使用商務用 OneDrive 帳戶搭配 Logic Apps。

請遵循下列步驟來授權 Logic Apps 連接到您的商務用 OneDrive 帳戶：  

1. 登入 Azure 入口網站。 

1. 在 [ **Azure 服務**] 底下，選取 [ **Logic Apps**]。 然後，從清單中選取邏輯應用程式的名稱。

1. 在邏輯應用程式的功能表上，選取 [**開發工具**] 底下的 [**邏輯應用程式設計**工具]。

1. 在 Logic Apps 設計工具中，從下拉式清單中選取 [**顯示 Microsoft 受控 api** ]，然後在搜尋方塊中輸入「*商務用 OneDrive* 」。 選取要使用的觸發程式或動作：  

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示具有商務用 OneDrive API 動作的週期觸發程式。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. 如果您之前尚未建立任何商務用 OneDrive 的連線，請依照提示來供應商務用 OneDrive 認證。 這些認證會用來授權邏輯應用程式存取您的商務用 OneDrive 帳戶的資料：  

   ![Logic Apps 設計工具的螢幕擷取畫面，顯示商務用 OneDrive 的登入提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. 提供您的商務用 OneDrive 使用者名稱和密碼，以授權您的邏輯應用程式：  

   ![[商務用 OneDrive] 登入頁面的螢幕擷取畫面，其中顯示 [登入提示]。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. 此連接現在會列在步驟中。 選取 [儲存]，然後繼續建立邏輯應用程式。 

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示已列出 [商務用 OneDrive] 連接的觸發程式。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
