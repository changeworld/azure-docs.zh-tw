---
title: 在 Visual Studio Code 中調試 Azure API 管理原則 |Microsoft Docs
description: 瞭解如何使用 Azure API 管理 Visual Studio Code 擴充功能來進行 Azure API 管理原則的調試
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: a8ed834c9ab093ae063e386978bca4d73fa46543
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400721"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>在 Visual Studio Code 中進行 Azure API 管理原則的調試

Azure API 管理中的[原則](api-management-policies.md)提供強大的功能，可協助 API 發行者解決跨領域考慮，例如驗證、授權、節流、快取和轉換。 原則是陳述式的集合，會因 API 的要求或回應循序執行。 

本文說明如何使用 [適用于 Visual Studio Code 的 AZURE API 管理延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)模組來進行 API 管理原則的偵錯工具。 

> [!NOTE]
> 這項功能處於公開預覽狀態。

## <a name="prerequisites"></a>Prerequisites

* 先遵循本 [快速入門](get-started-create-service-instance.md) ，建立 API 管理開發人員層實例。

* 針對 Visual Studio Code 安裝 [Visual Studio Code](https://code.visualstudio.com/) 和最新版的 [Azure API 管理延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)模組。 

* 將 API 匯入至 API 管理實例。 如需範例步驟，請參閱 [教學課程：使用適用于 Visual Studio Code 的 Api 管理延伸模組來匯入和管理 api](visual-studio-code-tutorial.md)。

## <a name="restrictions-and-limitations"></a>限制事項

這項功能僅適用于 API 管理的開發人員層。 每個 API 管理實例只支援一個並行的調試會話。

## <a name="initiate-a-debugging-session"></a>起始調試進程

1. 啟動 Visual Studio Code
2. 流覽至 Azure 擴充功能下的 API 管理延伸模組
3. 尋找要進行調試的 API 管理實例
4. 尋找要進行調試的 API 和作業
5. 以滑鼠右鍵按一下作業，然後選取 [**啟動原則調試**]

此時，擴充功能將會嘗試起始並建立 API 管理閘道的調試會話。

![起始調試](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>傳送測試要求
當偵錯工具建立完成時，擴充功能將會開啟新的編輯器，讓我們能夠利用 [REST 用戶端擴充](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)功能來建立測試 HTTP 要求，並將其傳送到此作業。

您將會注意到 **Apim-Debug** 標頭已新增至要求中。 此標頭是必要的，而且值必須設定為服務層級的全存取訂用帳戶金鑰，以在 API 管理閘道中觸發偵錯工具。

根據您的測試案例，修改編輯器中的 HTTP 要求。 然後按一下 [ **傳送要求** ]，將測試要求傳送至 API 管理閘道。

![傳送測試要求](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>偵錯原則
在傳送測試 HTTP 要求之後，擴充功能將會開啟 [偵錯工具] 視窗，顯示此作業的有效原則，並在第一個有效的原則下停止。 

![調試原則](media/api-management-debug-policies/main-window.png)

若要遵循原則管線，您可以透過單一步驟逐步完成個別原則，或在原則上設定中斷點，並直接跳到該原則。 

在 [ **變數** ] 面板中，您可以檢查系統建立和使用者建立之變數的值。 在 [ **中斷點** ] 面板中，您可以看到所有已設定中斷點的清單。 在 [ **呼叫堆疊** ] 面板中，您可以看到目前的有效原則範圍。 

如果原則執行期間發生錯誤，您將會在發生錯誤的情況下看到錯誤的詳細資料。 

![例外狀況](media/api-management-debug-policies/exception.png)

> [!TIP]
> 請記得在完成時按一下 [ **停止** ] 按鈕，結束偵錯工具會話。


## <a name="next-steps"></a>後續步驟

+ 深入瞭解 Visual Studio Code 的 [API 管理延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)模組。 
+ 在[GitHub 存放庫](https://github.com/Microsoft/vscode-apimanagement)中報告問題

