---
title: 使用 Azure 媒體服務 REST 編碼時子剪輯影片
description: 本主題說明如何在使用 REST 編碼時，使用 Azure 媒體服務來子剪輯影片
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514318"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>使用媒體服務 REST 編碼時子剪輯影片

使用[作業](https://docs.microsoft.com/rest/api/media/jobs)進行編碼時，您可以修剪或子剪輯影片。 這項功能適用于使用 [ [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ] 預設值或 [ [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) ] 預設建立的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。 

本主題中的 REST 範例會建立一項作業，以在提交編碼工作時修剪影片。 

## <a name="prerequisites"></a>先決條件

若要完成此主題中所述的步驟，您必須：

- [建立 Azure 媒體服務帳戶](create-account-cli-how-to.md)。
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。
    
    請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 
- 建立轉換和輸出資產。 您可以在以[URL 為基礎的遠端檔案編碼和串流影片 REST](stream-files-tutorial-with-rest.md)教學課程中，瞭解如何建立轉換和輸出資產。
- 請參閱[編碼概念](encoding-concept.md)主題。

## <a name="create-a-subclipping-job"></a>建立子剪輯作業

1. 在您下載的 Postman 集合中，選取 [**轉換] 和 [作業** -> ] [**建立具有子裁剪的作業**]。
    
    **PUT**要求看起來像這樣：
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 使用您的轉換名稱來更新 "transformName" 環境變數的值。 
1. 選取 [內**文] 索引標籤，並**以您的輸出資產名稱更新 "myOutputAsset"。

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. 按 [傳送]  。

    您會看到**回應**，其中包含已建立和提交之作業的相關資訊，以及作業的狀態。 

## <a name="next-steps"></a>後續步驟

[如何使用自訂轉換進行編碼](custom-preset-rest-howto.md) 
