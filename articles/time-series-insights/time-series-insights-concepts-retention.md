---
title: 瞭解環境中的數據保留 - Azure 時間序列洞察 |微軟文件
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380680"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>瞭解 Azure 時間序列的解中的資料保留

本文介紹了影響 Azure 時間序列見解環境中數據保留的兩個主要設置。

## <a name="video"></a>影片

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>以下視頻總結了時間序列見解數據保留以及如何規劃。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

每個 Azure 時間序列見解環境都有一個控制**數據保留時間的**設置。 可設定的值為 1 到 400 天。 根據環境存儲容量或保留期限(以先到者為準)刪除數據。

此外,Azure 時間序列見解環境具有**超出行為設定的儲存限制**。 當達到環境的最大容量時,它控制入口和清除行為。 設定時有兩種行為可供選擇:

- **清除舊資料** (預設)  
- **暫停輸入**

> [!NOTE]
> 根據預設，建立新環境時，保留期會設定為**清除舊資料**。 在時間序列見解環境的 **「配置」** 頁上,可以使用 Azure 門戶在創建時間後根據需要切換此設定。
> * 關於如何設定保留政策的資訊,請閱讀[「時間序列」 的設定保留](time-series-insights-how-to-configure-retention.md)。

下面將更詳細地介紹這兩個數據保留策略。

## <a name="purge-old-data"></a>清除舊資料

- **清除舊資料**是 Azure 時間序列見解環境的預設設定。  
- 當使用者希望始終在其時間序列見解環境中擁有*其最新資料*時,最好**清除舊資料**。
- 一旦達到環境的限制(保留時間、大小或計數,以先到者為準),**清除舊數據**設置*將清除*資料。 保留期預設值為 30 天。
- 首先清除最舊的引入數據("先出先"方法)。

### <a name="example-one"></a>範例一

考慮具有保留行為 [繼續輸入並清除舊資料]**** 的範例環境：

**數據保留時間**設置為 400 天。 [容量]**** 是設定為 S1 單位，其中包含 30 GB 的總容量。 假設傳入的資料平均每天會累積至 500 MB。 在給定的資料傳入速率下，此環境只能將資料保留 60 天，因為會在 60 天時達到容量上限。 傳入資料會累積為：500 MB 每天 x 60 天 = 30 GB。

在第 61 天,環境顯示最新鮮的數據,但清除最舊的數據,超過 60 天。 清除動作可釋出空間供傳入的新資料使用，因此可以繼續瀏覽新資料。 如果使用者希望將資料保留更久一點，可以透過增加額外單位容量以提高環境的資料容納量，或減少傳入的資料量。  

### <a name="example-two"></a>範例 2

考慮也設定 [繼續輸入並清除舊資料]**** 保留行為的環境。 在這個範例中，[資料保留時間]**** 是設定為較低的值：180 天。 [容量]**** 是設定為 S1 單位，其中包含 30 GB 的總容量。 為將資料儲存 180 天，每日輸入量不能超過 0.166 GB (166 MB)。  

每當此環境的每日入侵率超過每天 0.166 GB 時,數據將無法存儲 180 天,因為某些數據將被清除。 請考慮此相同環境在忙碌的時間範圍內會發生什麼狀況。 假設環境的入口率可能增加到平均每天 0.189 GB。 在該忙碌時間範圍內，約可保留 158 天的資料 (30 GB/0.189 = 158.73 保留天數)。 此時間短於所需的資料保留時間範圍。

## <a name="pause-ingress"></a>暫停輸入

- 「**暫停入口」** 設定旨在確保在保留期之前達到大小和計數限制時不會清除資料。  
- **暫停入口**為使用者提供了額外的時間,在數據被清除之前,由於違反保留期,增加其環境的容量。
- 它有助於保護您免受數據丟失,但如果入口在事件源的保留期之後暫停,則可以為丟失最新數據創造機會。
- 但是,一旦達到環境的最大容量,環境就會暫停數據入口,直到發生以下其他操作:

   - 您可以增加環境的最大容量,以添加更多縮放單位,如[「如何縮放時間序列見解」環境](time-series-insights-how-to-scale-your-environment.md)中所述。
   - 達到數據保留期並清除數據,使環境低於其最大容量。

### <a name="example-three"></a>範例三

考慮保留期行為設定為**暫停輸入**的環境。 在此範例中，[資料保留期間]**** 設定為 60 天。 **容量**設置為 S1 的三 (3) 個單位。 假設此環境每天輸入 2 GB 資料。 在此環境中，一旦達到最大容量就會暫停輸入。

此時,環境顯示相同的數據集,直到入口恢復或**啟用繼續進入**(這將清除較舊的數據,為新數據騰出空間)。

恢復繼續輸入時：

- 資料會依事件來源收到資料的順序輸入
- 系統會依據事件的時間戳記為事件編製索引，除非您已超出事件來源的保留原則。 如需有關事件來源保留設定的詳細資訊，請參閱[事件中樞常見問題集](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> 您應該設定警示，以提供可協助避免暫停輸入資料的通知。 因為 Azure 事件來源的保留期預設為 1 天，所以是有可能會遺失資料的。 因此，輸入暫停之後，除非您採取其他動作，否則就可能會遺失最新資料。 您必須增加容量，或將行為切換為 [清除舊資料]****，以避免發生資料遺失。

當事件中樞受到影響時，請考慮調整 [訊息保留期]**** 屬性，以在「時間序列深入解析」中發生暫停輸入時，將資料遺失機率降到最低。

[![事件中樞訊息保留。](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

如果事件來源 ()`timeStampPropertyName`上未 配置任何屬性,則時序見解預設為到達事件中心作為 X 軸的時間戳。 如果`timeStampPropertyName`配置為其他內容,則當分析事件時,環境將查`timeStampPropertyName`找 數據包中配置的。

閱讀[如何擴展時間序列見解環境](time-series-insights-how-to-scale-your-environment.md)以擴展環境以適應額外容量或增加保留時間長度。

## <a name="next-steps"></a>後續步驟

- 有關配置或更改數據保留設置的資訊,請查看[「時間序列見解」中的設定保留。](time-series-insights-how-to-configure-retention.md)

- 在[Azure 時間序列的解題解決延遲的問題](time-series-insights-environment-mitigate-latency.md)。
