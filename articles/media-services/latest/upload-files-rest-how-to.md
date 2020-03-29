---
title: 使用 REST 將檔上載到 Azure 媒體服務 v3 帳戶 |微軟文檔
description: 了解如何建立並上傳資產，以將媒體內容移至媒體服務中。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705763"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>使用 REST 將檔上載到媒體服務 v3 帳戶

在媒體服務中，您會將數位檔案上傳到與資產相關聯的 Blob 容器。 [資產](https://docs.microsoft.com/rest/api/media/operations/asset)實體可以包含視頻、音訊、圖像、縮略圖集合、文本軌道和隱藏字幕檔（以及有關這些檔的中繼資料）。 一旦檔案會上傳到資產的容器，您的內容會安全地儲存在雲端，以便進行進一步的處理和串流。

本文介紹如何使用 REST 上載本地檔。

## <a name="prerequisites"></a>Prerequisites

若要完成此主題中所述的步驟，您必須：

- 檢閱[資產概念](assets-concept.md)。
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。
    
    請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 

## <a name="create-an-asset"></a>建立資產

本節說明如何建立新的資產。

1. 選擇 **"資產** -> **創建或更新資產**"。
2. 按 [傳送]****。

    ![建立資產](./media/upload-files/postman-create-asset.png)

您會看到 [回應]****，包含新建資產的相關資訊。

## <a name="get-a-sas-url-with-read-write-permissions"></a>取得具有讀寫權限的 SAS URL 

本節說明如何取得針對已建立資產所產生的 SAS URL。 SAS URL 是以讀寫權限建立，可用來將數位檔案上傳到資產容器。

1. 選擇 **"資產** -> **清單"資產 URL。**
2. 按 [傳送]****。

    ![上傳檔案](./media/upload-files/postman-create-sas-locator.png)

您會看到 [回應]****，包含資產的 URL 相關資訊。 複製第一個 URL 並用於上傳檔案。

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>使用上傳 URL 將檔案上傳至 blob 儲存體

使用 Azure 存儲 API 或 SDK（例如，[存儲 REST API](../../storage/common/storage-rest-api-auth.md)或[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）。

## <a name="next-steps"></a>後續步驟

[教程：基於 URL 對遠端檔進行編碼並資料流視頻 - REST](stream-files-tutorial-with-rest.md)
