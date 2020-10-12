---
title: 服務配額和區域可用性
description: Azure Container Instances 服務的配額、限制和區域可用性。
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384818"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Azure Container Instances 的配額和限制

所有 Azure 服務都包含資源和功能的特定預設限制和配額。 本文詳述 Azure Container Instances 的預設配額和限制。

Azure 容器實例的計算、記憶體和儲存體資源可用性會因區域和作業系統而有所不同。 如需詳細資訊，請參閱 [Azure 容器實例的資源可用性](container-instances-region-availability.md)。

使用 [清單使用方式](/rest/api/container-instances/location/listusage) API 來查看訂用帳戶的區域中目前的配額使用量。

## <a name="service-quotas-and-limits"></a>服務配額和限制

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>接下來的步驟

某些預設限制和配額可以增加。 若要要求增加一或多個支援此類增加的資源，請提交 [Azure 支援要求][azure-support] (針對 [問題類型]**** 選取 [配額])。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
