---
title: 不支援的語言部署 - 自訂轉換器
titleSuffix: Azure Cognitive Services
description: 本文介紹如何在 Azure 認知服務自訂轉換器中部署不支援的語言對。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837442"
---
# <a name="unsupported-language-deployments"></a>不支援的語言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

隨著 Microsoft 翻譯中心即將停用，Microsoft 將取消部署當前通過該中心部署的所有模型。 你們中的許多人在 Hub 中部署了模型，這些模型的語言對在自訂轉換器中不受支援。  我們不希望處於這種情況的使用者無法翻譯其內容。

現在，我們有一個流程，允許您通過自訂轉換器部署不支援的模型。  此過程使您能夠繼續使用最新的 V3 API 翻譯內容。  這些模型將被託管，直到您選擇取消部署它們或語言對在自訂翻譯中可用。  本文介紹了部署具有不支援的語言對的模型的過程。

## <a name="prerequisites"></a>Prerequisites

為了使模型成為部署的候選者，它們必須滿足以下條件：
* 包含模型的專案必須使用遷移工具從中心遷移到自訂轉換器。  遷移專案和工作區的過程[可以在這裡](how-to-migrate.md)找到。
* 遷移發生時，模型必須處於已部署的狀態。  
* 模型的語言對必須是自訂翻譯器中不受支援的語言對。  支援英語或從英語支援語言，但該對本身不包含英語的語言對，是不支援的語言部署的候選語言。  例如，法語到德語對的 Hub 模型被視為不受支援的語言對，即使法語到英語和英語到德語都支援語言對。

## <a name="process"></a>Process
從計畫部署的"中心"遷移模型後，可以通過訪問工作區的 **"設置"** 頁並滾動到頁面末尾，在頁面末尾看到**不支援的"翻譯中心培訓"** 部分來查找模型。  僅當專案符合上述先決條件時，才會顯示此部分。

![如何從中樞移轉](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

在 **"不支援的翻譯中心培訓"** 選擇頁中，"**未請求培訓"** 選項卡包含符合部署條件的模型。  選擇要部署的模型並提交請求。   在 4 月 30 日部署截止時間之前，您可以選擇所需的部署模型。
 
![如何從中樞移轉](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交後，該模型將不再在 **"未請求的培訓"** 選項卡上可用，而是顯示在 **"請求訓練"** 選項卡上。 您可以隨時查看您請求的培訓。

![如何從中樞移轉](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>下一步

一旦中心停用並且所有模型都未部署，您為部署選擇的模型將保存。  您必須在 5 月 24 日之前提交部署不支援的模型的請求。  我們將在 6 月 15 日部署這些模型，屆時它們將通過轉換器 V3 API 進行訪問。  此外，它們將通過 V2 API 提供，直到 7 月 1 日。  

有關中心棄用中重要日期的詳細資訊，[請在此處](https://www.microsoft.com/translator/business/hub/)檢查 。
部署後，將收取正常的託管費用。  有關詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。  

與標準自訂轉換器模型不同，集線器模型將僅在單個區域中可用，因此不會收取多區域託管費用。  部署後，您可以隨時通過遷移的自訂轉換器專案取消部署中心模型。

## <a name="next-steps"></a>後續步驟

- [訓練模型](how-to-train-model.md)。
- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
