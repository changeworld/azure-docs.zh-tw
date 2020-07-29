---
title: 將 Azure 資源移至另一個區域
description: 概述如何跨 Azure 區域移動 Azure 資源。
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75485204"
---
# <a name="moving-azure-resources-across-regions"></a>跨區域移動 Azure 資源

本文提供在 Azure 區域之間移動 Azure 資源的相關資訊。

Azure 地理位置、區域和可用性區域形成 Azure 全球基礎結構的基礎。 Azure[地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置通常包含兩個或多個[azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 區域是地理位置內的區域，包含可用性區域和多個資料中心。 

在特定 Azure 區域中部署資源之後，您可能會想要將資源移到不同的區域。

- **對應至區域啟動**：將您的資源移至先前無法使用的新引進 Azure 區域。
- **服務/功能的對齊**：移動資源以利用特定區域中可用的服務或功能。
- **回應商務發展**：將資源移至區域以回應業務變更，例如合併或收購。
- **鄰近**性：將資源移至您業務的本機區域。
- **符合資料需求**：移動資源以符合資料常駐需求或資料分類需求。 [深入了解](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **回應部署需求**：移動錯誤部署的資源，或移動以回應容量需求。 
- 因區域解除委任而因應**解除**委任：移動資源。

## <a name="move-process"></a>移動進程

實際的移動程式取決於您要移動的資源。 不過，有一些常見的主要步驟：

- **驗證必要條件**：必要條件包括確定目的地區域中可使用您所需的資源、檢查是否有足夠的配額，以及確認您的訂用帳戶可以存取目的地區域。
- **分析**相依性：您的資源可能相依于其他資源。 在移動之前，請先找出相依性，讓移動的資源在移動之後繼續如預期般運作。
- **準備移動**：這些是您在移動前的主要區域中採取的步驟。 例如，您可能需要匯出 Azure Resource Manager 範本，或開始將資源從來源複寫到目標。
- **移動資源**：移動資源的方式取決於它們的用途。 您可能需要在目的地區域中部署範本，或將資源故障到目標。
- **捨棄目標資源**：移動資源之後，您可能會想要立即查看目的地區域中的資源，並決定是否有任何不需要的專案。
- **認可移動**：確認目的地區域中的資源之後，某些資源可能需要最終的認可動作。 例如，在現在是主要區域的目的地區域中，您可能需要將嚴重損壞修復設定為新的次要區域。 
- **清除來源**：最後，當所有專案都在新區域中啟動並執行之後，您就可以清除和解除委任您為移動所建立的資源，以及主要區域中的資源。



## <a name="next-steps"></a>後續步驟

如需哪些資源支援跨區域移動的清單，請參閱[資源的移動作業支援](region-move-support.md)。
