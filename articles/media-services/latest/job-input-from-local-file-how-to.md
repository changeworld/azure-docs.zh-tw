---
title: 從本地檔創建 Azure 媒體服務作業作業輸入 |微軟文檔
description: 本文演示如何從本地檔創建 Azure 媒體服務作業作業輸入。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345916"
---
# <a name="create-a-job-input-from-a-local-file"></a>從本機檔案建立工作輸入

在媒體服務 v3 中，當您提交工作來處理視訊時，必須告知媒體服務到何處尋找輸入視訊。 輸入視訊可以儲存為媒體服務資產，在這種情況下，您會建立一個以檔案為基礎的輸入資產 (儲存在本機或 Azure Blob 儲存體)。 本主題說明如何從本機檔案建立工作輸入。 如需完整的範例，請參閱此 [GitHub 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)。

## <a name="prerequisites"></a>Prerequisites 

* [創建媒體服務帳戶](create-account-cli-how-to.md)。
* 審核[管理資產](manage-asset-concept.md)。

## <a name="net-sample"></a>.NET 範例

下列程式碼說明如何建立一個輸入資產，並將它當作工作的輸入。 CreateInputAsset 函式會執行下列動作：

* 建立資產
* 取得資產[儲存體容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)的 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* 使用 SAS URL，將檔案上傳至儲存體中的容器

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

如果輸出資產不存在，以下程式碼片段將創建輸出資產：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

以下程式碼片段提交編碼作業：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>作業錯誤碼

請參閱[錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode) \(英文\)。

## <a name="next-steps"></a>後續步驟

[從 HTTPS URL 創建作業輸入](job-input-from-http-how-to.md)。
