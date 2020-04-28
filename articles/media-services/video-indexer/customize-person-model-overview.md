---
title: 在影片索引器中自訂人員模型 - Azure
titleSuffix: Azure Media Services
description: 本文概要說明影片索引器中的人員模型是什麼，以及如何加以自訂。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73838303"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在影片索引器中自訂人員模型

影片索引子可支援您影片中的名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 仍會偵測到不是由影片索引子辨識的人臉，但會保留未命名。 客戶可以建立自訂人員模型，並啟用影片索引子來辨識預設無法辨識的臉部。 客戶可以藉由將人員的名稱與人臉的影像檔配對，來建立這些人員模型。  

如果您的帳戶已經考慮到不同的使用案例，您可以從每個帳戶建立多個人員模型獲益。 例如，如果您帳戶中的內容要排序成不同的通道，您可能會想要為每個頻道建立個別的人員模型。 

> [!NOTE]
> 每個人員模型最多支援1000000人，而每個帳戶都有50個人員模型的限制。 

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 針對影片訓練新臉部，會更新與影片相關聯的特定自訂模型。 

如果您不需要多個人員模型支援，在上傳/編製索引或重新編製索引時，請勿將人員模型識別碼指派給您的視訊。 在此情況下，影片索引子會在您的帳戶中使用預設人員模型。 

您可以使用影片索引子網站來編輯在影片中偵測到的臉部，以及管理您帳戶中的多個自訂人員模型，如[使用網站自訂人員模型](customize-person-model-with-website.md)主題中所述。 您也可以使用 API，如 [使用 Api 自訂人員模型](customize-person-model-with-api.md)中所述。
