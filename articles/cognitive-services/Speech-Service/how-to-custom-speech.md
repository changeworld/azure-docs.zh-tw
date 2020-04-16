---
title: 開始使用自訂語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: 自訂語音是一組線上工具,允許您評估並提高我們應用程式、工具和產品的語音到文本準確性。 入門只需一小撮測試音頻檔。 按照以下鏈接開始創建自定義的語音到文本體驗。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: a52735ee62f564a5dc536fd2b7d3539406388341
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402072"
---
# <a name="what-is-custom-speech"></a>什麼是自定義語音?

[自訂語音](https://aka.ms/customspeech)是一組線上工具,允許您評估和改進 Microsoft 的應用程式、工具和產品的語音到文本準確性。 入門只需一小撮測試音頻檔。 按照以下鏈接開始創建自定義的語音到文本體驗。

## <a name="whats-in-custom-speech"></a>自定義語音中有什麼?

在使用自定義語音執行任何操作之前,需要 Azure 帳戶和語音服務訂閱。 獲得帳戶後,您可以準備數據、訓練和測試模型、檢查識別品質、評估準確性,並最終部署和使用自定義語音到文本模型。

此圖突出顯示構成[自定義語音門戶](https://aka.ms/customspeech)的部分。 使用以下連結瞭解有關每個步驟的更多內容。

![突出顯示構成自定義語音門戶的不同元件。](./media/custom-speech/custom-speech-overview.png)

1. [訂閱和創建專案](#set-up-your-azure-account)- 創建 Azure 帳戶並訂閱語音服務。 此統一訂閱讓您存取語音到文字、文字到語音轉換、語音轉換與[自訂語音門戶](https://speech.microsoft.com/customspeech)。 然後,使用語音服務訂閱,創建第一個自定義語音專案。

2. [上傳測試資料](how-to-custom-speech-test-data.md)- 上傳測試資料(音訊檔),以評估 Microsoft 為您的應用程式、工具和產品提供的語音到文本產品。

3. [檢查識別品質](how-to-custom-speech-inspect-data.md)- 使用[自訂語音門戶](https://speech.microsoft.com/customspeech)播放上傳的音訊並檢查測試數據的語音辨識品質。 有關定量測量,請參閱[檢查資料](how-to-custom-speech-inspect-data.md)。

4. [評估準確性](how-to-custom-speech-evaluate-data.md)- 評估語音到文本模型的準確性。 [自定義語音門戶](https://speech.microsoft.com/customspeech)將提供 Word*錯誤率*,可用於確定是否需要其他培訓。 如果您對準確性感到滿意,則可以直接使用語音服務 API。 如果要將準確性提高 5% - 20%,請使用門戶中的 **「訓練**」選項卡上傳其他培訓數據,如人工標記的成績單和相關文本。

5. [訓練模型](how-to-custom-speech-train-model.md)- 通過提供書面腳本(10-1,000 小時)和相關文本(<200 MB)以及音訊測試數據,提高語音到文本模型的準確性。 此資料有助於訓練語音到文本模型。 培訓后,重新測試,如果您對結果感到滿意,則可以部署模型。

6. [部署模型](how-to-custom-speech-deploy-model.md)- 為語音到文本模型創建自定義終結點,並在應用程式、工具或產品中使用。

## <a name="set-up-your-azure-account"></a>設定 Azure 帳戶

在使用[自定義語音門戶](https://speech.microsoft.com/customspeech)創建自定義模型之前,需要語音服務訂閱。 以這些說明建立標準的語音服務訂閱:[建立語音訂閱](get-started.md#new-resource)。

> [!NOTE]
> 請務必創建標準 (S0) 訂閱,不支援免費試用 (F0) 訂閱。

創建 Azure 帳戶和語音服務訂閱後,需要登錄到[自訂語音門戶](https://speech.microsoft.com/customspeech)並連接訂閱。

1. 從 Azure 門戶獲取語音服務訂閱密鑰。
2. 登入[自訂的語音門戶](https://aka.ms/custom-speech)。
3. 選擇需要處理的訂閱並創建語音專案。
4. 如果要修改訂閱,請使用位於頂部導航中的**齒輪**圖示。

## <a name="how-to-create-a-project"></a>如何建立專案

資料、模型、測試和終結點等內容被組織到[自訂語音門戶](https://speech.microsoft.com/customspeech)中的**項目中**。 每個專案都特定於域和國家/語言。 例如,您可以為在美國使用英語的呼叫中心創建專案。

要建立第一個專案,請選擇 **「語音到文字/自訂語音**」,然後單擊 **「新專案**」。 按照嚮導提供的說明創建專案。 建立項目後,應看到四個選項卡:**資料**、**測試**、**培訓和****部署**。 使用[「後續步驟」](#next-steps)中提供的連結瞭解如何使用每個選項卡。

> [!IMPORTANT]
> [自定義語音門戶](https://aka.ms/custom-speech)最近更新! 如果在CRIS.ai門戶或使用API創建了以前的數據、模型、測試和已發佈的終結點,則需要在新門戶中創建新專案以連接到這些舊實體。

## <a name="next-steps"></a>後續步驟

* [準備與測試資料](how-to-custom-speech-test-data.md)
* [檢查資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型您的模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
