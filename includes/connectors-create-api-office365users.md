---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789508"
---
## <a name="prerequisites"></a>必要條件

* [Office 365 使用者](https://office365.com) 帳戶  

您必須先授權邏輯應用程式連接到您的 Office 365 使用者帳戶，才可以在邏輯應用程式中使用您的 Office 365 使用者帳戶。幸運的是，您可以輕鬆地在 Azure 入口網站上從邏輯應用程式內完成。  

若要授與邏輯應用程式連接到 Office 365 使用者帳戶的權限，其步驟如下：  

1. 若要建立 Office 365 使用者連接，請在邏輯應用程式設計工具的下拉式清單中選取 [顯示 Microsoft 受控 API]****，然後在搜尋方塊中輸入「Office 365 使用者」**。 選取您要使用的觸發程序或動作：  
   ![Office 365 使用者的建立步驟](./media/connectors-create-api-office365users/office365users-1.png)  
2. 如果您之前尚未建立任何 Office 365 使用者連接，系統會提示您提供 Office 365 使用者認證。 這些認證會用來授與邏輯應用程式連接並存取 Office 365 使用者帳戶資料的權限：  
   ![Office 365 使用者的建立步驟](./media/connectors-create-api-office365users/office365users-2.png)  
3. 提供您 Office 365 使用者的使用者名稱和密碼以授與邏輯應用程式權限：  
   ![Office 365 使用者的建立步驟](./media/connectors-create-api-office365users/office365users-3.png)  
4. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：  
   ![Office 365 使用者的建立步驟](./media/connectors-create-api-office365users/office365users-4.png)  

