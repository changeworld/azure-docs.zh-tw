---
title: 媒體保留單元-Azure |Microsoft Docs
description: 媒體保留單元可讓您調整媒體處理常式，並決定媒體處理工作的速度。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: df691f5b9e74432f683e52fbb896dd4d44efe2aa
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91622059"
---
# <a name="media-reserved-units"></a>媒體保留單元

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒體服務可讓您透過管理媒體保留單元 (Mru) 來調整媒體處理。 MRU 提供編碼媒體所需的額外計算容量。 Mru 數目決定媒體工作的處理速度，以及帳戶中可同時處理的媒體工作數目。 例如，如果您的帳戶有五個 Mru，而且有工作要處理，則可以同時執行五個媒體工作。 任何剩餘的工作將會排入佇列，並可在執行中的工作完成時，依序挑選以進行處理。 您布建的每個 MRU 都會產生容量保留，但不會提供您專用的資源。 在極高需求的時間內，您的所有 Mru 可能不會立即開始處理。

工作是在資產上個別運作的作業，例如適應性串流編碼。 當您提交工作時，媒體服務會負責將作業細分為個別的作業 (例如，將會與個別 Mru 相關聯的工作) 。

## <a name="choosing-between-different-reserved-unit-types"></a>選擇不同的保留單元類型

下表可協助您在不同編碼速度之間進行選擇時做出決定。  它會根據所用的 MRU，顯示7分鐘、1080p 影片的編碼持續時間。

|RU 類型|案例|7 分鐘 1080p 視訊的結果範例 |
|---|---|---|
| **S1**|單一位元速率編碼。 <br/>在 SD或如下解決方法的檔案、對時間不敏感、低成本。|使用「H264 單一位元速率 SD 16x9」進行編碼為單一位元速率 SD 解析度的檔，大約需要7分鐘。|
| **S2**|單一位元速率和多重位元速率編碼。<br/>SD 和 HD 編碼的一般使用方式。|具有「H264 單一位元速率720p」預設值的編碼需要大約6分鐘的時間。<br/><br/>具有「H264 多重位元速率720p」預設值的編碼需要大約12分鐘。|
| **S3**|單一位元速率和多重位元速率編碼。<br/>Full HD 和 4K 解析度影片。 對時間敏感、周轉時間更快的編碼。|具有「H264 單一位元速率1080p」預設值的編碼需要大約3分鐘。<br/><br/>具有「H264 多重位元速率 1080p」預設值的編碼需要大約 8 分鐘。|

> [!NOTE]
> 如果您沒有為您的帳戶布建 MRU，則會使用 S1 MRU 的效能來處理您的媒體工作，且會依序挑選工作。 系統不會保留任何處理容量，因此一項工作完成和下一次啟動之間的等候時間將取決於系統中的資源可用性。

## <a name="considerations"></a>考量

* 針對媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您以10個 S3 單位布建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。
* 針對沒有 Mru 的編碼工作，您的工作在佇列狀態中可能會花費的時間沒有上限，而且一次最多隻會執行一個工作。

## <a name="billing"></a>計費

系統會根據在您的帳戶中布建媒體保留單元的分鐘數來收費，不論是否有任何工作正在執行。 如需詳細說明，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。

## <a name="next-step"></a>後續步驟
[使用 CLI](media-reserved-units-cli-how-to.md) 
 調整媒體保留單元[分析](analyze-videos-tutorial-with-api.md)影片

## <a name="see-also"></a>另請參閱

* [配額和限制](limits-quotas-constraints.md)
