---
title: Azure 中的子網擴充功能 |Microsoft Docs
description: 瞭解 Azure 中的子網擴充功能。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "73587507"
---
# <a name="subnet-extension"></a>子網路延伸模組
將工作負載遷移至公用雲端需要進行仔細的規劃和協調。 其中一個重要考慮可以是保留您 IP 位址的能力。 尤其是當您的應用程式具有 IP 位址相依性，或您的合規性需求使用特定的 IP 位址時，這可能很重要。 Azure 虛擬網路可讓您使用您選擇的 IP 位址範圍來建立 VNet 和子網，以解決此問題。

當上述需求與其他需要在內部部署環境中保留應用程式的需求結合時，遷移可能有點困難。 在這種情況下，您必須在 Azure 與內部部署之間分割應用程式，而不需在任一端重新為 IP 位址重新編號。 此外，您還必須允許應用程式以相同的網路進行通訊。

上述問題的一個解決方案是子網擴充。 擴充網路可讓應用程式在不同的實體位置上時，透過相同的廣播網域來交談，而不需要重新架構您的網路拓撲。 

雖然擴充您的網路並不是理想的做法，但是在下列情況下，使用案例可能會有必要。

- **階段式遷移**：最常見的案例是您想要進行遷移的階段。 您想要在一段時間後，將應用程式的其餘部分遷移至 Azure，以引入一些應用程式。
- **延遲**：低延遲需求可能是您將一些應用程式保留在內部部署的另一個原因，以確保它們盡可能靠近您的資料中心。
- **合規性**：另一個使用案例是您可能會有合規性需求，以將部分應用程式保留在內部部署環境。
 
> [!NOTE] 
> 除非必要，否則您不應擴充子網。 在擴充子網的情況下，您應該嘗試使其成為中繼步驟。 使用時間時，您應該嘗試在內部部署網路中重新編號應用程式，並將其遷移至 Azure。

在下一節中，我們將討論如何將您的子網擴充至 Azure。


## <a name="extend-your-subnet-to-azure"></a>將您的子網延伸至 Azure
 您可以使用第3層以網路為基礎的解決方案，將您的內部部署子網延伸至 Azure。 大部分的解決方案都使用重迭的技術（例如 VXLAN），利用第3層重迭網路來擴充第2層網路。 下圖顯示一般化的解決方案。 在此解決方案中，位於 Azure 和內部部署的雙方都有相同的子網。 

![子網擴充功能範例](./media/subnet-extension/subnet-extension.png)

子網中的 IP 位址會指派給 Azure 和內部部署的 Vm。 Azure 和內部部署都在其網路中插入 NVA。 當 Azure 中的 VM 嘗試與內部部署網路中的 VM 通訊時，Azure NVA 會捕捉封包、將其封裝，然後透過 VPN/Express 路由傳送至內部部署網路。 內部部署 NVA 會接收封包，解除該封包，並將其轉送到其網路中的預定收件者。 傳回流量會使用類似的路徑和邏輯。

在上述範例中，Azure NVA 和內部部署 NVA 會進行通訊，並瞭解彼此背後的 IP 位址。 更複雜的網路也可以有對應服務，這會維護 Nva 與其背後 IP 位址之間的對應。 當 NVA 接收到封包時，它會查詢對應服務，以找出其後面有目的地 IP 位址的 NVA 位址。

在下一節中，您會找到我們在 Azure 上測試的子網擴充解決方案的詳細資料。

## <a name="next-steps"></a>後續步驟 
[使用廠商解決方案將您的子網延伸至 Azure。](https://github.com/microsoft/Azure-LISP)