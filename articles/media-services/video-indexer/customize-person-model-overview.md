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
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047040"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在影片索引器中自訂人員模型

影片索引子支援您影片中的名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 影片索引子無法辨識的臉部仍會偵測到，但會保留未命名。 客戶可以建立自訂人員模型，並啟用影片索引子來辨識預設無法辨識的臉部。 客戶可以將人員的名稱與人臉的影像檔配對，以建立這些人員模型。  

如果您的帳戶已經考慮至不同的使用案例，您可以從每個帳戶建立多個人員模型來獲益。 例如，如果您帳戶中的內容要排序為不同的通道，您可能會想要為每個通道建立個別的人員模型。 

> [!NOTE]
> 每個人員模型最多支援1000000人，且每個帳戶都有50個人員模型的限制。 

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 將影片的新臉部定型，以更新與影片相關聯的特定自訂模型。 

如果您不需要多個人員模型支援，在上傳/編製索引或重新編製索引時，請勿將人員模型識別碼指派給您的視訊。 在此情況下，影片索引子會使用您帳戶中的預設人員模型。 

您可以使用影片索引子網站來編輯在影片中偵測到的臉部，並在您的帳戶中管理多個自訂人員模型，如 [使用網站自訂人員模型](customize-person-model-with-website.md) 主題所述。 您也可以使用 API，如 [使用 Api 自訂人員模型](customize-person-model-with-api.md)中所述。
