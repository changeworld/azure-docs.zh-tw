---
title: Azure 媒體播放器概觀
description: 深入了解 Azure 媒體播放器。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321722"
---
# <a name="azure-media-player-overview"></a>Azure 媒體播放器概觀 #

Azure 媒體播放器是一款網頁視訊播放器，可以在各種瀏覽器和裝置上播放 [Microsoft Azure 媒體服務](https://azure.microsoft.com/services/media-services/)的媒體內容。 Azure Media Player 採用業界標準，例如 HTML5、媒體來源延伸模組 (MSE) 和加密媒體擴充功能 (EME)，提供豐富的調適性串流體驗。  無法在裝置或瀏覽器使用這些標準時，Azure Media Player 則會使用 Flash 和 Silverlight 做為後援技術。 不論使用何種播放技術，開發人員都有統一的 JavaScript 介面來存取 API，無需額外作業即可讓 Azure 媒體服務提供的內容可以跨各種裝置和瀏覽器播放。

我們可以利用 Microsoft Azure 媒體服務播放 DASH、Smooth Streaming 和 HLS 資料流等格式的內容。 Azure Media Player 會考量這些不同的格式，並根據平台/瀏覽器功能自動播放最合適的連結。 Microsoft Azure 媒體服務也提供一般加密 (PlayReady 或 Widevine) 或 AES-128 位元信封加密，進行資產的動態加密。 只要設定正確，Azure Media Player 允許解密 PlayReady 和 AES 128 位元加密的內容。  若要了解如何設定播放機，請參閱[受保護內容](azure-media-player-protected-content.md)一節。

若要要求新功能，或是提供想法或意見反應，請提交至 [Azure 媒體播放器的 UserVoice](https://aka.ms/ampuservoice) \(英文\)。 如果您有特定問題或疑問，或是有發現任何錯誤 (Bug)，請以電子郵件通知我們 (ampinfo@microsoft.com)。

> [!NOTE]
> 請注意，Azure 媒體播放器只支援來自 Azure 媒體服務的媒體串流。

## <a name="license"></a>授權 ##

Azure 媒體播放器已獲得授權，並受限於 Azure 媒體播放器的 Microsoft 軟體授權條款中所述的條款。 如需完整的條款，請參閱[授權檔案](/legal/azure-media-player/azure-media-player-license)。 如需詳細資訊，請參閱[隱私權聲明](https://www.microsoft.com/en-us/privacystatement/default.aspx)。

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
