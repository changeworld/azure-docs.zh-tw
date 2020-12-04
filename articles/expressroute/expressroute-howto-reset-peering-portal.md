---
title: Azure ExpressRoute：使用 Azure 入口網站重設線路對等互連
description: 瞭解如何使用 Azure 入口網站來停用和啟用 Azure ExpressRoute 線路的對等互連。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581875"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>重設 ExpressRoute 線路對等互連使用 Azure 入口網站

本文說明如何使用 Azure 入口網站來停用和啟用 ExpressRoute 線路的對等互連。 當您停用對等互連時，ExpressRoute 線路的主要和次要連線的 BGP 會話將會關閉。 您將無法透過此對等互連連線到 Microsoft。 當您啟用對等互連時，ExpressRoute 線路的主要和次要連線上的 BGP 會話將會啟動。 您將會透過此對等互連重新取得與 Microsoft 之間的連線能力。 您可以在 ExpressRoute 線路上單獨啟用和停用 Microsoft 對等互連和 Azure 私用對等互連。 當您第一次在 ExpressRoute 線路上設定對等互連時，預設會啟用對等互連。

有數個案例可能會讓您覺得重設 ExpressRoute 對等互連很實用。
* 測試您的災害復原設計和實作。 例如，您有兩個 ExpressRoute 線路。 您可以停用一個線路的對等互連，並強制將網路流量容錯移轉至另一個線路。
* 在 Azure 私人對等互連或 ExpressRoute 線路的 Microsoft 對等互連上啟用雙向轉送偵測 (BFD) 。 如果您的 ExpressRoute 線路是在 1 2018 年8月和 Microsoft 對等互連之後建立的，則預設會在 Azure 私人對等互連上啟用 BFD。 如果您的 ExpressRoute 線路在 10 2020 年1月之後建立。 如果您的線路是在那之前建立的，則不會啟用 BFD。 您可以藉由停用對等互連並重新啟用它來啟用 BFD。 

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

## <a name="reset-a-peering"></a>重設對等互連

1. 選取您想要進行對等互連設定變更的線路。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute 線路清單":::

1. 選取您要啟用或停用的對等互連設定。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute 線路總覽":::

1. 取消核取 [ **啟用對等** ]，然後選取 [ **儲存** ] 以停用對等設定

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="停用私人對等互連":::

1. 您可以勾選 [ **啟用對等** ]，然後選取 [ **儲存**]，以重新啟用對等互連。

## <a name="next-steps"></a>後續步驟
如果您需要針對 ExpressRoute 問題進行疑難排解的協助，請參閱下列文章：
* [確認 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
* [網路效能疑難排解](expressroute-troubleshooting-network-performance.md)
