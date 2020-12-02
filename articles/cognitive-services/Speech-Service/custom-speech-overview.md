---
title: 自訂語音總覽-語音服務
titleSuffix: Azure Cognitive Services
description: 自訂語音是一組線上工具，可讓您針對應用程式、工具和產品評估及改善 Microsoft 語音轉換文字的精確度。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 96b8e4031520e1a0540fca7d84b3f9ae1cca9c22
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490341"
---
# <a name="what-is-custom-speech"></a>什麼是自訂語音？

[自訂語音](https://aka.ms/customspeech) 是一組以 UI 為基礎的工具，可讓您針對應用程式和產品評估及改善 Microsoft 語音轉換文字的精確度。 開始使用的只是少數的測試音訊檔案。 遵循本文中的連結，開始建立自訂的語音轉換文字體驗。

## <a name="whats-in-custom-speech"></a>什麼是自訂語音？

您需要有 Azure 帳戶和語音服務訂用帳戶，才能使用自訂語音進行任何動作。 擁有帳戶之後，您就可以準備您的資料、定型和測試您的模型、檢查辨識品質、評估精確度，並最終部署和使用自訂的語音轉換文字模型。

此圖表會反白顯示組成 [自訂語音入口網站](https://aka.ms/customspeech)的部分。 您可以使用下列連結來深入瞭解每個步驟。

![強調組成自訂語音入口網站之元件的圖表。](./media/custom-speech/custom-speech-overview.png)

1. [訂閱並建立專案](#set-up-your-azure-account)。 建立 Azure 帳戶，並訂閱語音服務。 此整合訂用帳戶可讓您存取語音轉換文字、文字轉換語音、語音翻譯和 [自訂語音入口網站](https://speech.microsoft.com/customspeech)。 然後使用您的語音服務訂用帳戶來建立您的第一個自訂語音專案。

1. [上傳測試資料](./how-to-custom-speech-test-and-train.md)。  (音訊檔案上傳測試資料) ，以針對您的應用程式、工具和產品評估 Microsoft 語音轉換文字產品。

1. [檢查辨識品質](how-to-custom-speech-inspect-data.md)。 使用 [自訂語音入口網站](https://speech.microsoft.com/customspeech) 播放上傳的音訊，並檢查測試資料的語音辨識品質。 如需量化度量的詳細資料，請參閱 [檢查資料](how-to-custom-speech-inspect-data.md)。

1. [評估及改善精確度](how-to-custom-speech-evaluate-data.md)。 評估及改善語音轉換文字模型的精確度。 [自訂語音入口網站](https://speech.microsoft.com/customspeech)會提供 *單字錯誤率*，您可以用它來判斷是否需要額外的定型。 如果您對精確度感到滿意，您可以直接使用語音服務 Api。 如果您想要透過相對平均5% 到20% 來改善精確度，請使用入口網站中的 [ **定型** ] 索引標籤上傳其他定型資料，例如人為標記的文字記錄和相關文字。

1. [定型和部署模型](how-to-custom-speech-train-model.md)。 提供書面文字記錄 (10 到1000小時) 和相關文字 ( # B0 200 MB) 和您的音訊測試資料，以改善語音轉換文字模型的精確度。 這種資料有助於定型語音轉換文字模型。 定型之後，請重新測試。 如果您對結果感到滿意，您可以將模型部署至自訂端點。

## <a name="set-up-your-azure-account"></a>設定您的 Azure 帳戶

您必須擁有 Azure 帳戶和語音服務訂用帳戶，才能使用 [自訂語音入口網站](https://speech.microsoft.com/customspeech) 來建立自訂模型。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](overview.md#try-the-speech-service-for-free)。

> [!NOTE]
> 請務必建立標準 (S0) 訂用帳戶。 不支援免費的 (F0) 訂閱。

在您建立 Azure 帳戶和語音服務訂用帳戶之後，您必須登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech) ，並聯機您的訂用帳戶。

1. 登入 [自訂語音入口網站](https://aka.ms/custom-speech)。
1. 選取您需要使用的訂用帳戶，並建立語音專案。
1. 如果您想要修改您的訂用帳戶，請選取頂端功能表中的 [齒輪] 按鈕。

## <a name="how-to-create-a-project"></a>如何建立專案

資料、模型、測試和端點等內容會組織成 [自訂語音入口網站](https://speech.microsoft.com/customspeech)中的 *專案*。 每個專案都是網域和國家/地區所特有。 例如，您可以為使用美國英文的電話中心建立專案。

若要建立您的第一個專案，請選取 [ **語音轉換文字]/[自訂語音**]，然後選取 [ **新增專案**]。 遵循 wizard 提供的指示來建立您的專案。 建立專案之後，您應該會看到四個索引標籤： **資料**、 **測試**、 **定型** 和 **部署**。 使用 [後續步驟](#next-steps) 中提供的連結來瞭解如何使用每個索引標籤。

> [!IMPORTANT]
> [自訂語音入口網站](https://aka.ms/custom-speech)最近已更新！ 如果您已在 CRIS.ai 入口網站或 Api 中建立先前的資料、模型、測試和已發佈的端點，您需要在新的入口網站中建立新的專案，以連接這些舊的實體。

## <a name="model-lifecycle"></a>模型生命週期

自訂語音會使用 *基底模型* 和 *自訂模型*。 每種語言都有一或多個基底模型。 一般來說，當新的語音模型發行至一般語音服務時，它也會匯入自訂語音服務作為新的基底模型。 每隔3到6個月更新一次。 舊版模型通常會在一段時間內變得較少，因為最新的模型通常會有較高的精確度

相反地，自訂模型的建立方式是將所選的基底模型調整為特定的客戶案例。 當您有符合需求的自訂模型之後，您可以繼續使用特定的自訂模型。 但是，我們建議您定期更新至最新的基底模型，並使用額外的資料重新定型。 

其他與模型生命週期相關的重要詞彙包括：

* 調整 **：使用** 文字資料和/或音訊資料，取得基底模型並將它自訂至您的網域/案例。
* **解碼**：使用模型並執行語音辨識 (將音訊解碼成文字) 。
* **端點**：基底模型或自訂模型的使用者特定部署， *僅* 供指定的使用者存取。

### <a name="expiration-timeline"></a>到期時間軸

由於新的模型和新的功能可供使用，而較舊的模型已淘汰，請參閱下列適用于模型和端點到期的時間軸：

**基底模型** 

* 調整：一年提供。 匯入模型之後，就可以使用一年來建立自訂模型。 一年之後，必須從較新的基底模型版本建立新的自訂模型。  
* 解碼：匯入後兩年提供。 因此，您可以建立端點，並使用批次轉譯搭配此模型兩年。 
* 端點：可在與解碼相同的時間軸上使用。

**自訂模型**

* 解碼：建立模型之後的兩年都有提供。 如此一來，您就可以使用自訂模型兩年 (批次/即時/測試) 建立之後。 在兩年之後， *您應該* 重新定型模型，因為基底模型通常已被取代為可進行調整。  
* 端點：可在與解碼相同的時間軸上使用。

當基底模型或自訂模型過期時，它一律會切換回 *最新的基底模型版本*。 因此，您的實施將永遠不會中斷，但如果自訂模型達到到期時間， *您的特定資料* 可能會變得較不精確。 您可以在自訂語音入口網站中的下列位置看到模型的到期日：

* 模型定型摘要
* 模型定型詳細資料
* 部署摘要
* 部署詳細資料

您也可以透過 [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) 和 [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) 自訂語音 api，在 `deprecationDates` JSON 回應中的屬性下檢查到期日期。

請注意，您可以在自訂語音入口網站的 [部署] 區段中變更端點所使用的模型，或透過自訂語音 API，在不需要停機的情況下，在自訂語音端點上升級模型。

## <a name="next-steps"></a>後續步驟

* [準備及測試您的資料](./how-to-custom-speech-test-and-train.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估及改善模型精確度](how-to-custom-speech-evaluate-data.md)
* [定型和部署模型](how-to-custom-speech-train-model.md)