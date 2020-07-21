---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524197"
---
## <a name="prerequisites"></a>必要條件

* Azure 帳戶；您可以建立一個 [免費帳戶](https://azure.microsoft.com/free)
* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 帳戶 

您必須先授權邏輯應用程式連線到您的 OneDrive 帳戶，才能在該邏輯應用程式中使用您的 OneDrive 帳戶。  您可以在 Azure 入口網站上，從邏輯應用程式內輕鬆完成此操作。 

使用以下步驟授權邏輯應用程式連接到 OneDrive 帳戶的權限：

1. 建立邏輯應用程式。 在 Logic Apps 設計工具中，從下拉式清單中選取 [顯示 Microsoft 受控 API]****，然後在搜尋方塊中輸入 "onedrive"。 選取其中一個觸發程序或動作︰  
   ![標題為「顯示 Microsoft 受控 Api」的對話方塊具有包含「onedrive」的 [搜尋] 方塊。 以下是四個觸發程式的清單。 清單中的第一個是「OneDrive-當檔案建立時」，這是已選取的選項。](./media/connectors-create-api-onedrive/onedrive-1.png)
2. 如果您之前尚未對 OneDrive 建立任何連線，系統會提示您使用 OneDrive 認證來登入：  
   ![標題為「OneDrive-當檔案建立時」的對話方塊有一個標示為 [登入] 的按鈕。](./media/connectors-create-api-onedrive/onedrive-2.png)
3. 選取 [登入]****，然後輸入您的使用者名稱和密碼。 選取 [登**入**]：  
   ![標題為 [登入] 的對話方塊會指示您「使用您的 Microsoft 帳戶」。 它有兩個標示為「電子郵件或電話」和「密碼」的文字方塊，也有一個標示為「讓我保持登入」的核取方塊，以及一個標示為「登入」的按鈕。](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    這些認證會用來授權邏輯應用程式連線到您的 OneDrive 帳戶，以及存取該帳戶中的資料。 
4. 選取 [是]**** 以授權邏輯應用程式使用您的 OneDrive 帳戶︰  
   ![標題為「讓此應用程式存取您的資訊？」的對話方塊 要求執行下列四項動作的許可權：1）「自動登入」、2）「存取您的電子郵件地址」、3）「隨時存取您的資訊」，以及4）「存取 OneDrive 檔案」。 有一個 [是] 按鈕可授與許可權，而 [否] 按鈕則拒絕它。 有一個連結可變更這些應用程式許可權。](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. 請注意，已建立連線。 現在，請繼續進行您邏輯應用程式中的其他步驟：  
   ![標題為「建立檔案時」的對話方塊有一個標題為「資料夾」的文字方塊，其中有相關聯的瀏覽按鈕。](./media/connectors-create-api-onedrive/onedrive-5.png)

