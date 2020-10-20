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
ms.openlocfilehash: a5918adfb6724c984f4a59fda802e2f4c5cde7c1
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221191"
---
# <a name="unsupported-language-deployments"></a>不支援的語言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

在即將淘汰的 Microsoft Translator Hub 淘汰之後，Microsoft 將會解除部署目前透過中樞部署的所有模型。 許多人都在中樞部署了自訂翻譯中不支援語言組的模型。  在這種情況下，我們不想讓使用者翻譯其內容的要是。

我們現在有一個可讓您透過自訂翻譯部署不支援模型的進程。  此程式可讓您使用最新的 V3 API 繼續轉譯內容。  這些模型將會被裝載，直到您選擇解除部署這些模型或自訂翻譯中的語言組為止。  本文說明使用不支援的語言組來部署模型的程式。

## <a name="prerequisites"></a>Prerequisites

為了讓您的模型成為部署的候選項目，它們必須符合下列準則：
* 包含模型的專案必須已經使用「遷移工具」從中樞遷移至自訂翻譯。  您可以在 [這裡](how-to-migrate.md)找到遷移專案和工作區的流程。
* 進行遷移時，模型必須處於已部署狀態。  
* 模型的語言組必須是自訂翻譯中不受支援的語言組。  語言支援或自英文的語言組，但這組本身不包含英文，是不支援的語言部署的候選項目。  例如，法文至德文語言組的中樞模型會被視為不受支援的語言組，即使法文至英文，以及英文至德文都是支援的語言組。

## <a name="process"></a>處理序
當您從作為部署候選項目的中樞遷移模型之後，您可以移至工作區的 [ **設定** ] 頁面，並將其移至頁面的結尾，您會看到 **不支援的翻譯中樞** 訓練區段。  只有當您的專案符合上述必要條件時，才會出現此區段。

![強調 [不支援的翻譯中樞訓練] 區段的螢幕擷取畫面。](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

在 [ **不支援的翻譯中樞** 訓練 **選擇] 頁面** 中，[未要求的定型] 索引標籤包含適合部署的模型。  選取您要部署的模型並提交要求。   在4月30日部署期限之前，您可以選取想要部署的任意數量模型。
 
![顯示 [未請求的訓練] 索引標籤的螢幕擷取畫面。](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交之後，將無法 **再于 [** 未要求的訓練] 索引標籤上使用模型，而是會出現在 [ **要求** 的訓練] 索引標籤上。 您可以隨時查看要求的訓練。

![如何從中樞移轉](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>接下來要做什麼？

當中樞已解除委任並解除部署所有模型之後，您選取要部署的模型就會儲存。  您可以在5月24日前提交不支援模型的部署要求。  我們將于6月15日部署這些模型，屆時將可透過 Translator V3 API 存取這些模型。  此外，在7月1日之前，都可以透過 V2 API 使用它們。  

如需有關中樞淘汰的重要日期的詳細資訊，請參閱 [這裡](https://www.microsoft.com/translator/business/hub/)。
部署之後，將會收取一般裝載費用。  請參閱 [定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) 以取得詳細資料。  

不同于標準自訂翻譯模型，中樞模型只會在單一區域中提供，因此不會套用多區域裝載費用。  一旦部署之後，您就可以隨時透過遷移的自訂翻譯專案解除部署中樞模型。

## <a name="next-steps"></a>後續步驟

- [定型模型](how-to-train-model.md)。
- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
