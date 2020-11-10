---
title: 針對 Azure 保留下載使用量詳細資料進行疑難排解
description: 本文可協助您了解為何無法在 Azure 入口網站中取得保留執行個體使用量詳細資料並進行疑難排解。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147316"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>針對 Azure 保留下載使用量詳細資料進行疑難排解

本文可協助您了解為何無法在 Azure 入口網站中取得保留執行個體使用量詳細資料並進行疑難排解。

## <a name="symptoms"></a>徵兆

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 [保留]。
1. 選取保留。
1. 在保留概觀頁面上，預設檢視會顯示過去七天的使用量。 您可以選取 [下載為 CSV] 以下載保留的使用量詳細資料。
1. 當您變更時間範圍時，[下載為 CSV] 選項會變成無法使用。
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="顯示 [下載為 CSV] 無法使用的範例" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>原因

由於技術方面的限制，Azure 不支援下載超過七天的資料。 對於具有大量保留的客戶而言，較長的期間會產生大量資料。 透過 API 傳回資料會對 Azure 資源造成負擔。

## <a name="solution"></a>解決方法

我們理解客戶想要下載較長期間的資料。 不過，目前沒有任何方法可下載長期的保留使用量資料。

## <a name="next-steps"></a>後續步驟

- 如需有關保留的詳細資訊，請參閱[什麼是 Azure 保留？](save-compute-costs-reservations.md)。