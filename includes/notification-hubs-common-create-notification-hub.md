---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095188"
---
### <a name="create-a-notification-hub"></a>建立通知中樞 

在本節中，您會建立通知中樞，並使用 **APNS** 設定驗證。 您可以使用 p12 推播憑證或權杖型驗證。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

1. 登入[Azure](https://portal.azure.com)。

1. 按一下 [建立資源]，然後搜尋並選擇 [通知中樞]，然後按一下 [建立]。

1. 更新下列欄位，然後按一下 [建立]：

    **基本詳細資料**  

    **訂用帳戶︰** 從下拉式清單選擇目標 [訂用帳戶]  
    **資源群組：** 建立新的**資源群組** (或選取現有的資源群組)  

    **命名空間詳細資料**  

    **通知中樞命名空間：** 輸入**通知中樞**命名空間的全域唯一名稱  

    > [!NOTE]
    > 確定已為此欄位選取 [新建] 選項。

    **通知中樞詳細資料**  

    **通知中樞：** 輸入**通知中樞**的名稱  
    **位置：** 從下拉式清單中選擇適當的位置  
    **定價層：** 保留預設的 [免費] 選項  

    > [!NOTE]
    > 除非您已達到免費層的中樞數目上限。

1. 佈建**通知中樞**之後，請瀏覽至該資源。
1. 瀏覽至新的**通知中樞**。
1. 從清單中選取 [存取原則] (位於 [管理] 底下)。
1. 請記下**原則名稱**值，以及其對應的**連接字串**值。
