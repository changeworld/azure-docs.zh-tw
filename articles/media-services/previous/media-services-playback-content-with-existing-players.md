---
title: 使用現有的撥放器來撥放內容 - Azure | Microsoft Docs
description: 本文列出您可以用來播放內容的現有播放程式。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2d3c22e17c37bc46c16a9cc80eb3cf4b9ec93ecf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81686915"
---
# <a name="playing-your-content-with-existing-players"></a>使用現有播放器來播放您的內容
Azure 媒體服務支援許多熱門的串流格式，例如 Smooth Streaming、HTTP 即時資料流和 MPEG-Dash。 本主題會指引您可用來測試串流的現有播放程式。

### <a name="the-azure-portal-media-services-content-player"></a>Azure 入口網站媒體服務內容播放程式
**Azure**入口網站會提供內容播放機，供您用來測試您的影片。

按一下想用的視訊 (請確定它 [已發行](media-services-portal-publish.md))，按一下入口網站底部的 [ **播放** ] 按鈕。

適用一些考量事項：

* **媒體服務內容播放程式** 會從預設串流端點播放。 如果您想要從非預設串流端點播放，請使用其他播放程式。 例如， [Azure 媒體播放器](https://aka.ms/azuremediaplayer)。

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure 媒體播放器

使用 [Azure 媒體播放器](https://aka.ms/azuremediaplayer) 播放以下任一格式的內容 (清除或受保護)：

* Smooth Streaming
* MPEG DASH
* HLS
* Progressive MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady 與權杖

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH 播放程式

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>其他
若要測試 HLS URL，您也可以使用：

* **Safari** 或
* **3ivx HLS 播放器** 。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
