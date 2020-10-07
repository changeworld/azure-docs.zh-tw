---
title: Azure 媒體播放器 URL 重寫工具
description: Azure 媒體播放器會從 Azure 媒體服務重寫指定的 URL，以提供 SMOOTH、DASH、HLS v3 和 HLS v4.的資料流。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91275552"
---
# <a name="url-rewriter"></a>URL Rewriter #

根據預設，Azure 媒體播放器會從 Azure 媒體服務重寫指定的 URL，以提供 SMOOTH、DASH、HLS v3 和 HLS v4.的資料流。 例如，如果依照下列方式提供來源，Azure 媒體播放器將確保其嘗試播放上述所有的通訊協定：

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

不過，如果您不想使用 URL 重寫工具，可以將 `disableUrlRewriter` 屬性新增至參數來執行此動作。 這表示傳遞至來源的所有資訊都會直接傳遞至播放器，而不需修改。  以下範例示範如何將兩個來源新增至播放器：DASH 和 SMOOTH Streaming。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

或

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

此外，如果您想要，可以使用 `streamingFormats` 參數來指定您想要 Azure 媒體播放器重寫成的特定串流格式。 選項包括 `DASH`、`SMOOTH`、`HLSv3`、`HLSv4`、`HLS`。 HLS 與 HLSv3 和 v4 之間的差異在於 HLS 格式支援播放 FairPlay 內容。 v3 和 v4 不支援 FairPlay。 如果您沒有可供特定通訊協定使用的傳遞原則，這就很有用。  以下是您的資產未啟用 DASH 通訊協定時的範例。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

或

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

上述兩個可根據您的特定資產，在多個情況下搭配使用。

> [!NOTE]
> Widevine 保護資訊只會保存在 DASH 通訊協定上。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)