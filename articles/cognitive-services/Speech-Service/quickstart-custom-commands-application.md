---
title: 快速入門：使用自訂命令建立語音助理
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會使用語音 Studio 來建立及測試基本的自訂命令應用程式。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d89f9330947d1c5d0146b8531f265d86d0fd5160
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329146"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>使用自訂命令建立語音助理

在本快速入門中，您會使用語音 Studio 來建立及測試基本的自訂命令應用程式。 您也可以從 Windows 用戶端應用程式存取此應用程式。 **自訂命令** 可讓您輕鬆地建立針對語音優先互動體驗優化的豐富語音命令應用程式。 它提供統一的撰寫體驗、自動化的裝載模型，以及較低的複雜度，協助您專注于為語音命令的案例建立最佳解決方案。

## <a name="region-availability"></a>區域可用性
目前，自訂命令支援在這些區域中建立的語音訂用帳戶：
* 美國西部
* 美國西部 2
* 美國東部
* 美國東部 2
* 北歐
* 西歐
* 美國中西部
* 印度中部
* 東亞
* 東南亞

## <a name="prerequisites"></a>先決條件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">在支援自訂命令的區域中建立 Azure 語音資源。<span class="docon docon-navigate-external x-hidden-focus"></span></a> 請參閱上方的 **區域可用性** 一節，以取得支援的區域清單。
> * 下載範例 [智慧房間 Lite](https://aka.ms/speech/cc-quickstart) json 檔案。
> * 下載 [Windows 語音助理用戶端](https://aka.ms/speech/va-samples-wvac)的最新版本。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>移至適用于自訂命令的語音 Studio

1. 在網頁瀏覽器中，移至 [Speech Studio](https://speech.microsoft.com/)。
1. 輸入您的認證來登入此入口網站。

   預設視圖是您的語音訂用帳戶清單。
   > [!NOTE]
   > 如果您沒有看到 [選取訂用帳戶] 頁面，您可以從頂端列的 [設定] 功能表選擇 [語音資源] 來流覽至該處。

1. 選取您的語音訂用帳戶，然後選取 [ **移至 Studio**]。
1. 選取 **自訂命令**。

   預設視圖是您所選訂用帳戶下的自訂命令應用程式清單。

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>匯入現有的應用程式作為新的自訂命令專案

1. 選取 [ **新增專案** ] 以建立專案。

1. 在 [ **名稱** ] 方塊中，輸入 [專案名稱] 做為 `Smart-Room-Lite` (，或輸入您選擇的其他內容) 。
1. 在 [ **語言** ] 清單中，選取 [ **英文 (美國) **。
1. 選取 [ **流覽** 檔案]，然後在 [流覽] 視窗中選取 [檔案 **SmartRoomLite.js** 。

    > [!div class="mx-imgBorder"]
    > ![建立專案](media/custom-commands/import-project.png)

1.  在 [ **LUIS 撰寫資源** ] 清單中，選取撰寫資源。 如果沒有有效的撰寫資源，請選取 [  **建立新的 LUIS 撰寫資源**] 建立一個。

    > [!div class="mx-imgBorder"]
    > ![建立資源](media/custom-commands/create-new-luis-resource.png)
    
    
    1. 在 [ **資源名稱** ] 方塊中，輸入資源的名稱。
    1. 在 **資源群組** 清單中，選取資源群組。
    1. 在 [ **位置** ] 清單中選取位置。
    1. 在 [ **定價層** ] 清單中，選取 [定價層]。
    
    
    > [!NOTE]
    > 您可以在 [資源群組] 欄位中輸入所需的資源組名，以建立資源群組。 當選取 [ **建立** ] 時，就會建立資源群組。


1. 接下來，選取 [ **建立** ] 以建立您的專案。
1. 建立專案之後，請選取您的專案。
您現在應該會看到新的自訂命令應用程式的總覽。

## <a name="try-out-some-voice-commands"></a>試用一些語音命令
1. 選取右窗格頂端的 [ **定型** ]。
1. 定型完成後，請選取 [ **測試** ]，並嘗試下列語句：
    - 開啟電視節目
    - 將溫度設定為80度
    - 關掉
    - 電視
    - 將鬧鐘設定為下午5點

## <a name="integrate-custom-commands-application-in-an-assistant"></a>在助理中整合自訂命令應用程式
您必須先發佈應用程式，才可以從 Speech Studio 外部存取此應用程式。 若要發行應用程式，您將需要設定預測 LUIS 資源。  

### <a name="update-prediction-luis-resource"></a>更新預測 LUIS 資源


1. 選取左窗格中的 [ **設定** ]，然後選取中間窗格中的 [  **LUIS 資源** ]。
1. 選取預測資源，或選取 [ **建立新資源**] 建立一個。
1. 選取 [儲存]。
    
    > [!div class="mx-imgBorder"]
    > ![設定 LUIS 資源](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> 由於撰寫資源每個月僅支援1000個預測端點要求，因此您必須先設定 LUIS 預測資源，然後再發佈自訂命令應用程式。

### <a name="publish-the-application"></a>發佈應用程式

選取右窗格上方的 [  **發行** ]。 發佈完成後，新的視窗隨即出現。 記下其中的 **應用程式識別碼** 和 **語音資源金鑰** 值。 您將需要這兩個值，才能從 Speech Studio 外部存取應用程式。

或者，您也可以選取 [**設定**  >  **一般**] 區段來取得這些值。

### <a name="access-application-from-client"></a>從用戶端存取應用程式

在本文的範圍內，我們將使用您在必要條件中下載的 Windows 語音助理用戶端。 將資料夾解壓縮。
1. 啟動 **VoiceAssistantClient.exe**。
1. 建立新的發行設定檔，並輸入 **連接設定檔**的值。 在 [ **一般設定** ] 區段中，輸入值訂用帳戶 **金鑰** (這與您發佈應用程式時所儲存的 **語音資源金鑰** 值相同) 、訂用帳戶 **金鑰區域** 和 **自訂命令應用程式識別碼**。
    > [!div class="mx-imgBorder"]
    > ![醒目顯示建立 WVAC 設定檔的 [一般設定] 區段的螢幕擷取畫面。](media/custom-commands/create-profile.png)
1. 選取 [ **儲存並套用設定檔**]。
1. 現在透過語音/文字試用下列輸入
    > [!div class="mx-imgBorder"]
    > ![WVAC 建立設定檔](media/custom-commands/conversation.png)


> [!TIP]
> 您可以按一下 [ **活動記錄** 檔] 中的專案，檢查從自訂命令服務傳送的原始回應。

## <a name="next-steps"></a>後續步驟

在本文中，您使用了現有的應用程式。 接下來，在 [操作 [說明] 區段](how-to-custom-commands-create-application-with-simple-commands.md)中，您將瞭解如何從頭開始設計、開發、測試和整合自訂命令應用程式。
