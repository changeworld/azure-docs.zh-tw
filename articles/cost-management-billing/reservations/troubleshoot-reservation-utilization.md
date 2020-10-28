---
title: 針對 Azure 保留使用率進行疑難排解
description: 本文可協助您了解在 Azure 入口網站中顯示無或零 (0) 使用率的 Azure 保留，並針對其進行疑難排解。 本文也會說明不相符的使用率。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207423"
---
# <a name="troubleshoot-reservation-utilization"></a>針對保留使用量進行疑難排解

本文可協助您了解在 Azure 入口網站中顯示無或零 (0) 使用率的 Azure 保留，並針對其進行疑難排解。 本文也會說明不相符的使用率。

## <a name="symptoms"></a>徵兆

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [保留]。
1. 在保留清單中，請查看 [使用率 (%)] 資料行中某個保留的使用率值。 此值可能是 0%。
1. 選取保留。
1. 在 [保留概觀] 頁面上，圖中的已使用百分比可能與保留清單中顯示的值不相符。

## <a name="cause"></a>原因

Azure 入口網站中的 [使用率 (%)] 資料行會顯示當日的值。 此值的計算方式為從資源執行所在處抵達的使用量資料。 Azure 會使用使用量來計算使用率百分比。

某些資源報告使用量的速度會慢於其他資源。 此外，某些產品類型 (例如，SQL Database) 會很慢才報告其使用量資料。

此延遲可能會導致使用率計算所顯示的值低於實際使用量。 時間長達一天時，差異就會很大。 在這類情況下，如果 Azure 無法取得四到八個小時的使用量資料，則會將值計算為 0%。 系統會顯示 0% 的值是因為使用量資料尚未抵達，而且保留似乎未將權益套用至任何資源。

當使用量資料抵達時，值就會朝正確百分比變化。 收集到所有使用量資料後，就能判斷正確的值，並在圖中正確顯示。

## <a name="solution"></a>解決方法

如果您發現使用率值不符合您的預期，請檢閱圖表以充分了解實際使用率。 若為超過兩天的任何時間點，其值應該都會正確。 7 天到 30 天的較長期平均值應該會是正確的。

## <a name="next-steps"></a>後續步驟

- 如需如何管理保留的詳細資訊，請參閱[管理 Azure 資源的保留](manage-reserved-vm-instance.md)。