---
title: 什麼是自訂視覺服務？
titleSuffix: Azure Cognitive Services
description: 了解如何使用自訂視覺服務在 Azure 雲端中建置自訂影像分類器。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 79ecb801e1b4d0fa96ca7ae06223fc231cbf12e6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129879"
---
# <a name="what-is-custom-vision"></a>什麼是自訂視覺服務？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 自訂視覺是一項認知服務，可讓您建置、部署和改善自己的影像分類器。 影像分類器是一項 AI 服務，可根據影像的視覺特性對影像套用標籤 (代表_類別_)。 不同於[電腦視覺](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)服務，自訂視覺可讓您指定所要套用的標籤。

## <a name="what-it-does"></a>作用

自訂視覺服務會使用機器學習演算法對影像套用標籤。 身為開發人員的您必須提交具備和缺少相關特性的影像群組。 您必須在提交時自行為影像加上標籤。 然後，演算法會針對這項資料進行訓練，並藉由對這些相同的影像進行自我測試，計算其本身的精確度。 演算法經過訓練後，您可以測試、重新訓練，最終根據您的應用程式需求用它來分類新的影像。 您也可以匯出模型本身以供離線使用。

### <a name="classification-and-object-detection"></a>分類和物件偵測

自訂視覺功能可以細分成兩項功能。 **影像分類**會對影像套用一或多個標籤。 **物件偵測**也相類似，但它還會傳回影像中已套用的標籤所在位置的座標。

### <a name="optimization"></a>Optimization

自訂視覺服務已經過最佳化，可快速辨識影像之間的差異，因此您可以少量的資料開始建立您的模型原型。 一般而言，每個標籤 50 個影像是不錯的起點。 不過，此服務並未具備偵測影像中些微差異的最佳效能 (例如，在品保案例中偵測出微小的裂縫或凹痕)。

此外，您可以從數種不同針對特定題材內容的影像進行最佳化的自訂視覺演算法中擇一使用&mdash;例如，地標或零售項目。 如需詳細資訊，請參閱[建置分類器](getting-started-build-a-classifier.md)指南。

## <a name="what-it-includes"></a>包含內容

自訂視覺服務可作為一組的原生 SDK，也可透過[自訂視覺服務首頁](https://customvision.ai/)上的 Web 介面來使用。 您可以使用上述一或兩種介面來建立、測試和訓練模型。

![Chrome 瀏覽器視窗中的自訂視覺服務首頁](media/browser-home.png)

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

和所有認知服務一樣，使用自訂視覺服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

請依照[建置分類器](getting-started-build-a-classifier.md)指南在 Web 上開始使用自訂視覺，或完成[影像分類教學課程](quickstarts/image-classification.md)以使用程式碼實作基本案例。
