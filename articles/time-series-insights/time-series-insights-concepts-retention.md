---
title: 瞭解環境中的資料保留-Azure 時間序列深入解析 |Microsoft Docs
description: 本文說明「Azure 時間序列深入解析」環境中，控制資料保留的兩個設定。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fd34595d5ea942602efc920904ff326fc203c088
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81380680"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>瞭解 Azure 時間序列深入解析中的資料保留

本文說明兩個主要設定，會影響 Azure 時間序列深入解析環境中的資料保留。

## <a name="video"></a>影片

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>下列影片將摘要說明時間序列深入解析資料保留以及如何進行規劃。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

您的每個 Azure 時間序列深入解析環境都有一個設定，可控制**資料保留時間**。 可設定的值為 1 到 400 天。 資料會根據環境儲存容量或保留期間來刪除（以先發生者為准）。

此外，您的 Azure 時間序列深入解析環境具有 [**超過儲存限制**] 的行為設定。 當達到環境的最大容量時，它會控制輸入和清除行為。 設定時有兩種行為可供選擇：

- **清除舊資料** (預設)  
- **暫停輸入**

> [!NOTE]
> 根據預設，建立新環境時，保留期會設定為**清除舊資料**。 在建立時間之後，您可以在時間序列深入解析環境的 [**設定**] 頁面上，使用 [Azure 入口網站]，視需要切換此設定。
> * 如需有關如何設定保留原則的詳細資訊，請參閱[在時間序列深入解析中設定保留](time-series-insights-how-to-configure-retention.md)。

下面會更詳細地說明這兩個數據保留原則。

## <a name="purge-old-data"></a>清除舊資料

- [**清除舊資料**] 是 Azure 時間序列深入解析環境的預設設定。  
- 當使用者想要在其時間序列深入解析環境中一律擁有*最新的資料*時，建議您**清除舊資料**。
- 一旦達到環境的限制（保留時間、大小或計數（以先發生者為准）時，[**清除舊資料**] 設定就會*清除*資料。 保留期預設值為 30 天。
- 最舊的內嵌資料會先清除（「先進先出」方法）。

### <a name="example-one"></a>範例一

考慮具有保留行為 [繼續輸入並清除舊資料]**** 的範例環境：

[**資料保留時間**] 設定為400天。 [容量]**** 是設定為 S1 單位，其中包含 30 GB 的總容量。 假設傳入的資料平均每天會累積至 500 MB。 在給定的資料傳入速率下，此環境只能將資料保留 60 天，因為會在 60 天時達到容量上限。 傳入資料會累積為：500 MB 每天 x 60 天 = 30 GB。

在61st 日，環境會顯示最不能的資料，但會清除超過60天的舊資料。 清除動作可釋出空間供傳入的新資料使用，因此可以繼續瀏覽新資料。 如果使用者希望將資料保留更久一點，可以透過增加額外單位容量以提高環境的資料容納量，或減少傳入的資料量。  

### <a name="example-two"></a>範例二

考慮也設定 [繼續輸入並清除舊資料]**** 保留行為的環境。 在這個範例中，[資料保留時間]**** 是設定為較低的值：180 天。 [容量]**** 是設定為 S1 單位，其中包含 30 GB 的總容量。 為將資料儲存 180 天，每日輸入量不能超過 0.166 GB (166 MB)。  

每當此環境的每日輸入速率超過每日 0.166 GB 時，將無法儲存180天的資料，因為某些資料會被清除。 請考慮此相同環境在忙碌的時間範圍內會發生什麼狀況。 假設環境的輸入速率可能會增加到每日平均 0.189 GB。 在該忙碌時間範圍內，約可保留 158 天的資料 (30 GB/0.189 = 158.73 保留天數)。 此時間短於所需的資料保留時間範圍。

## <a name="pause-ingress"></a>暫停輸入

- 「**暫停**輸入」設定的設計，是為了確保在其保留期限之前達到大小和計數限制時，不會清除資料。  
- **暫停**輸入可讓使用者在資料因保留期限違反而清除之前，額外增加其環境的容量。
- 它可協助保護您免于資料遺失，但如果輸入暫停超過事件來源的保留期間，可能會造成資料遺失的機會。
- 不過，一旦達到環境的最大容量，環境就會暫停資料輸入，直到發生下列其他動作為止：

   - 您會增加環境的最大容量，以新增更多縮放單位，如[如何調整您的時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)中所述。
   - 已達到資料保留期並清除資料，將環境帶入其最大容量。

### <a name="example-three"></a>範例三

考慮保留期行為設定為**暫停輸入**的環境。 在此範例中，[資料保留期間]**** 設定為 60 天。 **容量**設定為 S1 的三（3）個單位。 假設此環境每天輸入 2 GB 資料。 在此環境中，一旦達到最大容量就會暫停輸入。

此時，環境會顯示相同的資料集，直到輸入繼續，或直到**繼續**輸入為止（這會清除較舊的資料以騰出空間給新的資料）。

恢復繼續輸入時：

- 資料會依事件來源收到資料的順序輸入
- 系統會依據事件的時間戳記為事件編製索引，除非您已超出事件來源的保留原則。 如需有關事件來源保留設定的詳細資訊，請參閱[事件中樞常見問題集](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> 您應該設定警示，以提供可協助避免暫停輸入資料的通知。 因為 Azure 事件來源的保留期預設為 1 天，所以是有可能會遺失資料的。 因此，輸入暫停之後，除非您採取其他動作，否則就可能會遺失最新資料。 您必須增加容量，或將行為切換為 [清除舊資料]****，以避免發生資料遺失。

當事件中樞受到影響時，請考慮調整 [訊息保留期]**** 屬性，以在「時間序列深入解析」中發生暫停輸入時，將資料遺失機率降到最低。

[![事件中樞訊息保留。](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

如果事件來源（）上未設定任何屬性 `timeStampPropertyName` ，時間序列深入解析預設為以 X 軸到達事件中樞的時間戳記。 如果 `timeStampPropertyName` 設定為其他專案，環境會在剖析事件時，尋找 `timeStampPropertyName` 資料封包中所設定的。

瞭解[如何調整您的時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)，以調整您的環境以容納額外的容量或增加保留的長度。

## <a name="next-steps"></a>後續步驟

- 如需設定或變更資料保留設定的詳細資訊，請參閱[在時間序列深入解析中設定保留](time-series-insights-how-to-configure-retention.md)。

- 瞭解[Azure 時間序列深入解析中的緩和延遲](time-series-insights-environment-mitigate-latency.md)。
