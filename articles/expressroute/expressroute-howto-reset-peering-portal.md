---
title: Azure ExpressRoute：使用 Azure 入口網站重設線路對等互連
description: 瞭解如何使用 Azure 入口網站來停用和啟用 Azure ExpressRoute 線路的對等互連。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680281"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>使用 Azure 入口網站重設 ExpressRoute 線路對等互連

本文說明如何使用 Azure 入口網站來停用和啟用 Azure ExpressRoute 線路的對等互連。 當您停用對等互連時，您的 ExpressRoute 線路主要和次要連線的邊界閘道協定 (BGP) 會話都會關閉。 當您啟用對等互連時，您的 ExpressRoute 線路主要和次要連線上的 BGP 會話都會還原。

> [!Note]
> 當您第一次在 ExpressRoute 線路上設定對等互連時，預設會啟用對等互連。

在下列案例中，重設您的 ExpressRoute 對等互連可能會很有説明：

* 您正在測試嚴重損壞修復的設計和實行。 例如，假設您有兩個 ExpressRoute 線路。 您可以停用某一電路的對等互連，並強制網路流量使用其他線路。

* 您想要在 Azure 私人對等互連或 Microsoft 對等互連上啟用 (BFD) 的雙向轉送偵測。 如果您的 ExpressRoute 線路是在2018年8月1日之前建立的，在 Azure 私人對等互連上，或在2020年1月10日之前建立，則預設不會啟用 BFD。 重設對等互連以啟用 BFD。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

從瀏覽器移至 [Azure 入口網站](https://portal.azure.com)，然後使用您的 Azure 帳戶登入。

## <a name="reset-a-peering"></a>重設對等互連

您可以將 Microsoft 對等互連和 Azure 私用對等互連個別重設為 ExpressRoute 線路。

1. 選擇您要變更的線路。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="顯示在 ExpressRoute 線路清單中選擇電路的螢幕擷取畫面。":::

1. 選擇您想要重設的對等互連設定。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="顯示在 ExpressRoute 電路總覽中選擇對等互連的螢幕擷取畫面。":::

1. 清除 [ **啟用對等** ] 核取方塊，然後選取 [ **儲存** ] 以停用對等互連設定。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="顯示清除 [啟用對等] 核取方塊的螢幕擷取畫面。":::

1. 選取 [ **啟用對等互連** ] 核取方塊，然後選取 [ **儲存** ] 以重新啟用對等互連設定。

## <a name="next-steps"></a>後續步驟

若要針對 ExpressRoute 問題進行疑難排解，請參閱下列文章：

* [確認 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
* [網路效能疑難排解](expressroute-troubleshooting-network-performance.md)
