---
title: 快速入門：創建自訂命令（預覽） - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將創建和測試託管的自訂命令應用程式。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155582"
---
# <a name="quickstart-create-a-custom-command-preview"></a>快速入門：創建自訂命令（預覽）

在本文中，您將學習如何創建和測試託管的自訂命令應用程式。
該應用程式將識別諸如"打開電視"之類的話語，並回復一條簡單的消息"好的，打開電視"。

## <a name="prerequisites"></a>Prerequisites

- 語音訂閱。

如果沒有語音訂閱，可以通過導航到[語音工作室](https://speech.microsoft.com/)並選擇 **"創建語音資源**"來創建一個訂閱。

  > [!div class="mx-imgBorder"]
  > [![創建專案](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > 在預覽期間，僅支援 Westus2 區域。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>轉到語音工作室獲得自訂命令

1. 打開 Web 瀏覽器，然後導航到[語音工作室](https://speech.microsoft.com/)
1. 輸入要登錄到門戶的憑據

   - 預設視圖是語音訂閱清單
     > [!NOTE]
     > 如果看不到"選擇訂閱"頁，則可以通過從頂部欄的"設置"功能表中選擇"語音資源"來導航。

1. 選擇語音訂閱，然後選擇 **"轉到工作室**"
1. 選擇**自訂命令（預覽）**

預設視圖是您創建的自訂命令應用程式的清單。

## <a name="create-a-custom-commands-project"></a>創建自訂命令專案

1. 選擇 **"新建專案**"以創建新專案

   > [!div class="mx-imgBorder"]
   > ![建立專案](media/custom-speech-commands/create-new-project.png)

1. 輸入專案名稱和語言。
1. 選擇創作資源。 如果沒有有效的創作資源，請通過選擇 **"創建新資源"** 來創建一個資源。

   > [!div class="mx-imgBorder"]
   > ![建立資源](media/custom-speech-commands/create-new-resource.png)

   1. 輸入資源名稱、組、位置和定價層。

         > [!NOTE]
         > 您可以通過在"資源組"欄位中輸入所需的資源組名稱來創建資源組。 選擇 **"創建"** 後，將創建資源組。

1. 按一下"**創建**"以創建專案。
1. 創建後，選擇專案。

現在，您的視圖應該是自訂命令應用程式的概述。

## <a name="update-luis-resources-optional"></a>更新 LUIS 資源（可選）

您可以在新專案視窗中更新創作資源集，並設置用於在運行時識別輸入的預測資源。

> [!NOTE]
> 您需要在應用程式請求超出創作資源提供的 1，000 個請求之前設置預測資源。

> [!div class="mx-imgBorder"]
> ![設置 LUIS 資源](media/custom-speech-commands/set-luis-resources.png)

1. 通過從左側窗格中選擇 **"設置"，** 然後從中間窗格中選擇 LUIS 資源，導航到**LUIS 資源**窗格。
1. 選擇預測資源，或通過選擇 **"創建新資源"** 創建一個資源
1. 選擇 **"保存"**

## <a name="create-a-new-command"></a>創建新命令

現在，您可以創建命令。 讓我們使用一個示例，該示例將採用單個陳述，`turn on the tv`並使用消息`Ok, turning on the TV`進行回應。

1. 通過選擇命令旁邊的`+`圖示並為其指定名稱來創建新命令`TurnOn`
1. 選擇 **"保存"**

> [!div class="mx-imgBorder"]
> ![創建命令](media/custom-speech-commands/create-add-command.png)

命令是一組：

| 群組            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 示例句子 | 使用者可以說觸發此命令的示例陳述                                                                 |
| 參數       | 完成命令所需的資訊                                                                                |
| 完成規則 | 要執行命令的操作。 例如，回應使用者或與其他 Web 服務通信 |
| 高級規則   | 處理更具體或複雜情況的其他規則                                                              |

### <a name="add-a-sample-sentence"></a>添加示例句子

讓我們從示例句子開始，並提供使用者可以說的示例：

```
turn on the tv
```

現在，我們沒有參數，因此我們可以轉到完成規則。

### <a name="add-a-completion-rule"></a>添加完成規則

現在添加一個完成規則以回應指示正在執行的操作的使用者。

1. 通過選擇"完成規則"旁邊的圖示`+`創建新的完成規則
1. 輸入規則名稱
1. 新增動作
   1. 通過選擇"操作"旁邊的`+`圖示並選擇"操作"，創建新的語音回應操作`SpeechResponse`
   1. 輸入回應

   > [!NOTE]
   > 常規文本必須以破折號開頭。 有關詳細資訊，請[轉到此處](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![創建語音回應](media/custom-speech-commands/create-speech-response-action.png)

1. 按一下 **"保存"** 以保存規則

> [!div class="mx-imgBorder"]
> ![創建完成規則](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 設定    | 建議的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 規則名稱  | "確認回復"                   | 描述規則目的的名稱          |
| 條件 | None                                     | 確定規則何時可以運行的條件    |
| 動作    | 語音回應 "-好的，打開電視" | 規則條件為 true 時要執行的操作 |

## <a name="try-it-out"></a>試做

使用"測試聊天"面板測試行為。

> [!div class="mx-imgBorder"]
> ![使用網路聊天進行測試](media/custom-speech-commands/create-basic-test-chat.png)

- 鍵入："打開電視"
- 預期回應："好的，打開電視"

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：使用參數創建自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
