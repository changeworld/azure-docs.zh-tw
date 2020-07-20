---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240286"
---
1. 使用 Xcode 打開下載的用戶端專案。

2. 轉到[Azure 門戶](https://portal.azure.com/)並導航到您創建的移動應用。 在邊`Overview`欄選項卡上，查找移動應用的公共終結點的 URL。 示例 - 我的應用程式名稱"test123"的網站名稱將為https://test123.azurewebsites.net。

3. 對於 Swift 專案，打開`ToDoTableViewController.swift`此資料夾中的檔 - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift。 應用程式名稱是 `ZUMOAPPNAME`。

4. 在`viewDidLoad()`方法中，`ZUMOAPPURL`將參數替換為上面的公共終結點。

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    變成
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. 對於 Objective-C 專案，打開`QSTodoService.m`此資料夾中的檔 - ZUMOAPPNAME/ZUMOAPPNAME。 應用程式名稱是 `ZUMOAPPNAME`。

6. 在`init`方法中，`ZUMOAPPURL`將參數替換為上面的公共終結點。

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    變成
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. 按 [執行] **** 按鈕以建置專案，並在 iOS 模擬器中啟動應用程式。

8. 在應用程式中，按一下加號 （）**+** 圖示，鍵入有意義的文本，如*完成教程*，然後按一下"保存"按鈕。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。

   ![在 iOS 上執行的快速入門應用程式](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
