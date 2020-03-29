---
title: 在 Azure 入口網站中將檔案上傳至媒體服務帳戶 | Microsoft Docs
description: 本教學課程逐步引導您完成在 Azure 入口網站中將檔案上傳至媒體服務帳戶的步驟。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 3ebeb3c601dd3f734265d49d60728056561928be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61127800"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>在 Azure 入口網站中將檔案上傳至媒體服務帳戶 

> [!div class="op_single_selector"]
> * [入口網站](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [休息](media-services-rest-upload-files.md)
> 

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>查看最新版本，[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，請參閱[從 v2 到 v3 的遷移指南](../latest/migrate-from-v2-to-v3.md)

在 Azure 媒體服務中，您會將數位檔案上傳到到資產。 資產可以包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式輔助字幕檔案 (以及這些檔案的中繼資料)。 上傳檔案之後，您的內容會安全地儲存在雲端，以進一步進行處理和串流處理。

媒體服務有處理檔案的檔案大小上限。 如需有關檔案大小限制的詳細資訊，請參閱[媒體服務配額和限制](media-services-quotas-and-limitations.md)。

若要完成此教學課程，您需要 Azure 帳戶。 有關詳細資訊，請參閱[Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="upload-files"></a>上傳檔案
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 選擇 **"設置** > **資產**"。 然後，選取 [上傳]**** 按鈕。
   
    ![上傳檔案](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    [上傳視訊資產] **** 視窗隨即出現。
   
   > [!NOTE]
   > 媒體服務不會限制上傳影片的檔案大小。
 
3. 在您的電腦上，移至您要上傳的影片。 選取影片，然後選取 [確定]****。  
   
    隨即開始上傳。 您可以在檔名底下看到進度。  

上傳完成後，新資產會列在 [資產]**** 窗格中。 

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>後續步驟
* 了解如何[將上傳的資產編碼](media-services-portal-encode.md)。

* 您也可以使用 Azure Functions，以在檔案到達所設定的容器時觸發編碼作業。 如需詳細資訊，請參閱[媒體服務：整合 Microsoft Azure 媒體服務與 Azure Functions 和 Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) 中的範例。


