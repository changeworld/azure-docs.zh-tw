---
title: 從本機檔案建立 Azure 媒體服務工作輸入 |Microsoft Docs
description: 本文示範如何從本機檔案建立 Azure 媒體服務工作輸入。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 02aaae89f7544392765b46361b1617e0fd7dae8c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89296801"
---
# <a name="create-a-job-input-from-a-local-file"></a>從本機檔案建立工作輸入

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在媒體服務 v3 中，當您提交工作來處理視訊時，必須告知媒體服務到何處尋找輸入視訊。 輸入視訊可以儲存為媒體服務資產，在這種情況下，您會建立一個以檔案為基礎的輸入資產 (儲存在本機或 Azure Blob 儲存體)。 本主題說明如何從本機檔案建立工作輸入。 如需完整的範例，請參閱此 [GitHub 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)。

## <a name="prerequisites"></a>Prerequisites 

* [建立媒體服務帳戶](./create-account-howto.md)。

## <a name="net-sample"></a>.NET 範例

下列程式碼說明如何建立一個輸入資產，並將它當作工作的輸入。 CreateInputAsset 函式會執行下列動作：

* 建立資產
* 取得[儲存體中資產容器](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)的可寫入[SAS URL](../../storage/common/storage-sas-overview.md)
* 使用 SAS URL，將檔案上傳至儲存體中的容器

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

下列程式碼片段會建立輸出資產（如果尚未存在）：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

下列程式碼片段會提交編碼工作：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>作業錯誤碼

請參閱[錯誤碼](/rest/api/media/jobs/get#joberrorcode) \(英文\)。

## <a name="next-steps"></a>接下來的步驟

[從 HTTPS URL 建立作業輸入](job-input-from-http-how-to.md)。
