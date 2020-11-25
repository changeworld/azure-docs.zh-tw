---
title: 使用 Azure 媒體播放器播放 - Azure | Microsoft Docs
description: Azure 媒體播放器是一款網頁視訊播放器，可以在各種瀏覽器和裝置上播放 Microsoft Azure 媒體服務的媒體內容。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: a3b32110a7236d47946c3a1f901aaedf51b47775
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022212"
---
# <a name="playback-with-azure-media-player"></a>使用 Azure 媒體播放機播放

Azure 媒體播放器是一款網頁視訊播放器，可以在各種瀏覽器和裝置上播放 Microsoft Azure 媒體服務的媒體內容。 Azure Media Player 採用業界標準，例如 HTML5、媒體來源延伸模組 (MSE) 和加密媒體擴充功能 (EME)，提供豐富的調適性串流體驗。 無法在裝置或瀏覽器使用這些標準時，Azure Media Player 則會使用 Flash 和 Silverlight 做為後援技術。 不論使用何種播放技術，開發人員都會有統一的 JavaScript 介面來存取應用程式開發介面。 這樣我們就可以在各種裝置和瀏覽器上順利播放 Azure 媒體服務提供的內容。

我們可以利用 Microsoft Azure 媒體服務播放 HLS、DASH、Smooth Streaming 資料流等格式的內容。 Azure Media Player 會考量這些不同的格式，並根據平台/瀏覽器功能自動播放最合適的連結。 媒體服務也允許利用 PlayReady 加密或 AES 128 位元信封加密，進行資產的動態加密。 只要設定正確，Azure Media Player 允許解密 PlayReady 和 AES 128 位元加密的內容。

> [!NOTE]
> 需要 HTTPS 播放才能 Widevine 加密的內容。

## <a name="use-azure-media-player-demo-page"></a>使用 Azure 媒體播放器示範頁面

### <a name="start-using"></a>開始使用

您可以使用 [Azure 媒體播放器示範頁面](https://aka.ms/azuremediaplayer)來播放 Azure 媒體服務範例或您自己的資料流。  

若要播放新的影片，請貼上不同 URL，並按 [更新]。

若要設定各種播放選項 (例如技術、語言或加密)，請按 [進階選項]。

![Azure 媒體播放器](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>監視視訊資料流的診斷

您可以使用 [Azure 媒體播放器示範頁面](https://aka.ms/azuremediaplayer) \(英文\) 來監視視訊資料流的診斷。

![Azure 媒體播放器診斷](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>設定您 HTML 中的 Azure 媒體播放器

Azure 媒體播放器很容易設定。 只需要一些時間從媒體服務帳戶取得基本的媒體內容播放。 請參閱 [Azure 媒體播放器文件](../azure-media-player/azure-media-player-overview.md)，了解如何安裝和設定 Azure 媒體播放器。

## <a name="additional-notes"></a>其他注意事項

* Widevine 是 Google Inc. 所提供的服務，並受到 Google Inc. 的服務條款和隱私權原則所約束。

## <a name="next-steps"></a>後續步驟

* [Azure 媒體播放機檔](../azure-media-player/azure-media-player-overview.md)
* [Azure 媒體播放機範例](https://github.com/Azure-Samples/azure-media-player-samples)