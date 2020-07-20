---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240328"
---
1. 使用 **Android Studio**、**匯入專案 (Eclipse ADT、Gradle 等等)** 開啟專案。 請確認選取此匯入選項，以避免發生 JDK 錯誤。

2. 打開此資料夾中`ToDoActivity.java`的檔 - ZUMOAPPNAME/應用程式/src/主/java/com/示例/zumoappname。 應用程式名稱是 `ZUMOAPPNAME`。

3. 轉到[Azure 門戶](https://portal.azure.com/)並導航到您創建的移動應用。 在邊`Overview`欄選項卡上，查找移動應用的公共終結點的 URL。 示例 - 我的應用程式名稱"test123"的網站名稱將為https://test123.azurewebsites.net。

4. 在`onCreate()`方法中，`ZUMOAPPURL`將參數替換為上面的公共終結點。
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    變成
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. 按 [ **執行 '應用程式'** ] 按鈕以建置專案，並在Android 模擬器中啟動應用程式。

4. 在應用中，鍵入有意義的文本，如*完成教程，* 然後按一下"添加"按鈕。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。
    ![快速啟動安卓](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)