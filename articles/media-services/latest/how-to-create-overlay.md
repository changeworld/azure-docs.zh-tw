---
title: 如何使用媒體編碼器標準建立覆蓋
description: 瞭解如何使用媒體編碼器標準建立重迭。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267321"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>如何使用媒體編碼器標準建立覆蓋

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard 可讓您在現有影片上疊加影像。 目前支援下列格式：png、jpg、gif 及 bmp。

## <a name="prerequisites"></a>先決條件

* 收集您需要在範例中設定 *appsettings.js* 檔案的帳戶資訊。 如果您不確定該怎麼做，請參閱 [快速入門：使用 Microsoft 身分識別平臺註冊應用程式](../../active-directory/develop/quickstart-register-app.md)。 檔案中的 *appsettings.js* 應為下列值。

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

如果您還不熟悉轉換，建議您完成下列活動：

* [使用媒體服務讀取編碼影片和音訊](encoding-concept.md)
* 閱讀 [如何使用自訂轉換進行編碼-.net](customize-encoder-presets-how-to.md)。 遵循該文章中的步驟，設定使用轉換所需的 .NET，然後返回此處以試用重迭的預設範例。
* 請參閱 [轉換參考檔](/rest/api/media/transforms)。

當您熟悉轉換之後，請下載重迭範例。

## <a name="overlays-preset-sample"></a>重迭預設值範例

下載 [媒體服務](https://github.com/Azure-Samples/media-services-overlays) 重迭範例以開始使用重迭。

## <a name="next-steps"></a>後續步驟

* [使用媒體服務編碼時子剪輯影片-.NET](subclip-video-dotnet-howto.md)