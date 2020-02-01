---
title: 存取 Azure 媒體服務 API - Azure CLI | Microsoft Docs
description: 請遵循此操作說明文章中的步驟來存取 Azure 媒體服務 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 06ca26ee9a8af7f49aba6a48a1831d30d5886851
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896349"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 存取 Azure 媒體服務 API
 
若要使用 Azure AD 服務主體驗證來連線至 Azure 媒體服務 API，您的應用程式必須要求具有下列參數的 Azure AD token：

* Azure AD 租用戶端點
* 媒體服務資源 URI
* REST 媒體服務的資源 URI
* Azure AD 應用程式值：用戶端識別碼和用戶端祕密

> [!IMPORTANT]
> 如需詳細說明，請參閱[存取媒體服務 V3 api](media-services-apis-overview.md#accessing-the-azure-media-services-api)。

本主題說明如何使用 Azure CLI 建立 Azure AD 應用程式和服務主體，以及取得必要的值來存取 Azure 媒體服務資源。

## <a name="prerequisites"></a>必要條件 

[建立媒體服務帳戶](create-account-cli-how-to.md)。

請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>請參閱

- [調整媒體保留單元 - CLI](media-reserved-units-cli-how-to.md)
- [建立媒體服務帳戶 - CLI](create-account-cli-how-to.md) 
- [重設帳戶認證 - CLI](cli-reset-account-credentials.md)
- [建立資產 - CLI](cli-create-asset.md)
- [上傳檔案 - CLI](cli-upload-file-asset.md)
- [建立轉換 - CLI](cli-create-transform.md)
- [使用自訂轉換進行編碼-CLI](custom-preset-cli-howto.md)
- [建立作業 - CLI](cli-create-jobs.md)
- [建立 EventGrid - CLI](job-state-events-cli-how-to.md)
- [發佈資產 - CLI](cli-publish-asset.md)
- [篩選 - CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>後續步驟

您想要從中串流內容的串流端點必須處於 [執行中] 狀態。 下列 CLI 命令會啟動您的預設串流端點：

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

