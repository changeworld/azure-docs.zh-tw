---
title: 撰寫自訂命令應用程式時的 Debug 錯誤（預覽）
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何在撰寫自訂命令應用程式時，進行錯誤的調試。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 9c84b35318637f5b89e6c88c0ebb3fd6616533fc
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023120"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>撰寫自訂命令應用程式時的 Debug 錯誤

本文說明如何在您建立自訂命令應用程式時看到錯誤時進行 debug。 

## <a name="errors-when-creating-an-application"></a>建立應用程式時發生錯誤
在建立自訂命令應用程式時，自訂命令也會在[LUIS](https://www.luis.ai/)中建立應用程式。 

[LUIS 會限制每個撰寫資源的500應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits)。 如果您使用的撰寫資源已經有500應用程式，則建立 LUIS 應用程式可能會失敗。 

請確定選取的 LUIS 撰寫資源少於500個應用程式。 如果不是，您可以建立新的 LUIS 撰寫資源、切換至另一個資源，或嘗試清除 LUIS 應用程式。  

## <a name="errors-when-deleting-an-application"></a>刪除應用程式時發生錯誤
### <a name="cant-delete-luis-application"></a>無法刪除 LUIS 應用程式
刪除自訂命令應用程式時，自訂命令也可能會嘗試刪除與自訂命令應用程式相關聯的 LUIS 應用程式。

如果 LUIS 應用程式刪除失敗，請移至您的[LUIS](https://www.luis.ai/)帳戶手動將它們刪除。

### <a name="toomanyrequests"></a>TooManyRequests
當您嘗試一次刪除大量應用程式時，您可能會看到「TooManyRequests」錯誤。 這些錯誤表示您的刪除要求會受到 Azure 的節流。 

重新整理頁面，並嘗試刪除較少的應用程式。

## <a name="errors-when-modifying-an-application"></a>修改應用程式時發生錯誤

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>無法刪除參數或 Web 端點
當您使用參數時，不允許將它刪除。 在任何語音回應、範例句子、條件、動作中移除參數的任何參考，然後再試一次。

### <a name="cant-delete-a-web-endpoint"></a>無法刪除 Web 端點
當 Web 端點正在使用時，不允許您刪除它。 移除 Web 端點之前，請先移除使用此 Web 端點的任何**呼叫 web**端點動作。

## <a name="errors-when-training-an-application"></a>定型應用程式時發生錯誤
### <a name="built-in-intents"></a>內建意圖
LUIS 具有內建的「是/否」意圖。 具有只有 "yes"、"no" 的範例句子將無法進行定型。 

| 關鍵字 | 變化 | 
| ------- | --------- | 
| Yes | 確定，確定 |
| No | 不對，Not | 

### <a name="common-sample-sentences"></a>常見的範例句子
自訂命令不允許在不同命令之間共用的常見範例句子。 如果某個命令中的一些範例句子已在另一個命令中定義，則應用程式的定型可能會失敗。 

請確定您沒有在不同命令之間共用的常見範例句子。 

如需跨不同命令平衡範例句子的最佳作法，請參閱[LUIS 最佳做法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)。

### <a name="empty-sample-sentences"></a>空的樣本句子
每個命令都必須至少有一個範例句子。

### <a name="undefined-parameter-in-sample-sentences"></a>範例句子中未定義的參數
範例句子中使用了一或多個參數，但未定義。

### <a name="training-takes-too-long"></a>定型花費的時間太長
LUIS 訓練的目的是要以較少的範例快速學習。 不要加入太多範例句子。 

如果您有許多類似的範例句子，請定義參數，將其抽象化為模式，並將它新增至範例句子。

例如，您可以為下列範例句子定義參數 {車輛}，並只將 "Book a {車輛}" 加入至範例句子。

| 範例句子 | 模式 | 
| ------- | ------- | 
| 預訂汽車 | 書 a {車輛} | 
| 預訂班機 | 書 a {車輛} |
| 預訂計程車 | 書 a {車輛} |

如需 LUIS 訓練的最佳作法，請參閱[LUIS 最佳作法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)。

## <a name="cant-update-luis-key"></a>無法更新 LUIS 金鑰
### <a name="reassign-to-e0-authoring-resource"></a>重新指派給 E0 撰寫資源
LUIS 不支援將 LUIS 應用程式重新指派給 E0 撰寫資源。

如果您需要將撰寫資源從 F0 變更為 E0，或變更為不同的 E0 資源，請重新建立應用程式。 

若要快速匯出現有的應用程式，並將它匯入至新的應用程式，請參閱[使用 Azure DevOps 的持續部署](./how-to-custom-commands-deploy-cicd.md)。

### <a name="save-button-is-disabled"></a>[儲存] 按鈕已停用
如果您從未將 LUIS 預測資源指派給應用程式，當您嘗試變更撰寫資源而不新增預測資源時，會停用 [儲存] 按鈕。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [請參閱 GitHub 上的範例](https://aka.ms/speech/cc-samples)
