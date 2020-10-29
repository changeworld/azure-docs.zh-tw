---
title: 快速入門 - 使用 Azure 入口網站建立 Blob
titleSuffix: Azure Storage
description: 在本快速入門中，您會在物件 (Blob) 儲存體中使用 Azure 入口網站。 然後，使用 Azure 入口網站將 blob 上傳至 Azure 儲存體、下載 blob，以及列出容器中的 blob。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: 0bf9d6eb68536588b35df93e13b04841d7868d31
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547143"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>快速入門：使用 Azure 入口網站上傳、下載及列出 Blob

在本快速入門中，您會了解如何使用 [Azure 入口網站](https://portal.azure.com/)在 Azure 儲存體中建立容器，並且在該容器中上傳和下載區塊 blob。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>建立容器

若要在 Azure 入口網站中建立容器，請遵循下列步驟：

1. 在 Azure 入口網站中瀏覽至新的儲存體帳戶。
2. 在儲存體帳戶的左窗格中，捲動到 [Blob 服務]  區段，然後選取 [容器]  。
3. 選取 [+ 容器]  按鈕。
4. 輸入新容器的名稱。 容器名稱必須是小寫，以字母或數字開頭，並且只能包含字母、數字和虛線 (-) 字元。 如需關於容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。
5. 設定容器的公用存取層級。 預設層級是 [私用 (沒有匿名存取權)]  。
6. 選取 [確定]  以建立容器。

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="顯示如何在 Azure 入口網站中建立容器的螢幕擷取畫面":::

## <a name="upload-a-block-blob"></a>上傳區塊 Blob

區塊 Blob 是由組合以成為 Blob 的資料區塊所組成。 使用 Blob 儲存體的大部分案例會採用區塊 Blob。 區塊 Blob 十分適用於在雲端中儲存文字和二進位資料，例如檔案、映像和影片。 本快速入門示範如何使用區塊 Blob。

若要將區塊 Blob 上傳到 Azure 入口網站中的新容器，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至您在上一節中建立的容器。
1. 選取容器以顯示它所包含的 Blob 清單。 此容器是新的，因此尚未包含任何 Blob。
1. 選取 [上傳]  按鈕以開啟 [上傳] 刀鋒視窗，然後瀏覽您的本機檔案系統，尋找要上傳做為區塊 blob 的檔案。 您可以選擇性地展開 [進階] 區段，進行上傳作業的其他設定。

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="顯示如何在 Azure 入口網站中建立容器的螢幕擷取畫面":::

1. 選取 [上傳]  按鈕上傳 Blob。
1. 用這種方式上傳任意數量的 Blob。 您會看到新的 Blob 現在列在容器內。

## <a name="download-a-block-blob"></a>下載區塊 Blob

您可以下載區塊 Blob，以在瀏覽器中顯示或儲存到本機檔案系統。 若要下載區塊 Blob，請遵循下列步驟：

1. 瀏覽至您在上一節中上傳的 Blob 清單。
1. 以滑鼠右鍵按一下您要下載的 Blob，然後選取 [下載]  。

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="顯示如何在 Azure 入口網站中建立容器的螢幕擷取畫面":::

## <a name="delete-a-block-blob"></a>刪除區塊 Blob

若要在 Azure 入口網站中刪除一或多個 Blob，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽到容器。
1. 顯示容器中的 Blob 清單。
1. 使用核取方塊從清單中選取一或多個 Blob。
1. 選取 [刪除] 按鈕，即可刪除選取的 Blob。
1. 在對話方塊中，確認刪除，並指出是否也要刪除 Blob 快照集。

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="顯示如何在 Azure 入口網站中建立容器的螢幕擷取畫面":::

## <a name="clean-up-resources"></a>清除資源

若要移除您在本快速入門中建立的所有資源，只要刪除容器即可。 容器中的所有 Blob 也會遭到刪除。

若要刪除容器：

1. 在 Azure 入口網站中，瀏覽至儲存體帳戶中的容器清單。
1. 選取要刪除的容器。
1. 選取 [更多]  按鈕 ( **...** )，然後選取 [刪除]  。
1. 確認您要刪除容器。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您將了解如何透過 Azure 入口網站來建立容器和上傳 Blob。 若要了解如何從 Web 應用程式使用 Blob 儲存體，請繼續閱讀教學課程，該教學課程說明如何將影像上傳至儲存體帳戶。

> [!div class="nextstepaction"]
> [教學課程：使用 Azure 儲存體在雲端中上傳影像資料](storage-upload-process-images.md)
