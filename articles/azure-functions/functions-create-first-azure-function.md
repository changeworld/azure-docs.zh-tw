---
title: 在 Azure 入口網站中建立您的第一個函式
description: 了解如何使用 Azure 入口網站來建立您的第一個 Azure 函式以進行無伺服器執行。
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 030af8a289daaf03d17f8402e8d603e893657853
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123599"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>在 Azure 入口網站中建立您的第一個函式

Azure Functions 可讓您在無伺服器環境中執行程式碼，而不需要先建立虛擬機器 (VM) 或發佈 Web 應用程式。 在本文中，您將瞭解如何使用 Azure Functions 來建立 Azure 入口網站中的 "hello world" HTTP 觸發程式函數。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

如果您是 C# 開發人員，請考慮[在 Visual Studio 2019 中建立第一個函式](functions-create-your-first-function-visual-studio.md)，而不是在入口網站中建立。 

## <a name="sign-in-to-azure"></a>登入 Azure

使用您的 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式可讓您將多個函式群組為邏輯單位，以方便您管理、部署、調整和共用資源。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

接下來，您要在新的函式應用程式中建立函式。

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>建立 HTTP 觸發程式函數

1. 從 [**函數**] 視窗的左側功能表中，選取 [**函數**]，然後從頂端功能表中選取 [**新增**]。 
 
1. 從 [**新增函數**] 視窗中，選取 [ **Http 觸發**程式]。

    ![選擇 HTTP 觸發程式函數](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. 在 [**新增函數**] 視窗中，接受 [**新**函式] 的預設名稱，或輸入新的名稱。 

1. 從 [**授權層級**] 下拉式清單中選擇 [**匿名**]，然後選取 [**建立函數**]。

    Azure 會建立 HTTP 觸發程式函數。 現在，您可以藉由傳送 HTTP 要求來執行新的函式。

## <a name="test-the-function"></a>測試函式

1. 在新的 HTTP 觸發程式函式中，從左側功能表中選取 [程式**代碼 + 測試**]，然後從頂端功能表中選取 [取得函式**URL** ]。

    ![選取 [取得函數 URL]](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. 在 [**取得函數 URL** ] 對話方塊中，從下拉式清單選取 [**預設值**]，然後選取 [**複製到剪貼**簿] 圖示。 

    ![從 Azure 入口網站複製函式 URL](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. 將函式 URL 貼入瀏覽器的網址列中。 將查詢字串值新增 `?name=<your_name>` 至此 URL 的結尾，然後按 enter 鍵以執行要求。 

    下列範例會顯示瀏覽器中的回應：

    ![瀏覽器中的函式回應。](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    要求 URL 預設會包含所需金鑰，以便透過 HTTP 存取您的函式。

1. 當函式執行時，系統會將追蹤資訊寫入到記錄中。 若要查看追蹤輸出，請回到入口網站中的 [程式**代碼 + 測試**] 頁面，然後展開頁面底部的 [**記錄**] 箭號。

   ![Azure 入口網站中的函式記錄檢視器。](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

