---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102523"
---
## <a name="prerequisites"></a>Prerequisites

* [商務用 OneDrive](https://OneDrive.com)帳戶 

您必須先授權 Logic Apps 連接到 Azure 入口網站中的商務用 onedrive 帳戶，才能搭配 Logic Apps 使用商務用 OneDrive 帳戶。

遵循下列步驟以授權 Logic Apps 連接到您的商務用 OneDrive 帳戶：  

1. 登入 Azure 入口網站。 

1. 在 [ **Azure 服務**] 下，選取 [ **Logic Apps**]。 然後，從清單中選取邏輯應用程式的名稱。

1. 在邏輯應用程式的功能表上，選取 [**開發工具**] 下的 [**邏輯應用程式設計**工具]。

1. 在 Logic Apps 設計工具中，從下拉式清單中選取 [ **顯示 Microsoft 受控 api** ]，然後在 [搜尋] 方塊中輸入 *商務用 OneDrive* 。 選取要使用的觸發程式或動作：  

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示使用商務用 OneDrive API 動作的週期觸發程式。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. 如果您之前尚未建立任何與商務用 OneDrive 的連線，請遵循提示來提供您的商務用 OneDrive 認證。 這些認證會用來授權邏輯應用程式存取您的商務用 OneDrive 帳戶資料：  

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示商務用 OneDrive 的登入提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. 提供您的商務用 OneDrive 使用者名稱和密碼來授權邏輯應用程式：  

   ![[商務用 OneDrive] 登入頁面的螢幕擷取畫面，其中顯示登入提示。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. 此連接現在會列在步驟中。 選取 [儲存]，然後繼續建立邏輯應用程式。 

   ![Logic Apps 設計工具的螢幕擷取畫面，其中列出列出商務用 OneDrive 連接的觸發程式。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
