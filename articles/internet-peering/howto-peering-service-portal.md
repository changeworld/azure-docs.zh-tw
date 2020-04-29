---
title: 使用 Azure 入口網站在直接對等互連上啟用 Azure 對等服務
titleSuffix: Azure
description: 使用 Azure 入口網站在直接對等互連上啟用 Azure 對等服務
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687062"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>使用 Azure 入口網站在直接對等互連上啟用 Azure 對等服務

本文說明如何使用 Azure 入口網站在直接對等互連上啟用 Azure 對[等服務](overview-peering-service.md)。

如果您想要的話，可以使用[PowerShell](howto-peering-service-powershell.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先參閱[必要條件](prerequisites.md)。
* 在您的訂用帳戶中，選擇您想要啟用對等互連服務的直接對等互連。 如果您沒有，請轉換舊版直接對等互連，或建立新的直接對等互連：
    * 若要轉換舊版直接對等互連，請依照[使用入口網站將舊版直接對等互連轉換至 Azure 資源](howto-legacy-direct-portal.md)中的指示進行。
    * 若要建立新的直接對等互連，請依照[使用入口網站建立或修改直接對等互連](howto-direct-portal.md)中的指示進行。

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接對等互連上啟用對等互連服務

### <a name="view-direct-peering"></a><a name= get></a>觀看直接對等互連
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>啟用對等互連服務的直接對等互連

在上一個步驟中開啟直接對等互連之後，請將它啟用對等互連服務。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接對等互連連線

若要修改連線設定，請參閱[使用入口網站建立或修改直接對等互連](howto-direct-portal.md)中的「修改直接對等互連」一節。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

如需常見問題，請參閱對[等互連服務常見問題](service-faqs.md)。