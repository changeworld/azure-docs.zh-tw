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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838303"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在影片索引器中自訂人員模型

視頻索引子支援視頻中的名人識別。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 視頻索引子無法識別的面仍被檢測到，但未命名。 客戶可以構建自訂人員模型，並使視頻索引子能夠識別預設情況下無法識別的面。 客戶可以通過將人員姓名與人臉的影像檔配對來構建這些人員模型。  

如果您的帳戶適合不同的用例，則可以從能夠創建每個帳戶的多個人員模型中獲益。 例如，如果帳戶中的內容要分到不同的通道中，則可能需要為每個通道創建單獨的 Person 模型。 

> [!NOTE]
> 每個人員模型最多支援 100 萬人，每個帳戶有 50 人模型的限制。 

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 為視頻訓練新面孔，更新視頻關聯的特定自訂模型。 

如果您不需要多個人員模型支援，在上傳/編製索引或重新編製索引時，請勿將人員模型識別碼指派給您的視訊。 在這種情況下，視頻索引子將在您的帳戶中使用預設的"人"模型。 

您可以使用視頻索引子網站編輯視頻中檢測到的人臉，並管理帳戶中的多個自訂人員模型，如[使用網站主題自訂人員模型](customize-person-model-with-website.md)中所述。 您還可以使用 API，如 [使用 API 自訂人員模型](customize-person-model-with-api.md)中所述。
