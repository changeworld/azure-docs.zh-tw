---
title: 使用自訂語音語音服務改善合成
titleSuffix: Azure Cognitive Services
description: 自訂語音是一組線上工具，可讓您為品牌建立可辨識且獨一無二的語音。 開始使用的只是少數音訊檔案和相關聯的轉譯。 遵循下列連結以開始建立自訂的語音轉換文字體驗。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 272367d5311952b45c73febe0e05b1ec2d225261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056681"
---
# <a name="get-started-with-custom-voice"></a>開始使用自訂語音

[自訂語音](https://aka.ms/customvoice) 是一組線上工具，可讓您為品牌建立可辨識且獨一無二的語音。 開始使用的只是少數音訊檔案和相關聯的轉譯。 遵循下列連結以開始建立自訂文字轉換語音體驗。

## <a name="whats-in-custom-voice"></a>自訂語音的功能

開始使用自訂語音之前，您需要 Azure 帳戶和語音服務訂用帳戶。 建立帳戶之後，您就可以準備您的資料、定型和測試您的模型、評估語音品質，最後部署您的自訂語音模型。

下圖強調使用 [自訂語音入口網站](https://aka.ms/customvoice)建立自訂語音模型的步驟。 若要深入瞭解，請使用連結。

![自訂語音架構圖表](media/custom-voice/custom-voice-diagram.png)

1. [訂閱和建立專案](#set-up-your-azure-account) -建立 Azure 帳戶，並建立語音服務訂用帳戶。 此整合訂用帳戶可讓您存取語音轉換文字、文字轉換語音、語音翻譯和自訂語音入口網站。 然後，使用您的語音服務訂用帳戶，建立您的第一個自訂語音專案。

2. [上傳資料](how-to-custom-voice-create-voice.md#upload-your-datasets) ：使用自訂語音入口網站或自訂語音 API 將資料上傳 (音訊和文字) 。 您可以從入口網站調查並評估發音分數和雜訊的比率。 如需詳細資訊，請參閱 [如何準備自訂語音的資料](how-to-custom-voice-prepare-data.md)。

3. 將[您的模型定型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model)–使用您的資料來建立自訂的文字轉換語音語音模型。 您可以使用不同的語言來定型模型。 定型之後，請測試您的模型，如果您對結果感到滿意，就可以部署模型。

4. [部署模型](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) -為您的文字轉換語音語音模型建立自訂端點，並將其用於您的產品、工具和應用程式中的語音合成。

## <a name="custom-neural-voices"></a>自訂神經語音

類神經語音自訂功能目前處於公開預覽狀態，僅限選取的客戶。 填寫此 [應用程式表單](https://go.microsoft.com/fwlink/?linkid=2108737) 以開始使用。

> [!NOTE]
> 做為 Microsoft 對設計負責任 AI 的承諾，我們的目的是要保護個人和社會的權利，並促進透明的人類電腦互動。 基於這個理由，自訂神經語音尚未提供給所有客戶。 只有在您的應用程式經過審核之後，且您已致力於配合我們的道德原則時，您才能獲得技術的存取權。 深入瞭解我們的 [應用程式管制流程](https://aka.ms/custom-neural-gating-overview)。

## <a name="set-up-your-azure-account"></a>設定您的 Azure 帳戶

您必須要有語音服務訂用帳戶，才能使用自訂語音入口網站來建立自訂模型。 請遵循下列指示，在 Azure 中建立語音服務訂用帳戶。 如果您沒有 Azure 帳戶，您可以註冊一個新的帳戶。  

當您建立 Azure 帳戶和語音服務訂用帳戶之後，您必須登入自訂語音入口網站，並聯機您的訂用帳戶。

1. 從 Azure 入口網站取得您的語音服務訂用帳戶金鑰。
2. 登入 [自訂語音入口網站](https://aka.ms/custom-voice)。
3. 選取您的訂用帳戶，並建立語音專案。
4. 如果您想要切換到其他語音訂用帳戶，請使用位於頂端導覽中的齒輪圖示。

> [!NOTE]
> 您必須先在 Azure 中建立 F0 或 S0 金鑰，才能使用服務。

## <a name="how-to-create-a-project"></a>如何建立專案

資料、模型、測試和端點等內容會組織成自訂語音入口網站中的 **專案** 。 每個專案都是國家/地區和您想要建立的語音性別所特有。 例如，您可以為撥接中心的聊天機器人建立女性語音的專案，該專案在美國 (en-us) 使用英文。

若要建立您的第一個專案，請選取 [ **文字轉換語音]/[自訂語音** ] 索引標籤，然後按一下 [ **新增專案**]。 遵循 wizard 提供的指示來建立您的專案。 建立專案之後，您會看到四個索引標籤： **資料**、 **定型**、 **測試**和 **部署**。 使用 [後續步驟](#next-steps) 中提供的連結來瞭解如何使用每個索引標籤。

> [!IMPORTANT]
> [自訂語音的入口網站](https://aka.ms/custom-voice)最近已更新！ 如果您已在 CRIS.ai 入口網站或 Api 中建立先前的資料、模型、測試和已發佈的端點，您需要在新的入口網站中建立新的專案，以連接這些舊的實體。

## <a name="next-steps"></a>後續步驟

- [準備自訂語音資料](how-to-custom-voice-prepare-data.md)
- [建立自訂語音](how-to-custom-voice-create-voice.md)
- [指南：錄製您的語音範例](record-custom-voice-samples.md)
