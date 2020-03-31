---
title: Azure 媒體服務高可用性流式處理
description: 瞭解如何在區域資料中心中斷或發生故障時容錯移轉到輔助媒體服務帳戶。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899226"
---
# <a name="media-services-high-availability-streaming"></a>媒體服務高可用性流

如果區域資料中心中斷或基礎元件或從屬服務出現故障，Azure 媒體服務當前不會提供服務的即時容錯移轉。 本文指導如何構建視頻點播跨區域流。

## <a name="prerequisites"></a>Prerequisites

回顧[如何構建跨區域編碼系統](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>如何構建視頻點播跨區域流 

* 視頻點播跨區域流涉及複製[資產](assets-concept.md)、[內容金鑰策略](content-key-policy-concept.md)（如果使用）、[流式處理策略](streaming-policy-concept.md)和[流式處理器](streaming-locators-concept.md)。 
* 您必須在這兩個區域創建策略，並使它們保持最新狀態。 
* 創建流式處理器時，需要使用相同的定位器 ID 值、ContentKey ID 值和 ContentKey 值。  
* 如果要對內容進行編碼，建議對區域 A 中的內容進行編碼併發布，然後將編碼的內容複寫到區域 B，並使用與區域 A 相同的值發佈內容。
* 您可以在源和金鑰傳遞服務的主機名稱上使用流量管理器（在媒體服務配置中，這看起來像自訂金鑰伺服器 URL）。

## <a name="next-steps"></a>後續步驟

退房：

* [教程：基於 URL 對遠端檔進行編碼並資料流視頻](stream-files-dotnet-quickstart.md)
* [代碼示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
