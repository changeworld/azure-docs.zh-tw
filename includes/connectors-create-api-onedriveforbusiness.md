---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3a0cb48e7fe5f3eef101f644e4f72fcfa2689d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789440"
---
## <a name="prerequisites"></a>Prerequisites

* [OneDrive](https://OneDrive.com) 帳戶 

您必須先授與邏輯應用程式連接到 OneDrive for Business 帳戶的權限，之後才能在邏輯應用程式中使用您的 OneDrive for Business 帳戶。 所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。 

若要授與邏輯應用程式連接到 OneDrive for Business 帳戶的權限，其步驟如下：

1. 若要建立商務用 OneDrive 連線，請在邏輯應用程式設計工具中，選取下拉式清單的 [顯示 Microsoft 受控 API]****，然後在搜尋方塊中輸入「商務用 OneDrive」**。 選取您要使用的觸發程序或動作：  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. 如果您之前尚未建立任何 OneDrive for Business 連線，系統會提示您提供 OneDrive for Business 認證。 這些認證會用來授與邏輯應用程式連接並存取商務用 OneDrive 帳戶資料的權限：  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. 提供您的商務用 OneDrive 使用者名稱和密碼以授與邏輯應用程式權限：  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

