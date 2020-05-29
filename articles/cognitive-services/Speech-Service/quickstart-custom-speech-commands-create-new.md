---
title: 快速入門：建立自訂命令預覽應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將建立並測試託管的自訂命令預覽應用程式。 應用程式將會處理語句。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142345"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>快速入門：建立自訂命令預覽應用程式

在本快速入門中，您將瞭解如何建立和測試自訂命令應用程式。
應用程式會處理像是「開啟電視」的語句，並使用像是「確定，開啟電視」之類的簡單訊息來回複。

## <a name="prerequisites"></a>Prerequisites

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">建立 Azure 語音資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 目前，自訂命令僅支援 westus、westus2 和 neur 區域中的語音訂用帳戶。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>前往適用于自訂命令的語音 Studio

1. 在網頁瀏覽器中，移至 [[語音 Studio](https://speech.microsoft.com/)]。
1. 輸入您的認證來登入此入口網站。

   預設的視圖是您的語音訂用帳戶清單。
    > [!NOTE]
    > 如果您沒有看到 [選取訂用帳戶] 頁面，可以在畫面頂端的 [設定] 功能表中選取 [**語音資源**] 來取得。

1. 選取您的語音訂用帳戶，然後選取 [**移至 Studio**]。
1. 選取 [**自訂命令**]。

     預設的視圖是您在所選訂用帳戶中擁有的自訂命令應用程式清單。

## <a name="create-a-custom-commands-project"></a>建立自訂命令專案

1. 選取 [**新增專案**] 以建立專案。

   > [!div class="mx-imgBorder"]
   > ![建立專案](media/custom-speech-commands/create-new-project.png)

1. 在 [**名稱**] 方塊中，輸入專案名稱。
1. 在 [**語言**] 清單中，選取語言。
1. 在 [ **LUIS 撰寫資源**] 清單中，選取 [撰寫] 資源。 如果沒有有效的撰寫資源，請選取 [**建立新的 LUIS 撰寫資源**] 建立一個。

   > [!div class="mx-imgBorder"]
   > ![建立資源](media/custom-speech-commands/create-new-resource.png)

   1. 在 [**資源名稱**] 方塊中，輸入資源的名稱。
   1. 在 [**資源群組**] 清單中，選取資源群組。
   1. 在 [**位置**] 清單中，選取一個位置。
   1. 在 [**定價層**] 清單中，選取一層。

      > [!NOTE]
      > 您可以在 [**資源群組**] 方塊中輸入資源組名，以建立資源群組。 當您選取 [**建立**] 時，將會建立資源群組。

1. 選取 [建立]。
1. 建立專案之後，請選取它。

    您現在應該會看到新的自訂命令應用程式的總覽。

## <a name="update-luis-resources-optional"></a>更新 LUIS 資源（選擇性）

您可以更新您在 [**新增專案**] 視窗中選取的撰寫資源，並設定預測資源。 當您的自訂命令應用程式發行時，會使用預測資源來進行辨識。 在開發和測試階段中，您不需要預測資源。

1. 在左窗格中選取 [**設定**]，然後在中間窗格中選取 [ **LUIS 資源**]。
1. 選取預測資源，或選取 [**建立新資源**] 來建立一個。
1. 選取 [儲存]。
    
    > [!div class="mx-imgBorder"]
    > ![設定 LUIS 資源](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> 由於撰寫資源僅支援每月1000個預測端點要求，因此您必須先設定 LUIS 預測資源，然後再發佈您的自訂命令應用程式。


## <a name="create-a-command"></a>建立命令

讓我們建立一個簡單的命令，它會採用單一語句， `turn on the tv` 並以訊息回應 `Ok, turning on the tv` 。

1. 選取左窗格頂端的 [**新增命令**] 來建立命令。 [**新增命令**] 視窗隨即開啟。
1. 在 [**名稱**] 方塊中，輸入**homeautomation.turnon**。
1. 選取 [建立]。

中間窗格會列出命令的屬性：


| 組態            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **範例句子** | 語句的範例會告訴使用者觸發命令。                                                                 |
| **參數**       | 完成命令所需的資訊。                                                                                |
| **完成規則** | 將採取來完成命令的動作。 例如，回應使用者或與另一個 web 服務通訊。 |
| **互動規則**   | 處理更具體或複雜情況的其他規則。                                                              |


> [!div class="mx-imgBorder"]
> ![建立命令](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>新增範例句子

讓我們從**範例句子**一節開始。 我們將提供使用者可以說的範例。

1. 在中間窗格中選取**範例句子**。 
1. 在右窗格中，新增範例：

    ```
    turn on the tv
    ```

1. 選取窗格頂端的 [**儲存**]。

我們目前沒有參數，因此可以移至**完成規則**一節。

### <a name="add-a-completion-rule"></a>新增完成規則

現在，新增具有下列設定的完成規則。 此規則會告訴使用者正在採取履行動作。


| 設定    | 建議的值                          | 描述                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **名稱**  | **ConfirmationResponse**                  | 描述規則用途的名稱。          |
| **條件** | 無                                     | 決定何時可執行規則的條件。    |
| **動作**    | **傳送語音回應-> [確定]，開啟電視** | 規則條件為 true 時所要採取的動作。 |

1. 選取中間窗格頂端的 **[新增]** ，以建立新的完成規則。
1. 在 [名稱]**** 方塊中輸入名稱。
1. 新增動作。
   1. 選取 [**動作**] 區段中的 [**新增動作**] 來建立動作。
   1. 在 [**編輯動作**] 視窗的 [**類型**] 清單中，選取 [**傳送語音回應**]。
   1. 在 [**回應**] 底下，選取 [**簡單編輯器**]。 在**第一個 [變化**] 方塊中，輸入 **[確定]，開啟電視**。

   > [!div class="mx-imgBorder"]
   > ![建立回應](media/custom-speech-commands/create-speech-response-action.png)

1. 選取 [**儲存**] 以儲存規則。
1. 回到 [**完成規則**] 區段中，選取 [**儲存**] 以儲存所有變更。 

> [!div class="mx-imgBorder"]
> ![建立完成規則](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>試試看

使用 [測試聊天] 面板來測試行為。
1. 選取右窗格頂端的 [**定型**]。
1. 定型完成之後，請選取 [**測試**]。 隨即會出現新的 [**測試您的應用程式**] 視窗。
    - 輸入 **[開啟電視**]
    - 預期的回應： **[確定]，開啟電視**


> [!div class="mx-imgBorder"]
> ![測試行為](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：使用參數來建立自訂命令預覽應用程式](./quickstart-custom-speech-commands-create-parameters.md)
