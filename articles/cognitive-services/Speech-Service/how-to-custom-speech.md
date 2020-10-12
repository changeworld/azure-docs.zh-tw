---
title: 開始使用自訂語音-語音服務
titleSuffix: Azure Cognitive Services
description: 自訂語音是一組線上工具，可讓您針對應用程式、工具和產品評估及改善語音轉換文字的精確度。 開始使用的只是少數的測試音訊檔案。 遵循下列連結以開始建立自訂的語音轉換文字體驗。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: b9ac473c3864b4be06c978e7d6a3555bb4550d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056834"
---
# <a name="what-is-custom-speech"></a>什麼是自訂語音？

[自訂語音](https://aka.ms/customspeech) 是一組線上工具，可讓您針對應用程式、工具和產品評估及改善 Microsoft 的語音轉換文字精確度。 開始使用的只是少數的測試音訊檔案。 遵循下列連結以開始建立自訂的語音轉換文字體驗。

## <a name="whats-in-custom-speech"></a>什麼是自訂語音？

您需要有 Azure 帳戶和語音服務訂用帳戶，才能使用自訂語音進行任何動作。 一旦取得帳戶之後，您就可以準備您的資料、定型和測試您的模型、檢查辨識品質、評估精確度，並最終部署和使用自訂的語音轉換文字模型。

此圖表會反白顯示組成 [自訂語音入口網站](https://aka.ms/customspeech)的部分。 您可以使用下列連結來深入瞭解每個步驟。

![強調組成自訂語音入口網站的不同元件。](./media/custom-speech/custom-speech-overview.png)

1. [訂閱並建立專案](#set-up-your-azure-account) -建立 Azure 帳戶，並訂閱語音服務。 此整合訂用帳戶可讓您存取語音轉換文字、文字轉換語音、語音翻譯和 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。 然後，使用您的語音服務訂用帳戶，建立您的第一個自訂語音專案。

2. [上傳測試資料](how-to-custom-speech-test-data.md) -將測試資料上傳 (的音訊檔案) ，以針對您的應用程式、工具和產品評估 Microsoft 的語音轉換文字供應專案。

3. [檢查辨識品質](how-to-custom-speech-inspect-data.md) -使用 [自訂語音入口網站](https://speech.microsoft.com/customspeech) 播放上傳的音訊，並檢查測試資料的語音辨識品質。 如需量化度量的詳細資料，請參閱 [檢查資料](how-to-custom-speech-inspect-data.md)。

4. [評估精確度](how-to-custom-speech-evaluate-data.md) -評估語音轉換文字模型的精確度。 [自訂語音入口網站](https://speech.microsoft.com/customspeech)會提供*單字錯誤率*，可用來判斷是否需要額外的定型。 如果您對精確度感到滿意，您可以直接使用語音服務 Api。 如果您想要以相對平均 5%-20% 來改善精確度，請使用入口網站中的 [ **定型** ] 索引標籤上傳其他定型資料，例如人為標記的文字記錄和相關文字。

5. [提高精確度](how-to-custom-speech-improve-accuracy.md) -根據您的案例，策略性地選擇其他定型資料來改善語音轉換文字模型的品質。

6. [定型模型](how-to-custom-speech-train-model.md) -提供撰寫的文字記錄 (10-1000 小時) 和相關文字 ( # B0 200 MB) 以及您的音訊測試資料，以改善語音轉換文字模型的精確度。 這種資料有助於定型語音轉換文字模型。 在訓練、重新測試之後，如果您對結果感到滿意，就可以部署模型。

7. [部署模型](how-to-custom-speech-deploy-model.md) -為您的語音轉換文字模型建立自訂端點，並將其用於您的應用程式、工具或產品。

## <a name="set-up-your-azure-account"></a>設定您的 Azure 帳戶

您必須要有語音服務訂用帳戶，才能使用 [自訂語音入口網站](https://speech.microsoft.com/customspeech) 來建立自訂模型。 請遵循下列指示來建立標準語音服務訂用帳戶： [建立語音訂用](get-started.md#new-resource)帳戶。

> [!NOTE]
> 請務必建立標準 (S0) 訂用帳戶，但不支援免費的 (F0) 訂閱。

建立 Azure 帳戶和語音服務訂用帳戶之後，您必須登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech) ，並聯機您的訂用帳戶。

1. 從 Azure 入口網站取得您的語音服務訂用帳戶金鑰。
2. 登入 [自訂語音入口網站](https://aka.ms/custom-speech)。
3. 選取您需要處理的訂用帳戶，並建立語音專案。
4. 如果您想要修改您的訂用帳戶，請使用位於頂端導覽中的 **齒輪** 圖示。

## <a name="how-to-create-a-project"></a>如何建立專案

資料、模型、測試和端點等內容會組織成[自訂語音入口網站](https://speech.microsoft.com/customspeech)中的**專案**。 每個專案都是網域和國家/地區所特有。 例如，您可以針對在美國使用英文的電話中心建立專案。

若要建立您的第一個專案，請選取 [ **語音轉換文字]/[自訂語音**]，然後按一下 [ **新增專案**]。 遵循 wizard 提供的指示來建立您的專案。 建立專案之後，您應該會看到四個索引標籤： **資料**、 **測試**、 **定型**和 **部署**。 使用 [後續步驟](#next-steps) 中提供的連結來瞭解如何使用每個索引標籤。

> [!IMPORTANT]
> [自訂語音入口網站](https://aka.ms/custom-speech)最近已更新！ 如果您已在 CRIS.ai 入口網站或 Api 中建立先前的資料、模型、測試和已發佈的端點，您需要在新的入口網站中建立新的專案，以連接這些舊的實體。

## <a name="next-steps"></a>後續步驟

* [準備及測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
