---
title: 將 Azure 資源移動到其他區域
description: 提供跨 Azure 區域移動 Azure 資源的概述。
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485204"
---
# <a name="moving-azure-resources-across-regions"></a>跨區域移動 Azure 資源

本文提供有關跨 Azure 區域移動 Azure 資源的資訊。

Azure 地理位置、區域和可用性區域構成了 Azure 全域基礎結構的基礎。 Azure[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)通常包含兩個或多個[Azure 區域](https://azure.microsoft.com/global-infrastructure/regions/)。 區域是地理位置內的一個區域，包含可用性區域和多個資料中心。 

在特定 Azure 區域中部署資源後，可能需要將資源移動到其他區域的原因有很多。

- **與區域啟動對齊**：將資源移動到以前不可用的新引入的 Azure 區域。
- **對齊服務/功能**：移動資源以利用特定區域中可用的服務或功能。
- **回應業務發展**：將資源移動到區域，以應對業務變化，如合併或收購。
- **對齊以進行接近**：將資源移動到企業本地區域。
- **滿足資料要求**：移動資源以符合資料駐留要求或資料分類需求。 [深入了解](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **回應部署要求**：移動錯誤部署的資源，或根據容量需求移動。 
- **回應退役**：因區域退役而轉移資源。

## <a name="move-process"></a>移動過程

實際移動過程取決於要移動的資源。 但是，有一些常見的關鍵步驟：

- **驗證先決條件**：先決條件包括確保所需的資源在目的地區域中可用，檢查您是否有足夠的配額，以及驗證訂閱是否可以訪問目的地區域。
- **分析依賴項**：您的資源可能依賴于其他資源。 在移動之前，找出依賴項，以便移動的資源在移動後繼續按預期運行。
- **準備移動**：這些是您在移動之前在主要區域中採取的步驟。 例如，您可能需要匯出 Azure 資源管理器範本，或開始從源複製到目標資源。
- **移動資源**：如何移動資源取決於資源是什麼。 您可能需要在目的地區域中部署範本，或將資源容錯移轉到目標。
- **丟棄目標資源**：移動資源後，您可能需要查看目的地區域中現在的資源，並決定是否不需要任何資源。
- **提交移動**：在驗證目的地區域中的資源後，某些資源可能需要最終提交操作。 例如，在現在為主要區域的目的地區域中，您可能需要將災害復原設置為新的次要區域。 
- **清理源**：最後，在新區域中啟動並運行所有內容後，可以清理和停用為移動創建的資源以及主區域中的資源。



## <a name="next-steps"></a>後續步驟

有關資源支援跨區域移動的清單，請參閱[移動資源操作支援](region-move-support.md)。
