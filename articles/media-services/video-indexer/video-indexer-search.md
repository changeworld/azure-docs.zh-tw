---
title: 使用影片索引子搜尋影片中的確切時刻
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子來搜尋影片中的確切時間。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127917"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>使用影片索引子搜尋影片中的確切時刻

本主題說明搜尋選項，可讓您搜尋影片中的確切時間。

1. 移至[影片索引子](https://www.videoindexer.ai/)網站並登入。
2. 搜尋您帳戶中的所有影片。

    在下列範例中，我們會搜尋所有與安全性相關的影片，並在 Satya 中出現。

    ![在影片索引子中搜尋影片](./media/video-indexer-search/video-indexer-search01.png)

3. 搜尋影片的深入解析摘要。

    然後，您可以選取影片上的 [**播放**] 來搜尋影片。 然後，您可以選取 [**搜尋] 索引**標籤來搜尋影片中的確切時間。

    在下列範例中，我們會搜尋所選影片內的「安全」。

    ![使用影片索引子在影片中搜尋](./media/video-indexer-search/video-indexer-search02.png)

    如果您選取其中一個結果，播放程式會將您帶往影片中的確切時間。 您可以在應用程式中達成 player/insights 視圖和同步處理。 如需詳細資訊，請參閱[將影片索引子小工具內嵌至您的應用程式](video-indexer-embed-widgets.md)。

4. 搜尋影片的明細。

    如果您想要根據找到的影片建立自己的剪輯，請選取 [**編輯**] 按鈕。 此頁面會顯示影片以及其深入解析作為篩選準則。 如需詳細資訊，請參閱[檢視和編輯影片索引器的深入解析](video-indexer-view-edit.md)。

    您可以搜尋影片中的確切時間，只顯示您感興趣的線條。 使用 [側邊深入解析] 來篩選您想要查看的元件。 當您完成時，您可以預覽剪輯並選取 [**發佈**]，以建立出現在資源庫中的新剪輯。

    在下列範例中，我們會搜尋「mixed reality」文字。 我們也套用了其他篩選，如下列畫面所示。

    ![在影片索引子中搜尋確切的時間](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>後續步驟

一旦您在影片中找到您想要使用的確切時間，就可以繼續處理影片。 如需詳細資訊，請參閱下列主題：

- [使用您的影片深度深入解析](use-editor-create-project.md)
- [使用影片索引器 REST API 處理內容](video-indexer-use-apis.md)
- [將視覺介面控件內嵌到應用程式](video-indexer-embed-widgets.md)

## <a name="see-also"></a>請參閱

[影片索引子概觀](video-indexer-overview.md)
