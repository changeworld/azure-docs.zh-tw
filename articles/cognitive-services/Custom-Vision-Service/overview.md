---
title: 什麼是自訂視覺服務？
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Azure 自訂視覺服務，在 Azure 雲端中建置自訂的 AI 影像分類器和物件偵測器。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 影像辨識, 影像識別工具, 影像辨識應用程式, 自訂視覺
ms.openlocfilehash: 7274fed961c4bbac8841e1c741f54031614cea5c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562985"
---
# <a name="what-is-custom-vision"></a>什麼是自訂視覺服務？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 自訂視覺是一項影像辨識服務，可讓您建置、部署和改善自己的影像識別工具。 影像識別工具可根據影像的視覺特性對影像套用標籤 (代表類別或物件)。 不同於[電腦視覺](../computer-vision/overview.md)服務，自訂視覺可讓您指定標籤，並定型自訂模型來偵測標籤。

## <a name="what-it-does"></a>作用

自訂視覺服務會使用機器學習演算法來分析影像。 身為開發人員的您必須提交具備和缺少相關特性的影像群組。 您必須在提交時自行為影像加上標籤。 然後，演算法會針對這項資料進行訓練，並藉由對這些相同的影像進行自我測試，計算其本身的精確度。 一旦演算法定型之後，您就可以測試、重新定型，最後在影像辨識應用程式中使用此演算法來分類新的影像。 您也可以匯出模型本身以供離線使用。

### <a name="classification-and-object-detection"></a>分類和物件偵測

自訂視覺功能可以細分成兩項功能。 **影像分類** 會對影像套用一或多個標籤。 **物件偵測** 也相類似，但它還會傳回影像中已套用的標籤所在位置的座標。

### <a name="optimization"></a>Optimization

自訂視覺服務已經過最佳化，可快速辨識影像之間的差異，因此您可以少量的資料開始建立您的模型原型。 一般而言，每個標籤 50 個影像是不錯的起點。 不過，此服務並未具備偵測影像中些微差異的最佳效能 (例如，在品保案例中偵測出微小的裂縫或凹痕)。

此外，您可以從數種不同針對特定題材內容的影像進行最佳化的自訂視覺演算法中擇一使用&mdash;例如，地標或零售項目。 如需詳細資訊，請參閱[建置分類器](getting-started-build-a-classifier.md)或[建置物件偵測器](get-started-build-detector.md)指南。

## <a name="what-it-includes"></a>包含內容

自訂視覺服務可作為一組的原生 SDK，也可透過[自訂視覺網站](https://customvision.ai/)上的 Web 介面來使用。 您可以使用上述一或兩種介面來建立、測試和訓練模型。

![Chrome 瀏覽器視窗中的自訂視覺網站](media/browser-home.png)

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

和所有認知服務一樣，使用自訂視覺服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

遵循[建置分類器](getting-started-build-a-classifier.md)指南，以在入口網站上開始使用自訂視覺，或完成[用戶端程序庫快速入門](quickstarts/image-classification.md)以在程序碼中實作基本案例。