---
title: Microsoft Azure 媒體服務常見案例 |Microsoft Docs
description: 本文概述 Microsoft Azure 媒體服務案例。
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
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: d195ad6715c47b9b4c14dc2e65ba1d07ebf79ce8
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696256"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure 媒體服務常見案例

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 查看最新版本的[媒體服務 v3](../latest/media-services-overview.md)。 另請參閱[從 v2 變更為 v3 的移轉指導方針](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure 媒體服務 (AMS) 可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時串流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

本文說明即時或隨選傳遞內容的常見案例。

## <a name="overview"></a>概觀

### <a name="prerequisites"></a>必要條件

* Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com)。
* Azure 媒體服務帳戶。 如需詳細資訊，請參閱[建立帳戶](media-services-portal-create-account.md)。
* 您想要串流內容的串流端點必須處於 [執行中] 狀態。

    建立 AMS 帳戶時，系統會將 **預設** 的串流端點新增至您的帳戶處於 **已停止** 狀態。 若要開始串流處理您的內容並利用動態封裝和動態加密功能，串流端點必須處於 [執行中] 狀態。

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>針對 AMS OData 模型開發時常用的物件

下列影像顯示針對媒體服務 OData 模型進行開發時一些最常用的物件。

按一下影像可以完整大小檢視。  

[![此圖顯示針對 Azure 媒體服務物件資料模型進行開發時，最常使用的物件。](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

您可以[在此](https://media.windows.net/API/$metadata?api-version=2.15)檢視整個模型。  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>保護儲存體中的內容並提供不加密的串流媒體

![VoD 工作流程](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. 將高品質媒體檔上傳到資產。

    將儲存體加密選項套用至您的資產，以在上傳期間保護您的內容，並建議在儲存體中待用。

1. 編碼成一組調適性位元速率 MP4 檔案。

    建議您將儲存體加密選項套用到輸出資產，以保護您的靜止內容。

1. 設定資產傳遞原則 (用於動態封裝)。

    如果您的資產是已加密的儲存體， **必須** 設定資產傳遞原則。
1. 藉由建立 OnDemand 定位器的方法來發行資產。
1. 串流發佈的內容。

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>保護儲存體中的內容、提供動態加密串流處理媒體

![利用 PlayReady 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. 將高品質媒體檔上傳到資產。 將儲存體加密選項套用到資產。
1. 編碼成一組調適性位元速率 MP4 檔案。 將儲存體加密選項套用到輸出資產。
1. 針對您想要在播放期間動態加密的資產，建立加密內容金鑰。
1. 設定內容金鑰授權原則。
1. 設定資產傳遞原則 (供動態封裝和動態加密使用)。
1. 藉由建立 OnDemand 定位器的方法來發行資產。
1. 串流發佈的內容。

## <a name="deliver-progressive-download"></a>提供漸進式下載

1. 將高品質媒體檔上傳到資產。
1. 編碼成單一 MP4 檔案。
1. 藉由建立 OnDemand 或 SAS 定位器的方法來發行資產。 如果使用 SAS 定位器，內容是從 Azure blob 儲存體下載的。 您不需要讓串流端點處於 [已啟動] 狀態。
1. 漸進式下載內容。

## <a name="delivering-live-streaming-events"></a>傳遞即時串流事件

1. 使用各種即時串流通訊協定 (例如 RTMP 或 Smooth Streaming) 擷取即時內容。
1. (選擇性) 將您的串流編碼成調適性位元速率串流。
1. 預覽您的即時串流。
1. 透過下列內容傳遞內容：
    1. 常見的串流通訊協定 (例如，MPEG 破折號、流暢、HLS) 直接傳送給您的客戶。
    1. 若為內容傳遞網路 (CDN) 以進行進一步散發，或
    1. 記錄並儲存內嵌內容，稍後再 (隨選影片) 進行資料流程處理。

執行即時串流時，您可以選擇下列其中一個路由：

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>使用可從內部部署編碼器接收多位元速率即時串流的通道 (即時通行)

下圖顯示 **即時通行** 工作流程中涉及的 AMS 平台主要部分。

![顯示「通過」工作流程中涉及之 M S 平臺主要部分的圖表。](./media/scenarios-and-availability/media-services-live-streaming-current.png)

如需詳細資訊，請參閱 [使用通道，從內部部署編碼器接收多位元速率即時串流](media-services-live-streaming-with-onprem-encoders.md)。

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>使用啟用的通道來以 Azure 媒體服務執行即時編碼

下圖顯示 AMS 平臺的主要元件，其中包含可讓通道以媒體服務執行即時編碼的即時串流工作流程。

![即時工作流程](./media/scenarios-and-availability/media-services-live-streaming-new.png)

如需詳細資訊，請參閱 [使用啟用的通道以 Azure 媒體服務執行即時編碼](media-services-manage-live-encoder-enabled-channels.md)。

## <a name="consuming-content"></a>使用內容

Azure 媒體服務提供一些工具，供您用來建立適用於大部分平台的豐富、動態用戶端播放器應用程式，此處所述的平台包括：iOS 裝置、Android 裝置、Windows、Windows Phone、Xbox 和機上盒。

## <a name="enabling-azure-cdn"></a>啟用 Azure CDN

媒體服務支援與 Azure CDN 整合。 如需如何啟用 Azure CDN 的資訊，請參閱 [如何管理媒體服務帳戶中的串流端點](media-services-portal-manage-streaming-endpoints.md)。

## <a name="scaling-a-media-services-account"></a>調整媒體服務帳戶

AMS 客戶可以使用其 AMS 帳戶來調整串流端點、媒體處理和儲存體。

* 媒體服務客戶可以選擇一個 **標準** 串流端點或一個 **進階** 串流端點。 大多數的串流工作負載都適合使用 **標準** 串流端點。 其所含的功能與 **進階** 串流端點相同，並會自動調整輸出頻寬。

    **進階** 串流端點適合進階工作負載，提供專用並能靈活調整的頻寬容量。 擁有 **進階** 串流端點的客戶預設會取得一個串流單位 (SU)。 藉由新增 SU 可以調整串流端點。 每個 SU 都可為應用程式提供額外的頻寬容量。 如需調整 **進階** 串流端點的詳細資訊，請參閱 [調整串流端點](media-services-portal-scale-streaming-endpoints.md)主題。

* 媒體服務帳戶是與保留單元類型相關聯，後者決定媒體處理工作的速度。 您可以選擇下列的保留單元類型：**S1**、**S2** 或 **S3**。 例如，在執行相同編碼作業的前提下，使用 **S2** 保留單元類型的速度會比 **S1** 類型快。

    除了指定保留單元類型之外，您還可以指定使用 **保留單位** (ru) 來布建您的帳戶。 佈建的 RU 數目可決定指定帳戶中可同時處理的媒體工作數目。

    > [!NOTE]
    > RU 用於平行化所有媒體處理，包括使用 Azure 媒體索引器的索引作業。 不過，與編碼不同，索引工作的處理速度不會因為使用較快的保留單元而變快。

    如需詳細資訊，請參閱 [調整媒體處理](media-services-portal-scale-media-processing.md)。

* 您也可以透過新增儲存體帳戶，來調整媒體服務帳戶。 每個儲存體帳戶的限制為 500 TB。 如需詳細資訊，請參閱[管理儲存體帳戶](./media-services-managing-multiple-storage-accounts.md)。

## <a name="next-steps"></a>後續步驟

[遷移至媒體服務 v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]