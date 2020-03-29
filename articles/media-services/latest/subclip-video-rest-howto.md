---
title: 使用 Azure 媒體服務 REST 進行編碼時，對視頻進行子剪輯
description: 本主題介紹如何使用 REST 使用 Azure 媒體服務進行編碼時對視頻進行子剪輯
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514318"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>使用媒體服務進行編碼時，對視頻進行子剪輯 - REST

您可以使用[作業](https://docs.microsoft.com/rest/api/media/jobs)對其進行編碼時修剪或子剪輯視頻。 此功能適用于使用[內置標準編碼預設預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)或[標準編碼預設](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)構建的任何[轉換](https://docs.microsoft.com/rest/api/media/transforms)。 

本主題中的 REST 示例創建一個作業，該作業在提交編碼作業時修剪視頻。 

## <a name="prerequisites"></a>Prerequisites

若要完成此主題中所述的步驟，您必須：

- [創建 Azure 媒體服務帳戶](create-account-cli-how-to.md)。
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。
    
    請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 
- 創建轉換和輸出資產。 您可以看到如何在基於 URL 編碼遠端檔中創建轉換和輸出資產[，並資料流視頻 - REST](stream-files-tutorial-with-rest.md)教程。
- 查看[編碼概念](encoding-concept.md)主題。

## <a name="create-a-subclipping-job"></a>創建子裁剪作業

1. 在下載的 Postman 集合中，選擇 **"轉換"和"使用** -> **子剪輯創建作業**"。
    
    **PUT**請求如下所示：
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 使用轉換名稱更新"轉換名稱"環境變數的值。 
1. 選擇 **"正文**"選項卡，然後使用輸出資產名稱更新"我的輸出資產"。

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
1. 按 [傳送]****。

    您將看到 **"回應"，** 其中包含有關創建和提交的作業的資訊以及作業的狀態。 

## <a name="next-steps"></a>後續步驟

[如何使用自訂轉換進行編碼](custom-preset-rest-howto.md) 
