---
title: Azure Analysis Services 資源和物件限制 | Microsoft Docs
description: 本文說明 Azure Analysis Services 伺服器的資源與物件限制。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c2caab4e449f4299d00fff14b697887ec00f35e6
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697487"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services 資源和物件限制。

本文描述資源和模型物件限制。

## <a name="tier-limits"></a>階層限制

如需開發人員、基本與標準層的 QPU 與記憶體限制，請參閱 [Azure Analysis Services 定價頁面](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="object-limits"></a>物件限制

這些是理論上的限制。 效能會因數量減少而降低。

|Object|大小/數目上限|  
|------------|----------------------------|  
|執行個體中的資料庫|16,000|  
|資料庫中資料表和資料行的合併數|16,000|  
|資料表中的資料列|無限制<br /><br /> **警告︰** 有一項限制，就是資料表中的任何資料行都不能擁有超過 1,999,999,997 個相異值。|  
|資料表中的階層|15,999|  
|階層中的層級|15,999|  
|關聯性|8,000|  
|所有資料表中的索引鍵資料行|15,999|  
|資料表中的量值|2^31-1 = 2,147,483,647|  
|查詢傳回的資料格|2^31-1 = 2,147,483,647|  
|來源查詢的記錄大小|64 K|  
|物件名稱的長度|512 個字元|  


