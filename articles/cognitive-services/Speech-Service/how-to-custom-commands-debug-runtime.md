---
title: 執行自訂命令應用程式時的偵錯工具錯誤
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何在自訂命令應用程式中進行執行時間錯誤的偵錯工具。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 1c9b0b48c7862990cfa2c8ba38bde0851058a228
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86023018"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>執行自訂命令應用程式時的偵錯工具錯誤

本文描述如何在執行自訂命令應用程式時看到錯誤時進行調試。 

## <a name="connection-failed"></a>連接失敗

如果您從用戶端應用程式執行自訂命令應用程式 [ (使用語音 SDK) ](./how-to-custom-commands-setup-speech-sdk.md) 或 [Windows 語音助理用戶端](./how-to-custom-commands-developer-flow-test.md)，您可能會遇到連接錯誤，如下所示：

| 錯誤碼 | 詳細資料 |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure： WebSocket 升級失敗，發生驗證錯誤 |
| [1002](#error-1002)] | 當預期狀態碼 ' 101 ' 時，伺服器傳回的狀態碼為 ' 404 '。 |

### <a name="error-401"></a>錯誤 401
- 用戶端應用程式中指定的區域與自訂命令應用程式的區域不相符

- 語音資源金鑰無效
    
    請確定您的語音資源金鑰正確無誤。

### <a name="error-1002"></a>錯誤1002 
- 未發行您的自訂命令應用程式
    
    在入口網站中發佈您的應用程式。

- 您的自訂命令 applicationId 無效

    請確定您的自訂命令應用程式識別碼正確。
 語音資源外的自訂命令應用程式

    請確定已在您的語音資源下建立自訂命令應用程式。

如需疑難排解連接問題的詳細資訊，請參閱 [Windows 語音助理用戶端疑難排解](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>對話方塊已取消

執行自訂命令應用程式時，會在發生一些錯誤時取消對話方塊。

- 如果您要在入口網站中測試應用程式，它會直接顯示取消描述並播放錯誤 earcon。 

- 如果您是使用 [Windows 語音助理用戶端](./how-to-custom-commands-developer-flow-test.md)來執行應用程式，則會 earcon 錯誤。 您可以在**活動記錄**下找到**事件： CancelledDialog** 。

- 如果您遵循用戶端應用程式範例 [用戶端應用程式 (與語音 SDK) ](./how-to-custom-commands-setup-speech-sdk.md)，則會 earcon 錯誤。 您可以在**狀態**下找到**事件： CancelledDialog** 。

- 如果您要建立自己的用戶端應用程式，您一律可以設計所需的邏輯來處理 CancelledDialog 事件。

CancelledDialog 事件是由取消程式碼和描述所組成，如下所示：

| 取消程式碼 | 取消描述 |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | 在允許的最大數目之後未進行任何進度 |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | 超過辨識器使用量配額 |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | 與辨識器的連接失敗 |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | 無法使用目前的訂用帳戶存取此應用程式 |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | 輸入超過辨識器支援的長度上限 |
| [RecognizerNotFound](#recognizer-not-found) | 找不到辨識器 |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | 辨識器的查詢無效 |
| [RecognizerError](#recognizer-return-an-error) | 辨識器傳回錯誤 |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>在允許的最大數目之後未進行任何進度
當必要的位置在特定的回合數之後未成功更新時，就會取消此對話方塊。 內建的最大值為3。

### <a name="recognizer-usage-quota-exceeded"></a>超過辨識器使用量配額
Language Understanding (LUIS) 具有資源使用量的限制。 通常會造成「辨識器使用量配額超過錯誤」的原因： 
- 您的 LUIS 撰寫超過限制

    將預測資源新增至您的自訂命令應用程式： 
    1. 移至 [ **設定**]、[LUIS 資源]
    1. 從**預測資源**選擇預測資源，或按一下 [**建立新資源**] 

- 您的 LUIS 預測資源超過限制

    如果您使用 F0 預測資源，其限制為每月10千個月，5個查詢/秒。

如需 LUIS 資源限制的詳細資訊，請參閱 [Language Understanding 資源使用量和限制](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>與辨識器的連接失敗
這通常表示暫時性連接無法 Language Understanding (LUIS) 辨識器。 再試一次，就應該解決問題。

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>無法使用目前的訂用帳戶存取此應用程式
您的訂用帳戶未獲授權存取 LUIS 應用程式。 

### <a name="input-exceeds-the-maximum-supported-length"></a>輸入超過支援的長度上限
您的輸入超過500個字元。 輸入語句最多隻允許500個字元。

### <a name="invalid-query-for-the-recognizer"></a>辨識器的查詢無效
您的輸入超過500個字元。 輸入語句最多隻允許500個字元。

### <a name="recognizer-return-an-error"></a>辨識器傳回錯誤
LUIS 辨識器在嘗試辨識您的輸入時傳回錯誤。

### <a name="recognizer-not-found"></a>找不到辨識器
在您的自訂命令對話模型中找不到指定的辨識器類型。 目前，我們只支援 [Language Understanding (LUIS) 辨識器](https://www.luis.ai/)。

## <a name="other-common-errors"></a>其他常見的錯誤
### <a name="unexpected-response"></a>未預期的回應
非預期的回應可能會導致多個問題。 以下是一些開始的檢查：
- 是/不是範例句子中的意圖

    因為使用 with 確認功能時，我們目前不支援 Yes/No 意圖。 未偵測到範例句子中定義的所有 Yes/No 意圖。

- 命令之間的類似意圖和範例句子

    當兩個命令共用類似意圖和範例句子時，LUIS 辨識精確度可能會受到影響。 您可以嘗試讓命令功能和範例句子盡可能相異。

    如需改善辨識精確度的最佳作法，請參閱 [LUIS 最佳作法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)。

- 對話方塊已取消
    
    檢查上一節中取消的原因。

### <a name="error-while-rendering-the-template"></a>轉譯範本時發生錯誤
語音回應中使用了未定義的參數。 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>物件參考未設定為物件的實例
您在 **傳送活動中定義給用戶端** 動作的 JSON 承載中有空白參數。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [請參閱 GitHub 上的範例](https://aka.ms/speech/cc-samples)