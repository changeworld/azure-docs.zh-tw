---
title: 使用自訂語音 - 語音服務改進合成
titleSuffix: Azure Cognitive Services
description: 自訂語音是一組線上工具，允許您為您的品牌創建可識別的、一種類型的聲音。 入門只需一小撮音訊檔和相關的轉錄。 按照以下連結開始創建自訂的語音到文本體驗。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: 786cf687e569af172eabfaa9068ae61ffd6b2988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221417"
---
# <a name="get-started-with-custom-voice"></a>開始使用自訂語音

[自訂語音](https://aka.ms/customvoice)是一組線上工具，允許您為您的品牌創建可識別的、一種類型的聲音。 入門只需一小撮音訊檔和相關的轉錄。 按照以下連結開始創建自訂文本到語音轉換體驗。

## <a name="whats-in-custom-voice"></a>自訂語音中有什麼？

在開始自訂語音之前，您需要 Azure 帳戶和語音服務訂閱。 創建帳戶後，可以準備資料、訓練和測試模型、評估語音品質並最終部署自訂語音模型。

下圖突出顯示了使用[自訂語音門戶](https://aka.ms/customvoice)創建自訂語音模型的步驟。 使用連結瞭解更多資訊。

![自訂語音體系結構圖](media/custom-voice/custom-voice-diagram.png)

1. [訂閱和創建專案](#set-up-your-azure-account)- 創建 Azure 帳戶並創建語音服務訂閱。 此統一訂閱允許您訪問語音到文本、文本到語音轉換、語音轉換和自訂語音門戶。 然後，使用語音服務訂閱，創建第一個自訂語音專案。

2. [上傳資料](how-to-custom-voice-create-voice.md#upload-your-datasets)- 使用自訂語音門戶或自訂語音 API 上傳資料（音訊和文本）。 通過門戶，您可以調查和評估發音分數和信噪比。 有關詳細資訊，請參閱[如何為自訂語音準備資料](how-to-custom-voice-prepare-data.md)。

3. [訓練模型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model)– 使用資料創建自訂文本到語音語音模型。 您可以用不同的語言訓練模型。 培訓後，測試模型，如果您對結果感到滿意，則可以部署模型。

4. [部署模型](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint)- 為文本到語音模型創建自訂終結點，並將其用於產品、工具和應用程式中的語音合成。

## <a name="custom-neural-voices"></a>自訂神經語音

神經語音自訂功能當前處於公共預覽版中，僅限於選定的客戶。 填寫此[申請表](https://go.microsoft.com/fwlink/?linkid=2108737)以開始。

> [!NOTE]
> 作為 Microsoft 設計負責任的 AI 承諾的一部分，我們的目的是保護個人和社會的權利，並促進透明的人機交互。 因此，自訂神經語音通常不能對所有客戶可用。 只有在您的應用程式經過審查並承諾根據我們的道德原則使用該技術後，您才能訪問該技術。 詳細瞭解我們的[應用程式澆注過程](https://aka.ms/custom-neural-gating-overview)。

## <a name="set-up-your-azure-account"></a>設置 Azure 帳戶

在使用自訂語音門戶創建自訂模型之前，需要語音服務訂閱。 按照這些說明在 Azure 中創建語音服務訂閱。 如果沒有 Azure 帳戶，則可以註冊新的帳戶。  

創建 Azure 帳戶和語音服務訂閱後，需要登錄到自訂語音門戶並連接訂閱。

1. 從 Azure 門戶獲取語音服務訂閱金鑰。
2. 登錄到[自訂語音門戶](https://aka.ms/custom-voice)。
3. 選擇訂閱並創建語音專案。
4. 如果要切換到其他語音訂閱，請使用位於頂部導航中的齒輪圖示。

> [!NOTE]
> 自訂語音服務不支援 30 天免費試用金鑰。 在使用服務之前，必須在 Azure 中創建 F0 或 S0 金鑰。

## <a name="how-to-create-a-project"></a>如何建立專案

資料、模型、測試和終結點等內容被組織到自訂語音門戶中的**專案中**。 每個專案都特定于國家/語言以及您要創建的聲音的性別。 例如，您可以為話務中心在美國使用英語的聊天機器人（en-US）創建女性語音專案。

要創建第一個專案，請選擇 **"文本到語音/自訂語音"** 選項卡，然後按一下 **"新專案**"。 按照嚮導提供的說明創建專案。 創建專案後，您將看到四個選項卡：**資料**、**培訓**、**測試**和**部署**。 使用["後續步驟"](#next-steps)中提供的連結瞭解如何使用每個選項卡。

> [!IMPORTANT]
> [自訂語音門戶](https://aka.ms/custom-voice)最近更新！ 如果在CRIS.ai門戶或使用 API 創建了以前的資料、模型、測試和已發佈的終結點，則需要在新門戶中創建新專案以連接到這些舊實體。

## <a name="next-steps"></a>後續步驟

- [準備自訂語音資料](how-to-custom-voice-prepare-data.md)
- [建立自訂語音](how-to-custom-voice-create-voice.md)
- [指南：錄製語音樣本](record-custom-voice-samples.md)
