---
title: 什麼是 Azure Resource Mover？
description: 了解 Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 621c5e7b0061ccd76fd0109552107915b943511f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653262"
---
# <a name="what-is-azure-resource-mover"></a>什麼是 Azure Resource Mover？

本文提供 Azure Resource Mover 服務的概觀。 Resource Mover 可協助您在 Azure 區域之間移動 Azure 資源。

您可以將資源移至不同的 Azure 區域，以便：

- **對齊區域啟動**：將資源移至先前無法使用的新引進 Azure 區域。
- **對齊服務/功能**：移動資源以利用特定區域中可用的服務或功能。
- **回應商業發展**：將資源移至某個區域以回應商業變化，例如合併或收購。
- **對齊鄰近性**：將資源移至您公司所在的區域。
- **符合資料需求**：移動資源以對齊資料落地需求或資料分類需求。
- **回應部署需求**：移動部署錯誤的資源，或進行移動以回應容量需求。
- **回應退役**：因為區域已退役而移動資源。

> [!IMPORTANT]
> Azure Resource Mover 目前處於公開預覽狀態。

## <a name="why-use-resource-mover"></a>為何要使用 Resource Mover？

Resource Mover 提供：

- 用於在區域之間移動資源的單一中樞。
- 減少移動時間和複雜度。 您需要的所有項目都在單一位置。
- 移動不同類型 Azure 資源的簡單且一致體驗。
- 一個簡單的方法，可識別您想要移動的資源之間的相依性。 這可協助您將相關的資源一起移動，以便在移動之後，所有項目在目標區域中都能如預期般運作。
- 自動清除來源區域中的資源 (如果您想要在移動之後將其刪除)。
- 測試： 如果您不想要進行完整移動，您可以嘗試移動，然後將其捨棄。

## <a name="move-across-regions"></a>跨區域移動

若要跨區域移動資源，請選取您想要移動的資源。 Resource Mover 會驗證這些資源，並解決其對其他資源的任何相依性。 如果有相依性，您會有幾個選項：
- 將相依資源移至目標區域。
- 不移動相依資源，但改為使用目標區域中的對等資源。

所有相依性均獲得解決後，Resource Mover 便會引導您完成簡單的移動程序。

1. 您啟動初始移動。
2. 初始移動過後，您可以決定要認可並完成移動，還是要捨棄移動。
3. 移動完成後，您可以決定是否要刪除來源位置中的資源。

您可以在 Resource Mover 中樞的區域之間移動資源，也可以從資源群組內移動資源。 [深入了解](select-move-tool.md) 

## <a name="what-resources-can-i-move-across-regions"></a>我可以跨區域移動哪些資源？

您目前可以使用 Resource Mover 跨區域移動下列資源：

- Azure VM 和相關聯的磁碟
- NIC
- 可用性設定組 
- Azure 虛擬網路 
- 公用 IP 位址
- 網路安全性群組 (NSG)。
- 內部和公用負載平衡器 
- Azure SQL 資料庫和彈性集區


## <a name="next-steps"></a>後續步驟

[深入了解](about-move-process.md) Resource Mover 元件和移動程序。
