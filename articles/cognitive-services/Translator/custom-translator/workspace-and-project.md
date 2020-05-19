---
title: 什麼是工作區和專案？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 本文將說明工作區和專案之間的差異，以及自訂翻譯服務的專案類別和標籤。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a67e199af1ee6e68f00dbde9a0a43d02e4c7d638
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588661"
---
# <a name="what-is-a-custom-translator-workspace"></a>什麼是自訂翻譯工具工作區？

工作區是用來撰寫和建置自訂翻譯系統的工作區域。 工作區可包含多個專案、模型和文件。 您在自訂翻譯工具中的所有工作都會在某個特定的工作區內執行。

工作區供您和受邀進入工作區的人員私自使用。 未受邀的人員無法存取您的工作區內容。 您可以邀請人數不限的人員進入您的工作區，並可隨時修改或移除其存取權。 您也可以建立新的工作區。 根據預設，工作區不會包含您在其他工作區中的任何專案或文件。

## <a name="what-is-a-custom-translator-project"></a>什麼是自訂翻譯工具專案？

專案是模型、文件和測試的包裝函式。 每個專案都會自動包含上傳到該工作區且具有正確語言組的所有文件。 例如，如果您同時有英文翻譯成西班牙文的專案和西班牙文翻譯成英文的專案，這兩個專案將會包含相同的文件。 每個專案都有其相關聯的 CategoryID，供查詢 [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 的翻譯時使用。 CategoryID 是一個參數，用來從使用 Custom Translator 建置的自訂系統取得翻譯。

## <a name="project-categories"></a>專案類別

類別會識別專案的領域 – 您要使用的術語和樣式所屬的區域。 請選擇與您的文件最為相關的類別。 在某些情況下，您所選擇的類別會直接影響到自訂翻譯工具的行為。

我們有兩組基準模型。 分別是「一般」和「技術」。 如果選取**技術**類別，就會使用「技術」基準模型。 如果選取任何其他類別，則會使用「一般」基準模型。 「技術」基準模型也能在技術領域良好運作，但如果用來翻譯的句子不屬於技術領域，其品質就會較差。 我們會建議客戶，只有在句子完全落在技術領域時，才選取「技術」類別。

在相同的工作區中，您可以為不同類別中的相同語言組建立專案。 自訂翻譯工具可避免建立具有相同語言組和類別的重複專案。 將標籤套用至專案，可讓您避免受到這項限制。 除非您要為多個用戶端建置翻譯系統，否則請勿使用標籤，因為將唯一標籤新增至您的專案，將會反映在您的專案 CategoryID 中。

## <a name="project-labels"></a>專案標籤

自訂翻譯工具可讓您將專案標籤指派給專案。 專案標籤可區分多個具有相同語言組和類別的專案。 最佳做法是，在非必要時避免使用專案標籤。

專案標籤會作為 CategoryID 的一部分。 如果專案標籤保留為未設定，或是多個專案間做了相同的設定，則具有相同類別和*不同*語言組的專案將會共用相同的 CategoryID。 這種方法很有利，因為它可讓您或您的客戶在使用翻譯工具之間切換語言，而不需要擔心每個專案獨有的類別。

例如，如果我想要在技術領域中啟用英文翻譯成法文和法文翻譯成英文的翻譯，我會建立兩個專案：一個用於英文 -\> 法文，另一個用於法文 -\> 英文。 我會為兩者指定相同的類別 (技術)，並將專案標籤保留為空白。 這兩個專案的 CategoryID 會相符，因此我無須修改 CategoryID，即可直接對 API 查詢英文和法文翻譯。

如果您是語言服務提供者，而想要為多個客戶提供保留相同類別和語言組的不同模型，則使用專案標籤來區分客戶將是明智的決策。

## <a name="next-steps"></a>後續步驟

- 閱讀[定型和模型](training-and-model.md)，以了解如何有效地建置翻譯模型。
