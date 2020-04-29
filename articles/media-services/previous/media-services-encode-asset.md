---
title: Azure 隨選媒體編碼器的總覽 |Microsoft Docs
description: Azure 媒體服務提供多個用於將雲端中之媒體編碼的選項。 本文提供 Azure 隨選媒體編碼器的總覽。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251098"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Azure 隨選媒體編碼器的總覽 

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>請查看最新版本，[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 另請參閱[從 v2 到 v3 的遷移指引](../latest/migrate-from-v2-to-v3.md)

Azure 媒體服務提供多個用於將雲端中之媒體編碼的選項。

開始使用媒體服務時，請務必了解轉碼器和檔案格式之間的差異。
轉碼器是實作壓縮/解壓縮演算法的軟體，而檔案格式是保存已壓縮視訊的容器。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH、HLS、Smooth Streaming) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

建立媒體服務帳戶時，**預設**串流端點會新增至您的帳戶處於 [**已停止**] 狀態。 若要開始串流內容並利用動態封裝和動態加密功能，您想要串流內容的串流端點必須處於 [執行中]**** 狀態。 當端點處於執行中狀態時，就會發生串流**端點的計費**。

媒體服務支援本文中所描述的下列隨選編碼器：

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [媒體編碼器高階工作流程](media-services-encode-asset.md#media-encoder-premium-workflow)

本文概略敘述隨選媒體編碼器，並提供文章連結以提供更詳細資訊。 本主題也提供各種編碼器的比較。

依預設，每個媒體服務帳戶一次可以有一個進行中的編碼工作。 您可以保留編碼單位，這樣就可以同時執行多個編碼工作，其中一個用於您購買的每一個編碼保留單位。 如需相關資訊，請參閱 [調整編碼單位](media-services-scale-media-processing-overview.md)。

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>如何使用
[如何使用 Media Encoder Standard 進行編碼](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>格式
[格式和轉碼器](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>預設值
Media Encoder Standard 使用 [這裡](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)描述的其中一個編碼器預設值進行設定。

### <a name="input-and-output-metadata"></a>輸入和輸出中繼資料
[這裡](media-services-input-metadata-schema.md)說明編碼器輸入中繼資料。

[這裡](media-services-output-metadata-schema.md)說明編碼器輸出中繼資料。

### <a name="generate-thumbnails"></a>產生縮圖
如需相關資訊，請參閱 [如何使用媒體編碼器標準產生縮圖](media-services-advanced-encoding-with-mes.md#thumbnails)。

### <a name="trim-videos-clipping"></a>修剪視訊 (裁剪)
如需相關資訊，請參閱 [如何使用媒體編碼器標準修剪視訊](media-services-advanced-encoding-with-mes.md#trim_video)。

### <a name="create-overlays"></a>建立疊加層
如需相關資訊，請參閱 [如何使用媒體編碼器標準建立覆疊](media-services-advanced-encoding-with-mes.md#overlay)。

### <a name="see-also"></a>請參閱
[媒體服務部落格](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>媒體編碼器高階工作流程
### <a name="overview"></a>概觀
[介紹 Azure 媒體服務中的 Premium 編碼](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>如何使用
Media Encoder Premium Workflow 使用複雜的工作流程設定。 您可以使用 [工作流程設計工具](media-services-workflow-designer.md) 建立和更新工作流程檔案。

[如何使用 Azure 媒體服務中的 Premium 編碼](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>已知問題
如果您的輸入視訊不包含隱藏式字幕，輸出資產仍然會包含空白 TTML 檔案。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相關文章
* [透過自訂 Media Encoder Standard 預設值來執行進階編碼工作](media-services-custom-mes-presets-with-dotnet.md)
* [配額和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
