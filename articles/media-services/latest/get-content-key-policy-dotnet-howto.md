---
title: 使用 Azure 媒體服務 v3 .NET 從原則取得簽署金鑰
description: 本主題示範如何使用媒體服務 v3 .NET SDK，從現有原則取得簽署金鑰。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80065970"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>從現有原則取得簽署金鑰

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 Api 不會在**取得**或**列出**作業時傳回秘密或認證。 請參閱這裡的詳細說明：如需詳細資訊，請參閱[RBAC 和媒體服務帳戶](rbac-overview.md)

本文中的範例示範如何使用 .NET 從現有原則取得簽署金鑰。 
 
## <a name="download"></a>下載 

使用以下命令，將包含完整 .NET 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
具有祕密的 ContentKeyPolicy 範例位於 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) 資料夾中。

## <a name="get-contentkeypolicy-with-secrets"></a>取得具有祕密的 ContentKeyPolicy 

若要取得金鑰，請使用 **GetPolicyPropertiesWithSecretsAsync**，如下面範例所示。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>後續步驟

[設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md) 
