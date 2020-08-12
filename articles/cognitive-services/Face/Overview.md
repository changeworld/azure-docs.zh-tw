---
title: 什麼是臉部辨識服務？
titleSuffix: Azure Cognitive Services
description: Azure 認知服務的臉部辨識服務會提供相關的演算法，用以偵測、辨識和分析影像中的人臉。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 061d6d1088358e5da0cb590fad5ed0a4cd9e422a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87834213"
---
# <a name="what-is-the-azure-face-service"></a>什麼是 Azure 臉部辨識服務？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure 認知服務的臉部辨識服務會提供相關的演算法，以偵測、辨識和分析影像中的人臉。 處理人臉資訊在許多不同的軟體案例中都是重要的能力。 舉例來說，這些案例包括安全性、自然使用者介面、影像內容分析和管理、行動應用程式及機器人。

臉部服務提供了幾個不同的功能，下列各節會有相關概述。

## <a name="face-detection"></a>臉部偵測

臉部辨識服務可偵測影像中的人臉，並傳回其位置的矩形座標。 臉部偵測也可以擷取一連串與臉部相關的屬性。 例如，姿勢、性別、年齡、頭部姿勢、臉部汗毛和眼鏡等。

> [!NOTE]
> [電腦視覺 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) 也提供臉部偵測功能。 如果您想要以臉部資料執行進一步的作業，請使用臉部辨識服務，這是本文所討論的服務。

![女性與男性影像，其臉部周圍繪有矩形，並顯示其年齡和性別](./Images/Face.detection.jpg)

如需臉部偵測的詳細資訊，請參閱[臉部偵測](concepts/face-detection.md)概念文章。 另請參閱[偵測 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 參考文件。

## <a name="face-verification"></a>臉部驗證

驗證 API 會對兩個偵測到的臉部執行驗證，或從一個偵測到的臉部向一個人員物件執行驗證。 實際上，它會評估兩張臉孔是否屬於同一人。 此功能在安全性案例中可能有其效用。 如需詳細資訊，請參閱[臉部辨識](concepts/face-recognition.md)概念指南或[驗證 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 參考文件。

## <a name="find-similar-faces"></a>尋找類似臉部

「尋找類似項目 API」會比較目標臉部和一組候選臉部，以尋找看起來與目標臉部相似的一小組臉部。 目前支援 matchPerson 和 matchFace 兩種工作模式。 matchPerson 模式會在使用[驗證 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 篩選出相同人員後，傳回類似的臉部。 matchFace 模式會忽略相同人員的篩選。 它會傳回不一定屬於同一人的類似候選臉部清單。

下列範例顯示目標臉部：

![面帶笑容的女性](./Images/FaceFindSimilar.QueryFace.jpg)

而這些影像是候選臉部：

![五個面帶笑容人員的影像。 影像 a 和 b 顯示同一人。](./Images/FaceFindSimilar.Candidates.jpg)

在尋找四個相似的臉部時，matchPerson 模式會傳回 a 和 b，因為它們顯示的是與目標臉部相同的人員。 matchFace 模式會傳回 a、b、c 和 d 四個候選項目，不過某些項目不是與目標相同的人員，或是相似度較低。 如需詳細資訊，請參閱[臉部辨識](concepts/face-recognition.md)概念指南或[尋找類似項目 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 參考文件。

## <a name="face-grouping"></a>臉部分組

群組 API 會根據相似度將一組陌生臉部分成數個群組。 每個群組都是與原始臉部集合不相連的適當子集。 一個群組中的所有臉部很可能屬於相同的人員。 一個人可以有多個不同的群組。 群組可由另一個因素來區分，例如表情。 如需詳細資訊，請參閱[臉部辨識](concepts/face-recognition.md)概念指南或[群組 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 參考文件。

## <a name="person-identification"></a>人員識別

識別 API 可用來識別對人員資料庫偵測出來的臉部。 對於相片管理軟體中的自動影像標記功能來說，此功能可能很實用。 您可以事先建立資料庫，然後隨著時間加以編輯。

下圖說明資料庫 `"myfriends"` 的範例。 每個群組最多可包含一百萬個不同的人員物件。 每個人員物件最多可以註冊 248 張臉。

![不同人員的網格，內含三個資料行，每個資料行有三個資料列的臉部影像](./Images/person.group.clare.jpg)

建立及訓練資料庫之後，您即可對新偵測到臉部的群組執行識別作業。 如果臉部識別為群組中的人員，則會傳回人員物件。

如需人員識別的詳細資訊，請參閱[臉部辨識](concepts/face-recognition.md)概念指南或[識別 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 參考文件。

## <a name="containers"></a>容器

藉由在更接近資料的位置安裝標準化的 Docker 容器，從而[使用臉部容器](face-how-to-install-containers.md)來偵測、辨識和識別臉部。

## <a name="sample-apps"></a>範例應用程式

下列應用程式範例說明幾種使用臉部辨識服務的方式：

- [臉部 API：Windows 用戶端程式庫和範例](https://github.com/Microsoft/Cognitive-Face-Windows)是一項 WPF 應用程式，可示範數個關於臉部偵測、分析和識別的案例。
- [FamilyNotes UWP 應用程式](https://github.com/Microsoft/Windows-appsample-familynotes)是一個通用 Windows 平台 (UWP) 應用程式，可在家庭記事分享案例中使用臉部識別以及語音、Cortana、筆跡和相機。

## <a name="data-privacy-and-security"></a>資料隱私權和安全性

和所有認知服務資源一樣，使用臉部服務的開發人員必須了解 Microsoft 對於客戶資料的政策。 如需詳細資訊，請參閱 Microsoft 信任中心的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)。

## <a name="next-steps"></a>後續步驟

請依照快速入門實作程式碼中的臉部偵測案例：

- [快速入門：使用 .NET SDK 和 C# 偵測影像中的臉部](quickstarts/csharp.md)。 另有其他語言可供使用。
