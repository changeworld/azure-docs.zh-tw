---
title: 使用影片索引子在影片中搜尋確切的時刻
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子在影片中搜尋確切的時刻。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030498"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>使用影片索引子在影片中搜尋確切的時刻

本主題說明如何使用影片索引子網站，在影片中搜尋確切的時間。

1. 移至 [影片索引子](https://www.videoindexer.ai/) 網站並登入。
1. 指定搜尋關鍵字，搜尋會在您帳戶文件庫中的所有影片之間執行。 

    您可以選取 **篩選** 條件來篩選搜尋。 在下列範例中，我們會搜尋顯示為螢幕上文字 (OCR) 的 "Microsoft"。

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="篩選，僅限文字":::
1. 按 [ **搜尋** ] 以查看結果。

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="影片搜尋結果":::

    如果您選取其中一個結果，播放程式會將您帶入影片中的確切時間。
1. 按一下影片上的 [ **播放** ]，或選取您的其中一個原始搜尋結果，以查看並搜尋影片的摘要見解。 

    您可以查看、搜尋、編輯 **見解**。 當您選取其中一個見解時，播放程式會將您帶入影片中的確切時間。  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="查看、搜尋和編輯影片的見解":::

    如果您透過影片索引子小工具內嵌影片，您可以在應用程式中完成播放程式/見解視圖和同步處理。 如需詳細資訊，請參閱 [將影片索引子小工具內嵌至您的應用程式](video-indexer-embed-widgets.md)。
1. 您可以按一下 [ **時程表** ] 索引標籤來查看、搜尋和編輯文字記錄。 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="查看、搜尋和編輯影片的文字記錄":::

    若要編輯文字，請選取右上角的 [ **編輯** ]，並依需要變更文字。 

    您也可以從右上角選取適當的選項，以轉譯和下載文字記錄。 

## <a name="embed-download-create-projects"></a>內嵌、下載、建立專案

您可以藉由選取 **</>內嵌** 在您的影片下方，來內嵌您的影片。 如需詳細資訊，請參閱 [在您的應用程式中內嵌視覺](video-indexer-embed-widgets.md)widget。

您可以按一下影片底下的 [ **下載** ]，以下載來源影片、影片的見解、文字記錄。

您可以按一下 [ **在編輯器中開啟**]，以根據您的特定線和時間的影片建立剪輯。 然後編輯影片，並儲存專案。 如需詳細資訊，請參閱 [使用您的影片深度見解](use-editor-create-project.md)。

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="內嵌、下載、建立影片的專案":::

## <a name="next-steps"></a>後續步驟

[使用影片索引子處理內容 REST API](video-indexer-use-apis.md)
