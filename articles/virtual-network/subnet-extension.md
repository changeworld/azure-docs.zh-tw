---
title: Azure 中的子網擴展 |微軟文檔
description: 瞭解 Azure 中的子網擴展。
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587507"
---
# <a name="subnet-extension"></a>子網路延伸模組
工作負載遷移到公共雲需要仔細規劃和協調。 關鍵考慮因素之一可能是保留 IP 位址的能力。 這一點非常重要，尤其是在應用程式具有 IP 位址依賴關係或您有使用特定 IP 位址的合規性要求時。 Azure 虛擬網路允許您使用您選擇的 IP 位址範圍創建 VNet 和子網，從而解決了此問題。

當上述要求加上將某些應用程式保留在本地的附加要求時，遷移可能會變得有點困難。 在這種情況下，您必須在 Azure 和本地之間拆分應用程式，而無需重新編號任何一側的 IP 位址。 此外，您必須允許應用程式像在同一網路中一樣進行通信。

解決上述問題的一個解決方案是子網擴展。 擴展網路允許應用程式在不同物理位置存在時討論同一廣播域，無需重新構建網路拓撲。 

雖然擴展網路通常不是一個好的做法，但下面的用例可能使得有必要這樣做。

- **分階段遷移**：最常見的方案是您希望分階段遷移。 您希望首先將一些應用程式帶來，隨著時間的推移，將其餘應用程式遷移到 Azure。
- **延遲**：低延遲要求可能是您保留某些應用程式本地以確保它們盡可能靠近您的資料中心的另一個原因。
- **合規性**：另一個用例是，您可能有合規性要求，可以保留某些應用程式在本地。
 
> [!NOTE] 
> 除非有必要，否則不應擴展子網。 在擴展子網的情況下，應嘗試將其作為中間步驟。 隨著時間的推移，應嘗試對本地網路中的應用程式重新編號，並將它們遷移到 Azure。

在下一節中，我們將討論如何將子網擴展到 Azure。


## <a name="extend-your-subnet-to-azure"></a>將子網擴展到 Azure
 您可以使用基於第 3 層覆蓋網路的解決方案將本地子網擴展到 Azure。 大多數解決方案使用覆蓋技術（如 VXLAN）使用第 3 層疊加網路擴展第 2 層網路。 下圖顯示了一個通用的解決方案。 在此解決方案中，Azure 和本地兩側都存在相同的子網。 

![子網擴展示例](./media/subnet-extension/subnet-extension.png)

子網中的 IP 位址分配給 Azure 和本地的 VM。 Azure 和本地網路都插入了 NVA。 當 Azure 中的 VM 嘗試與本地網路中的 VM 對話時，Azure NVA 會捕獲資料包、封裝資料包，並通過 VPN/快速路由將其發送到本地網路。 本地 NVA 接收資料包，將其解包並將其轉發到其網路中的目標收件者。 返回流量使用類似的路徑和邏輯。

在上面的示例中，Azure NVA 和本地 NVA 相互通信和瞭解 IP 位址。 更複雜的網路還可以有映射服務，該服務維護 NVA 及其後面的 IP 位址之間的映射。 當 NVA 收到資料包時，它會查詢映射服務以找出其背後具有目標 IP 位址的 NVA 的位址。

在下一節中，您將找到我們在 Azure 上測試的子網擴展解決方案的詳細資訊。

## <a name="next-steps"></a>後續步驟 
[使用供應商解決方案將子網擴展到 Azure。](https://github.com/microsoft/Azure-LISP)