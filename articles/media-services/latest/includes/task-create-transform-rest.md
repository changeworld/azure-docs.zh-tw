---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: JSON
ms.openlocfilehash: f1f9e83a68539f6019169cb2faed16a19ef85a90
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918086"
---
<!--Create a basic transform REST-->

下列 Azure REST 命令會建立基本的音訊資產。 `subscriptionID` `resourceGroup` `accountName` 以您目前使用的值取代值、和。 在這裡設定，以提供您的轉換名稱 `transformName` 。

將變更 `@odata.type` 為其中一個 [可用](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#definitions)的預設值，例如 `#Microsoft.Media.AudioAnalyzerPreset` 。

```

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>主體

```json
{
    "properties": {
        "description": "Transform for Audio Analyzer Basic Mode",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>範例回應
```json
{
    "name": "audioAnalyzerTransform",
    "id": "/subscriptions/35c2594a-xxxx-4fce-b59c-f6fb9513eeeb/resourceGroups/myresourcegroup/providers/Microsoft.Media/mediaservices/mymediaservicesaccount/transforms/audioAnalyzerTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-10-23T18:03:37.4793962Z",
        "description": "Transform for Audio Analyzer Basic Mode",
        "lastModified": "2020-10-23T18:03:37.4793962Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic",
                    "experimentalOptions": {
                        "basicAudioEnabled": "true"
                    }
                }
            }
        ]
    }
}

```
