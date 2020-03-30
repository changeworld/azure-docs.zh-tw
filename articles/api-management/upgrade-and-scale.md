---
title: 升級和調整 Azure API 管理執行個體的規模 | Microsoft Docs
description: 本主題描述如何升級和調整 Azure API 管理執行個體的規模。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018242"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>升級和調整 Azure API 管理執行個體的規模  

客戶可以新增和移除單位來調整 Azure API 管理 (APIM) 執行個體的規模。 **單位**由專用的 Azure 資源組成，具有一定的承載容量 (以每月 API 呼叫次數表示)。 此數字不代表呼叫限制，而不是允許粗略產能規劃的最大輸送量值。 受到一些因素的影響，實際的輸送量和延遲會大不相同，例如並行連線數和速率、設定的原則種類和數目、要求和回應的大小，以及後端延遲。

每個單位的容量和價格取決於該單位所在的**階層**。 有四層供您選擇：**開發人員**、**基本**、**標準**、**進階**。 如果您需要為階層內的服務增加容量，請新增單位。 如果 APIM 執行個體中目前選取的階層不允許新增更多單位，則必須升級至更高的階層。

每個單位的價格以及可用的功能 (例如，多區域部署)，取決於您為 APIM 執行個體所選擇的階層。 [定價詳細資料](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)一文說明每一層的每單位價格和功能。 

>[!NOTE]
>[定價詳細資料](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)一文顯示每一層單位容量的大約數字。 若要取得更精確的數字，則需要查看 API 的實際情節。 請參閱 [Azure API 管理執行個體的容量](api-management-capacity.md)一文。

## <a name="prerequisites"></a>Prerequisites

若要依照本文中的步驟進行，您必須：

+ 擁有有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 擁有 APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

+ 了解 [Azure API 管理執行個體的容量](api-management-capacity.md)的概念。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>升級和調整規模  

有四層供您選擇：**開發人員**、**基本**、**標準**和**進階**。 **開發人員**層應該用來評估服務，不適用於生產環境。 **開發人員**層沒有 SLA，您無法調整此階層的規模 (新增/移除單位)。 

**基本**、**標準**和**進階**是生產階層，具有 SLA 且可調整規模。 具有 SLA 的**基本**層最便宜並可相應增加 2 單位，**標準**層最多可相應增加 4 單位。 可新增至**進階**層的單位數沒有限制。

**進階**層可讓您將單一 Azure API 管理執行個體分散到您想要的 Azure 區域，而且數目不限。 最初建立 Azure API 管理服務時，執行個體只包含一個單位，而且位於單一 Azure 區域中。 初始區域會指定為**主要**區域。 您可以輕鬆新增更多區域。 新增區域時，您需要指定想要配置的單位數。 例如，您在**主要**區域中可以有一個單位，而在其他某些區域中有五個單位。 您可以依每個區域中的流量來調整單位數。 如需詳細資訊，請參閱[如何將 Azure API 管理服務執行個體部署到多個 Azure 區域](api-management-howto-deploy-multi-region.md)。

您可以升級到任何階層，也能從任何階層降級。 請注意，升級或降級可以移除某些功能；例如，從進階層降級到標準層或基本層時，就會移除 VNET 或多區域部署。

> [!NOTE]
> 升級或調整程序需要 15 到 45 分鐘才會生效。 完成後，您將收到通知。

> [!NOTE]
> **消耗**層中的 API 管理服務根據流量自動縮放。

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>使用 Azure 入口網站來升級和調整規模

![在 Azure 入口網站中調整 APIM](./media/upgrade-and-scale/portal-scale.png)

1. 導航到[Azure 門戶](https://portal.azure.com/)中的 APIM 實例。
2. 從功能表選取 [級別與價格]****。
3. 挑選所需階層。
4. 指定您想要新增的**單位**數。 您可以使用滑桿或輸入單位數。  
    如果您選擇**進階**層，則必須先選取區域。
5. 按下 [儲存] ****。

## <a name="downtime-during-scaling-up-and-down"></a>上下擴展過程中的停機時間
如果要從開發人員層縮放或擴展到開發人員層，則會有停機時間。 否則，沒有停機時間。 


## <a name="next-steps"></a>後續步驟

- [如何將 Azure API 管理服務執行個體部署到多個 Azure 區域](api-management-howto-deploy-multi-region.md)
- [如何自動調整 Azure API 管理服務執行個體](api-management-howto-autoscale.md)
