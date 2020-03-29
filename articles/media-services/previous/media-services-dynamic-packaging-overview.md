---
title: Azure 媒體服務動態封裝概觀 | Microsoft Docs
description: 本文概述了 Microsoft Azure 媒體服務動態打包。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901195"
---
# <a name="dynamic-packaging"></a>動態封裝

> [!div class="op_single_selector" title1="選擇您正在使用的媒體服務版本："]
> * [第 3 版](../latest/dynamic-packaging-overview.md)
> * [版本 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>查看最新版本，[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，請參閱[從 v2 到 v3 的遷移指南](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services 可用來針對數種用戶端技術 (例如 iOS、XBOX、Silverlight、Windows 8) 提供許多媒體來源檔案格式、媒體串流格式和內容保護格式。 這些用戶端各自使用不同的通訊協定，例如 iOS 需要 HTTP 即時串流 (HLS) V4 格式，而 Silverlight 與 Xbox 需要 Smooth Streaming。 如果您有一組自動調整位元速率 (多位元速率) MP4 (ISO Base Media 14496-12) 檔案或一組自動調整位元速率 Smooth Streaming 檔案，想要傳遞給了解 MPEG DASH、HLS 或 Smooth Streaming 的用戶端，應該利用媒體服務動態封裝。

使用動態打包，您需要創建一個包含一組自我調整位元速率 MP4 檔或自我調整位元速率平滑流檔的資產。 然後隨選資料流處理伺服器會根據資訊清單或片段要求中的指定格式，確保您以自己選擇的通訊協定接收串流。 因此，您只需要儲存及支付一種儲存格式之檔案的費用，媒體服務會根據用戶端的要求建置及提供適當的回應。

下圖顯示傳統編碼和靜態封裝工作流程。

![靜態編碼](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

下圖顯示動態封裝工作流程。

![動態編碼](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>常見的案例

1. 上傳輸入檔案 (稱為夾層檔)。 例如，H.264、MP4 或 WMV (如需支援格式清單，請參閱 [媒體編碼器標準所支援的格式](media-services-media-encoder-standard-formats.md))。
2. 將夾層檔編碼為 H.264 MP4 自動調整位元速率集。
3. 藉由建立隨選定位器，發行包含自動調整位元速率 MP4 集的資產。
4. 建置串流 URL 來存取和串流您的內容。

## <a name="preparing-assets-for-dynamic-streaming"></a>準備動態串流的資產

要準備動態流式處理的資產，您需要以下選項：

- [上傳主檔案](media-services-dotnet-upload-files.md)。
- [使用媒體編碼器標準編碼器產生 H.264 MP4 自動調整位元速率集](media-services-dotnet-encode-with-media-encoder-standard.md)。
- [串流處理內容](media-services-deliver-content-overview.md)。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>動態封裝支援的音訊轉碼器

動態打包支援 MP4 檔，其中包含使用[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)編碼的音訊（AAC-LC、HE-AAC v1、HE-AAC v2）、[杜比數位加號](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增強型 AC-3 或 E-AC3）、杜比全景聲或[DTS（DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)快遞、DTS LBR、DTS HD、DTS HD 無損）。 使用通用流格式 （CSF） 或通用媒體應用程式格式 （CMAF） 零碎 MP4 的標準以及通過 CMAF 的 HTTP 即時流式處理 （HLS） 支援杜比全景聲內容流（ 如 MPEG-DASH 協定）。

> [!NOTE]
> 動態封裝不支援包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音訊的檔案 (它是舊版的轉碼器)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

