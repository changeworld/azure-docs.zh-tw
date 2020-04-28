---
title: Azure 中的子網延伸模組 |Microsoft Docs
description: 瞭解 Azure 中的子網延伸模組。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73587507"
---
# <a name="subnet-extension"></a>子網路延伸模組
工作負載遷移至公用雲端需要謹慎的規劃和協調。 其中一個重要的考慮就是保留您的 IP 位址的能力。 如果您的應用程式具有 IP 位址相依性，或您有使用特定 IP 位址的合規性需求，這可能是很重要的。 Azure 虛擬網路可讓您使用您選擇的 IP 位址範圍來建立 VNet 和子網，以解決此問題。

當上述需求與其他需求結合，讓某些應用程式在內部部署時，遷移會有相當大的挑戰。 在這種情況下，您必須在 Azure 與內部部署之間分割應用程式，而不需要在任一端重新建立 IP 位址的編號。 此外，您還必須允許應用程式以相同網路中的方式進行通訊。

上述問題的其中一個解決方案是子網延伸模組。 擴充網路可讓應用程式在相同的廣播網域存在於不同的實體位置時進行交談，而不需要重新架構您的網路拓撲。 

雖然擴充您的網路在一般情況下並不是很好的作法，但以下的使用案例可能會使其成為必要

- **階段式遷移**：最常見的案例是您想要階段進行遷移。 您想要先建立幾個應用程式，並在一段時間後，將其餘的應用程式遷移至 Azure。
- **延遲**：低延遲需求可能是讓一些應用程式在內部部署環境中保留的另一個原因，以確保它們盡可能靠近您的資料中心。
- **合規性**：另一個使用案例是您可能會有合規性需求，以便在內部部署中保留一些應用程式。
 
> [!NOTE] 
> 除非必要，否則不應延伸您的子網。 在您擴充子網的情況下，您應該嘗試讓它成為中繼步驟。 有了一段時間，您應該嘗試在您的內部部署網路中重新編號應用程式，並將它們遷移至 Azure。

在下一節中，我們將討論如何將您的子網擴充到 Azure。


## <a name="extend-your-subnet-to-azure"></a>將您的子網延伸至 Azure
 您可以使用以第3層重迭的網路為基礎的解決方案，將內部部署子網延伸至 Azure。 大部分的解決方案都會使用重迭技術（例如 VXLAN），利用第3層重迭網路來擴充第2層網路。 下圖顯示一般化的解決方案。 在此解決方案中，相同的子網會同時存在於 Azure 和內部部署的兩端。 

![子網延伸模組範例](./media/subnet-extension/subnet-extension.png)

來自子網的 IP 位址會指派給 Azure 和內部部署上的 Vm。 Azure 和內部部署都會在其網路中插入 NVA。 當 Azure 中的 VM 嘗試與內部部署網路中的 VM 通訊時，Azure NVA 會捕捉封包、將它封裝起來，並透過 VPN/Express 路由傳送至內部部署網路。 內部部署 NVA 會接收封包，解除它，並將它轉送到其網路中的預定收件者。 傳回流量會使用類似的路徑和邏輯。

在上述範例中，Azure NVA 和內部部署 NVA 會彼此通訊，並瞭解彼此並存的 IP 位址。 更複雜的網路也可以有對應服務，這會維護 Nva 與它們背後的 IP 位址之間的對應。 當 NVA 收到封包時，它會查詢對應服務，以找出其後面有目的地 IP 位址的 NVA 位址。

在下一節中，您會找到我們在 Azure 上測試的子網延伸模組解決方案的詳細資料。

## <a name="next-steps"></a>後續步驟 
[使用廠商解決方案將您的子網延伸至 Azure。](https://github.com/microsoft/Azure-LISP)