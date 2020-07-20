---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174487"
---
1. 以滑鼠右鍵按一下 Windows 市集專案、按一下 [設定為啟始專案]  ，然後按 F5 鍵以執行 Windows 市集應用程式。

    應用程式啟動後，系統便會替裝置註冊推播通知。
2. 停止 Windows 市集應用程式，接著對 Windows Phone 市集應用程式重複上一個步驟。

    此時，系統會註冊這兩個裝置以接收推播通知。

3. 再執行 Windows 市集應用程式一次，接著在 [插入 TodoItem]  中輸入文字，然後按一下 [儲存]  。

    請注意，插入作業完成後，Windows 市集應用程式和 Windows Phone 應用程式都會收到來自 WNS 的推播通知。 即使應用程式未處於執行狀態，Windows Phone 上依然會顯示通知。

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
