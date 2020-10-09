---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74789752"
---
1. 在 [Azure 入口網站](https://portal.azure.com) 中，建立空白的邏輯應用程式。 

2. 在 Logic Apps 設計工具中，輸入 "github" 作為篩選條件。 

3. 選取您想要使用的 GitHub 連接器和觸發程序。

   ![選取 GitHub 連接器和觸發程序](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > 所有邏輯應用程式皆必須以觸發程序啟動。 只有已使用觸發程序啟動邏輯工作流程時，您才選取動作。 

4. 如果您先前未建立連線，請選擇 [登入]****，如此一來您可以在出現提示時，提供您的 GitHub 認證。  

   ![利用您的 GitHub 認證登入](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   邏輯應用程式會使用這些認證來授權連線，並存取您 GitHub 帳戶的資料。 

5. 提供您的 GitHub 使用者名稱和密碼，然後確認您的授權。

   ![提供認證並確認授權](./media/connectors-create-api-github/github-connector-authorize.png)   

   您的連線現在已在 Azure 入口網站中建立並可供使用。

6. 繼續定義邏輯應用程式工作流程。

   ![將多個動作新增到您的邏輯應用程式工作流程](./media/connectors-create-api-github/github-connector-logic-app.png)

