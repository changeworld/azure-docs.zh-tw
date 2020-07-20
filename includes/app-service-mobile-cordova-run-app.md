---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240246"
---
1. 導航到用戶端專案 （.sln） 中的解決方案檔，並使用 Visual Studio 將其打開。

2. 在 Visual Studio 中，從開始箭號旁邊的下拉式清單中選擇解決方案平台 (Android、iOS 或 Windows)。 按一下綠色箭號上的下拉式清單以選取特定的部署裝置或模擬器。 您可以使用預設 Android 平台和 Ripple 模擬器。 更進階的教學課程 (例如，推播通知) 會要求您選取支援的裝置或模擬器。

3. 打開此資料夾中`ToDoActivity.java`的檔 - ZUMOAPPNAME/應用程式/src/主/java/com/示例/zumoappname。 應用程式名稱是 `ZUMOAPPNAME`。

4. 轉到[Azure 門戶](https://portal.azure.com/)並導航到您創建的移動應用。 在邊`Overview`欄選項卡上，查找移動應用的公共終結點的 URL。 示例 - 我的應用程式名稱"test123"的網站名稱將為https://test123.azurewebsites.net。

5. 轉到 ZUMOAPPNAME/www/js/index.js`index.js``onDeviceReady()`和方法中的檔，將參數替換為`ZUMOAPPURL`上面的公共終結點。

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    變成
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. 若要建置和執行 Cordova 應用程式，請按 F5 或按一下綠色箭號。 如果您在模擬器中看到要求網路存取權的安全性對話方塊，請接受它。

7. 在設備或模擬器上啟動應用後，在 **"輸入新文本**"中鍵入有意義的文本，例如*完成教程*，然後按一下"**添加**"按鈕。

後端會將要求中的資料插入 SQL Database 中的 TodoItem 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。

您可以對其他平台重複步驟 3 到 5。