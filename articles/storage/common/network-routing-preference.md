---
title: 設定網路路由喜好設定 (預覽)
titleSuffix: Azure Storage
description: 設定 Azure 儲存體帳戶的網路路由喜好設定 (預覽)，以指定如何透過網際網路將網路流量從用戶端路由至您的帳戶。
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.openlocfilehash: 5b4a1b1f27dff059090d78e24a6a0eca0bbbf01f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514207"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>設定 Azure 儲存體的網路路由喜好設定 (預覽)

您可以設定 Azure 儲存體帳戶的網路[路由喜好設定](../../virtual-network/routing-preference-overview.md) (預覽)，以指定如何透過網際網路將網路流量從用戶端路由至您的帳戶。 根據預設，來自網際網路的流量會透過 [Microsoft 全域網路](../../networking/microsoft-global-network.md)路由至儲存體帳戶的公用端點。 Azure 儲存體提供其他選項，讓您設定如何將流量路由至儲存體帳戶。

設定路由喜好設定可讓您有彈性地將流量最佳化，以提升網路效能或降低成本。 設定路由喜好設定時，您可以指定如何依預設將流量導向至儲存體帳戶的公用端點。 您也可以為儲存體帳戶發佈路由特定的端點。

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft 全域網路與網際網路路由

根據預設，Azure 環境以外的用戶端會透過 Microsoft 全域網路存取您的儲存體帳戶。 Microsoft 全域網路已針對低延遲路徑選取進行最佳化，以提供高可靠性的優質網路效能。 輸入和輸出流量都會透過最靠近用戶端的存在點 (POP) 進行路由。 此預設路由設定可確保進出於儲存體帳戶的流量大多數的路徑都會流經 Microsoft 全域網路，而將網路效能最大化。

您可以變更儲存體帳戶的路由設定，使 Azure 環境以外的用戶端在輸入和輸出流量時，都會透過最接近儲存體帳戶的 POP 進行路由。 此路由能夠盡可能簡化透過 Microsoft 全域網路的流量周遊，並及早將其交給運輸 ISP。 利用此路由設定，可降低網路成本。

下圖顯示每個路由喜好設定在用戶端與儲存體帳戶之間傳輸流量的方式：

![Azure 儲存體的路由選項概觀](media/network-routing-preference/routing-options-diagram.png)

若要深入了解 Azure 中的路由喜好設定，請參閱[什麼是路由喜好設定 (預覽)？](../../virtual-network/routing-preference-overview.md)。

## <a name="routing-configuration"></a>路由設定

您可以從 Microsoft 全域網路與網際網路路由中擇一作為儲存體帳戶公用端點的預設路由喜好設定。 預設路由喜好設定適用於所有來自 Azure 外部用戶端的流量，且會對 Azure Data Lake Storage Gen2、Blob 儲存體、Azure 檔案儲存體和靜態網站的端點產生影響。 Azure 佇列或 Azure 資料表不支援路由喜好設定的設定。

您也可以為儲存體帳戶發佈路由特定的端點。 當您發佈路由特定的端點時，Azure 儲存體會為您的儲存體帳戶建立新的公用端點，而透過所需的路徑路由流量。 這種彈性可讓您透過特定路由將流量導向至您的儲存體帳戶，而無須變更預設的路由喜好設定。

例如，若為 'StorageAccountA' 發佈網際網路路由特定的端點，則會為您的儲存體帳戶發佈下列端點：

| 儲存體服務        | 路由特定端點                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob 服務           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| 檔案服務           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| 靜態網站        | `StorageAccountA-internetrouting.web.core.windows.net`   |

如果您有讀取權限異地備援儲存體 (RA-GRS) 或讀取權限異地區域備援儲存體 (RA-GZRS) 儲存體帳戶，則在發佈路由特定端點時，也會自動在次要區域中建立提供讀取權限的對應端點。

| 儲存體服務        | 路由特定的唯讀次要端點                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob 服務           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| 檔案服務           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| 靜態網站        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

已發佈之路由特定端點的連接字串可透過 [Azure 入口網站](https://portal.azure.com)來複製。 這些連接字串可用於所有現有 Azure 儲存體 SDK 和 API 的共用金鑰授權。

## <a name="about-the-preview"></a>關於預覽

Azure 儲存體的路由喜好設定適用於下列區域：

- 法國南部
- 美國中北部
- 美國中西部

下列已知問題會影響到 Azure 儲存體的路由喜好設定 (預覽)：

- 對 Microsoft 全域網路的路由特定端點提出的存取要求會失敗，且發生 HTTP 錯誤 404 或對等錯誤。 透過 Microsoft 全域網路的路由在設定為公用端點的預設路由喜好設定時，將可正常運作。

## <a name="pricing-and-billing"></a>價格和計費

如需定價和計費的詳細資料，請參閱[什麼是路由喜好設定 (預覽)？](../../virtual-network/routing-preference-overview.md#pricing)中的**定價**一節。

## <a name="next-steps"></a>後續步驟

- [什麼是路由喜好設定 (預覽)？](../../virtual-network/routing-preference-overview.md)
- [設定 Azure 儲存體防火牆和虛擬網路](storage-network-security.md)
- [Blob 儲存體的安全性建議](../blobs/security-recommendations.md)
