---
title: 不支援的語言部署-自訂翻譯
titleSuffix: Azure Cognitive Services
description: 本文說明如何在 Azure 認知服務自訂翻譯中部署不支援的語言組。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837442"
---
# <a name="unsupported-language-deployments"></a>不支援的語言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

隨著 Microsoft Translator Hub 即將淘汰，Microsoft 將會解除部署所有目前透過中樞部署的模型。 其中有許多模型部署在自訂翻譯中不支援其語言配對的中樞。  我們不希望使用者在這種情況下沒有任何要是可以翻譯其內容。

我們現在有一個程式，可讓您透過自訂翻譯部署不受支援的模型。  此程式可讓您使用最新的 V3 API 繼續翻譯內容。  這些模型將會主控，直到您選擇解除部署它們，或自訂翻譯中的語言組變成可用為止。  本文說明以不支援的語言組來部署模型的程式。

## <a name="prerequisites"></a>先決條件

為了讓您的模型成為部署的候選項目，它們必須符合下列準則：
* 包含模型的專案必須已經從中樞遷移至使用遷移工具的自訂翻譯。  您可以在[這裡](how-to-migrate.md)找到遷移專案和工作區的進程。
* 當發生遷移時，模型必須處於已部署狀態。  
* 在自訂翻譯中，模型的語言組必須是不支援的語言組。  語言在語言中支援或來自英文，但配對本身不包含英文，這是不支援的語言部署的候選項目。  例如，法文到德文語言組的中樞模型會被視為不受支援的語言組，即使法文到英文，而且英文至德文也是支援的語言組。

## <a name="process"></a>Process
當您從中樞遷移模型作為部署的候選項目之後，您可以移至工作區的 [**設定**] 頁面，然後在頁面結尾處滾動到您將會看到**不支援的 Translator Hub**訓練區段。  只有當您的專案符合上述必要條件時，才會顯示此區段。

![如何從中樞移轉](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

在 [**不支援的翻譯工具中樞**訓練選擇 **] 頁面**中，[未要求的定型] 索引標籤包含適用于部署的模型。  選取您想要部署的模型並提交要求。   在4月30日的部署期限之前，您可以選取您想要部署的多個模型。
 
![如何從中樞移轉](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交之後，模型就無法再于 [未**要求**的定型] 索引標籤上使用，而會顯示在 [要求的**定型] 索引**標籤上。 您隨時都能觀看所要求的訓練。

![如何從中樞移轉](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>下一步

一旦中樞已解除委任，而且所有模型都已解除部署，您選取要部署的模型就會儲存。  您可以在5月24日前提交要求，以部署不支援的模型。  我們將在6月15日部署這些模型，在這之後，即可透過 Translator V3 API 存取它們。  此外，在7月1日前，將可透過 V2 API 取得。  

如需有關中樞淘汰中重要日期的詳細資訊，請參閱[這裡](https://www.microsoft.com/translator/business/hub/)。
部署之後，將會套用一般的裝載費用。  如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。  

不同于標準自訂翻譯模型，中樞模型只會在單一區域中提供，因此不會套用多區域裝載費用。  一旦部署之後，您就能夠隨時透過遷移的自訂翻譯專案，解除部署您的中樞模型。

## <a name="next-steps"></a>後續步驟

- [將模型定型](how-to-train-model.md)。
- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
