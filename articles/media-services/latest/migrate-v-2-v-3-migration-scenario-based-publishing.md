---
title: 封裝和傳遞以案例為基礎的遷移指引 |Microsoft Docs
description: 本文將為您提供封裝和傳遞的案例指引，協助您從 Azure 媒體服務 v2 遷移至 v3。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 74437a4adee9e2853f7b932316d57e406b7d00be
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690284"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>封裝和傳遞以案例為基礎的遷移指導方針

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-4.svg)

本文提供封裝和傳遞的案例指引，可協助您從 Azure 媒體服務 v2 遷移至 v3。

在 v3 API 中發佈內容的方式有重大變更。 新的發行模型已簡化，並使用較少的實體來建立串流定位器。 API 縮減為只有兩個實體，與先前所需的四個實體相同。 內容金鑰原則和串流定位器現在取代了、、 `ContentKeyAuthoriationPolicy` 和的需求 `AssetDeliveyPolicy` `ContentKey` `AccessPolicy` 。

## <a name="packaging-and-delivery-in-v3"></a>V3 中的封裝和傳遞

1. 建立 [內容金鑰原則](content-key-policy-concept.md)。
1. 建立 [串流定位器](streaming-locators-concept.md)。
1. 取得 [串流路徑](create-streaming-locator-build-url.md) 
    1. 將它設定為 [虛線](dynamic-packaging-overview.md#mpeg-dash-protocol) 或 [HLS](dynamic-packaging-overview.md#hls-protocol) 播放程式。

如需特定步驟，請參閱發行概念、教學課程和操作說明指南。

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>發行概念、教學課程和操作指南

### <a name="concepts"></a>概念

- [媒體服務 v3 中的動態封裝](dynamic-packaging-overview.md)
- [篩選條件](filters-concept.md)
- [使用動態封裝程式篩選您的資訊清單](filters-dynamic-manifest-overview.md)
- [Azure 媒體服務中 (原始) 的串流端點](streaming-endpoint-concept.md)
- [使用 CDN 整合來串流內容](scale-streaming-cdn.md)
- [串流定位器](streaming-locators-concept.md)

### <a name="how-to-guides"></a>使用說明指南

- [使用媒體服務 v3 來管理串流端點](manage-streaming-endpoints-howto.md)
- [CLI 範例：發佈資產](cli-publish-asset.md)
- [建立串流定位器並建置 URL](create-streaming-locator-build-url.md)
- [下載作業的結果](download-results-howto.md)
- [指示描述性音訊曲目](signal-descriptive-audio-howto.md)
- [Azure 媒體播放機完整設定](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-full-setup)
- [如何搭配 Azure 媒體服務使用 Video.js 播放機](how-to-video-js-player.md)
- [如何搭配 Azure 媒體服務使用 Shaka player](how-to-shaka-player.md)

## <a name="samples"></a>範例

您也可以 [比較程式碼範例中的 V2 和 V3 程式碼](migrate-v-2-v-3-migration-samples.md)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
