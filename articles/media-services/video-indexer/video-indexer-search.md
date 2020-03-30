---
title: 使用視頻索引子搜索視頻中的確切時刻
titleSuffix: Azure Media Services
description: 瞭解如何使用視頻索引子搜索視頻中的確切時刻。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127917"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>使用視頻索引子搜索視頻中的確切時刻

本主題顯示搜索選項，使您能夠在視頻中搜索確切時刻。

1. 轉到[視頻索引子](https://www.videoindexer.ai/)網站並登錄。
2. 搜尋您帳戶中的所有影片。

    在下面的示例中，我們搜索所有討論安全性的視頻，並在其中顯示 Satya。

    ![在視頻索引子中搜索視頻](./media/video-indexer-search/video-indexer-search01.png)

3. 搜尋影片的深入解析摘要。

    然後，您可以通過選擇 **"播放**"視頻來搜索視頻。 然後，您可以通過選擇 **"搜索"** 選項卡來搜索視頻中的確切時刻。

    在下面的示例中，我們在所選視頻中搜索"安全"。

    ![使用視頻索引子在視頻中搜索](./media/video-indexer-search/video-indexer-search02.png)

    如果選擇其中一個結果，播放機將為您提供視頻中的精確時刻。 您可以在應用中實現播放機/見解視圖和同步。 有關詳細資訊，請參閱[將視頻索引子小部件嵌入到你的應用中](video-indexer-embed-widgets.md)。

4. 搜尋影片的明細。

    如果要根據找到的視頻創建自己的剪輯，請選擇 **"編輯**"按鈕。 本頁將視頻及其見解作為篩選器顯示。 如需詳細資訊，請參閱[檢視和編輯影片索引器的深入解析](video-indexer-view-edit.md)。

    您可以搜索視頻中的確切時刻，以便僅顯示您感興趣的行。 使用側洞察篩選要查看的部件。 完成後，可以預覽剪輯並選擇 **"發佈"** 以創建庫中顯示的新剪輯。

    在下面的示例中，我們搜索了"混合現實"文本。 我們也套用了其他篩選，如下列畫面所示。

    ![在視頻索引子中搜索確切時刻](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>後續步驟

在要處理的視頻中找到確切時刻後，可以繼續處理視頻。 如需詳細資訊，請參閱下列主題：

- [使用視頻的深入見解](use-editor-create-project.md)
- [使用影片索引器 REST API 處理內容](video-indexer-use-apis.md)
- [將視覺介面控件內嵌到應用程式](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)
