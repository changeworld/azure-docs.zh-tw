---
title: Azure 媒體服務地區可用性 |Microsoft Docs
description: 本文概述 Microsoft Azure 媒體服務功能和服務區域的可用性。
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
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 458927afc78c46ad365d437a574d0b40b32fc11e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695792"
---
# <a name="media-services-regional-availability"></a>媒體服務區域可用性

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 查看最新版本的[媒體服務 v3](../latest/media-services-overview.md)。 另請參閱[從 v2 變更為 v3 的移轉指導方針](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure 媒體服務 (AMS) 可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時串流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

AMS 在世界各地的多個區域中運作，可讓您彈性地選擇要在何處建立應用程式。 本文概述 Microsoft Azure 媒體服務功能和服務區域的可用性。

如需整個 Azure 全球基礎結構的詳細資訊，請參閱 [azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置。

## <a name="ams-accounts"></a>AMS 帳戶

使用 [Azure 產品（依區域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) ）來判斷特定區域中是否有可用的媒體服務。

## <a name="streaming-endpoints"></a>串流端點

媒體服務客戶可以選擇一個 **標準** 串流端點或一個 **進階** 串流端點。

|Name|狀態|區域
|---|---|---|
|標準|GA|全部|
|Premium|GA|全部|

## <a name="live-encoding"></a>即時編碼

適用于：德國、巴西南部、印度西部、印度南部和印度中部以外的所有區域。

## <a name="encoding-media-processors"></a>編碼媒體處理器

AMS 提供兩個隨選編碼器：**媒體編碼器標準** 和 **媒體編碼器進階工作流程**。 如需詳細資訊，請參閱 [Azure 隨選媒體編碼器的概觀和比較](media-services-encode-asset.md)。

|媒體處理器名稱|狀態|區域
|---|---|---|
|Media Encoder Standard|GA|全部|
|媒體編碼器高階工作流程|GA|所有區域 (中國除外)|

## <a name="analytics-media-processors"></a>分析媒體處理器

媒體分析是一組語音與視覺元件，可讓組織或企業從其影片檔輕鬆地產生能採取行動的見解。 如需詳細資訊，請參閱 [Azure 媒體服務分析概觀](./legacy-components.md)。

> [!NOTE]
> 即將淘汰某些分析媒體處理器。 如需淘汰日期，請參閱[舊版元件](legacy-components.md)主題。

|媒體處理器名稱|狀態|區域
|---|---|---|
|Azure 媒體臉部偵測器|預覽|全部|
|Azure Media Indexer|GA|全部|
|Azure 媒體動作偵測器|預覽|全部|
|Azure 媒體 OCR|預覽|全部|
|Azure 媒體Media Redactor|GA|全部|
|Azure 媒體視訊縮圖|預覽|全部|

## <a name="protection"></a>保護

Microsoft Azure 媒體服務可讓您保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 如需詳細資訊，請參閱[保護 AMS 內容](media-services-content-protection-overview.md)。

|加密|狀態|區域|
|---|---|---| 
|儲存體|GA|全部|
|AES-128 金鑰|GA|全部|
|Fairplay|GA|全部|
|PlayReady|GA|全部|
|Widevine|GA|除了德國、聯邦政府和中國以外的所有國家/地區。

> [!NOTE]
> Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="reserved-units-rus"></a>保留單元 (RU)

佈建的保留單元數目可決定指定帳戶中可同時處理的媒體工作數目。

所有區域都有提供。

## <a name="reserved-unit-ru-type"></a>保留單元 (RU) 類型

媒體服務帳戶是與保留單元類型相關聯，以決定媒體處理工作的完成速度。 您可以選擇下列保留單元類型： S1、S2 或 S3。

|RU 類型名稱|狀態|區域
|---|---|---|
|S1|GA|全部|
|S2|GA|全部，但不含巴西南部和印度西部|
|S3|GA|全部，但不含印度西部|

## <a name="next-steps"></a>後續步驟

[遷移至媒體服務 v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>提供意見反應

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]