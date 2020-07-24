---
title: Azure 媒體服務編碼錯誤代碼 | Microsoft Docs
description: 本主題列出在編碼工作執行期間發生錯誤時下可能傳回的錯誤碼。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b40bc8521755fae08e851e5b324979d5798e3dcd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87042863"
---
# <a name="encoding-error-codes"></a>編碼錯誤碼

下表列出在編碼工作執行期間發生錯誤的情況下可能傳回的錯誤碼。  若要取得 .NET 程式碼中的錯誤詳細資料，請使用 [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) 類別。 若要取得 REST 程式碼中的錯誤詳細資料，請使用 [ErrorDetail](/rest/api/media/operations/errordetail) REST API。

| ErrorDetail.Code | 導致發生錯誤的可能原因 |
| --- | --- |
| 未知 |執行工作時發生不明錯誤 |
| ErrorDownloadingInputAssetMalformedContent |涵蓋下載輸入資產中之錯誤 (例如無效的檔案名稱、長度為零檔案、錯誤格式等等) 的錯誤類別。 |
| ErrorDownloadingInputAssetServiceFailure |涵蓋服務端問題 (例如下載時發生網路或儲存體錯誤) 的錯誤類別。 |
| ErrorParsingConfiguration |工作 \<see cref="MediaTask.PrivateData"/> (設定) 無效時的錯誤類別，例如設定不是有效的系統預設或包含無效的 XML。 |
| ErrorExecutingTaskMalformedContent |在工作執行期間因輸入媒體檔案內部問題導致失敗的錯誤類別。 |
| ErrorExecutingTaskUnsupportedFormat |媒體處理器無法處理提供之檔案 (不支援的媒體格式或與組態不符) 的錯誤類別。 例如，嘗試從只有影片的資產產生只含音訊的輸出 |
| ErrorProcessingTask |媒體處理器在處理和內容不相關的工作時發生的其他錯誤類別。 |
| ErrorUploadingOutputAsset |上傳輸出資產時的錯誤類別 |
| ErrorCancelingTask |涵蓋嘗試取消工作時失敗的錯誤類別 |
| TransientError |涵蓋暫時性問題的錯誤類別 (例如 Azure 儲存體發生暫時性網路問題) |

若要獲得 **媒體服務** 小組的協助，請開啟 [支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相關文章
* [透過自訂 Media Encoder Standard 預設值來執行進階編碼工作](media-services-custom-mes-presets-with-dotnet.md)
* [配額和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
