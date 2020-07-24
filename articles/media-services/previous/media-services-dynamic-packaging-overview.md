---
title: Azure 媒體服務動態封裝概觀 | Microsoft Docs
description: 這篇文章提供 Microsoft Azure 媒體服務動態封裝的總覽。
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
ms.openlocfilehash: 2c4654ce175e64c375c96164aed274bf8078d196
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060124"
---
# <a name="dynamic-packaging"></a>動態封裝

> [!div class="op_single_selector" title1="選取您要使用的媒體服務版本："]
> * [第 3 版](../latest/dynamic-packaging-overview.md)
> * [第 2 版](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>查看最新版本的[媒體服務 v3](../latest/index.yml)。 另請參閱[從 v2 變更為 v3 的移轉指導方針](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure Media Services 可用來針對數種用戶端技術 (例如 iOS、XBOX、Silverlight、Windows 8) 提供許多媒體來源檔案格式、媒體串流格式和內容保護格式。 這些用戶端各自使用不同的通訊協定，例如 iOS 需要 HTTP 即時串流 (HLS) V4 格式，而 Silverlight 與 Xbox 需要 Smooth Streaming。 如果您有一組自動調整位元速率 (多位元速率) MP4 (ISO Base Media 14496-12) 檔案或一組自動調整位元速率 Smooth Streaming 檔案，想要傳遞給了解 MPEG DASH、HLS 或 Smooth Streaming 的用戶端，應該利用媒體服務動態封裝。

使用動態封裝，您需要建立包含一組自動調整位元速率的設定檔案或彈性位元速率 Smooth Streaming 檔案的資產。 然後隨選資料流處理伺服器會根據資訊清單或片段要求中的指定格式，確保您以自己選擇的通訊協定接收串流。 因此，您只需要儲存及支付一種儲存格式之檔案的費用，媒體服務會根據用戶端的要求建置及提供適當的回應。

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

若要準備您的資產以進行動態串流，您有下列選項：

- [上傳主檔案](media-services-dotnet-upload-files.md)。
- [使用媒體編碼器標準編碼器產生 H.264 MP4 自動調整位元速率集](media-services-dotnet-encode-with-media-encoder-standard.md)。
- [串流處理內容](media-services-deliver-content-overview.md)。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>動態封裝支援的音訊轉碼器

動態封裝支援檔案格式檔案，其中包含以[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) （AAC-LC，AAC V1，他 AAC v2）編碼的音訊、[杜比數位 Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增強的 AC-3 或 E-AC3）、杜比 Atmos 或[DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) （DTS EXPRESS、DTS LBR、dts hd、dts hd 不失真）。 如有一般串流格式（CSF）或通用媒體應用程式格式（CMAF）的 MPEG 破折號通訊協定，以及透過 CMAF 的 HTTP 即時串流（HLS），標準的「杜比」 Atmos 內容支援串流處理。

> [!NOTE]
> 動態封裝不支援包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音訊的檔案 (它是舊版的轉碼器)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
