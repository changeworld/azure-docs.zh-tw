---
title: Azure 容器映像產品 / |Azure 應用商店
description: 在 Azure Marketplace 上發佈容器供應項目的流程概觀。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: cd9f98d42efcb35dbab4f3c0a06c5a11360e36b6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270344"
---
# <a name="containers"></a>容器

> [!IMPORTANT]
> 從 2020 年 4 月 13 日開始,我們將開始將 Azure 容器產品的管理移動到合作夥伴中心。 遷移後,您將在合作夥伴中心創建和管理您的優惠。 按照[「創建 Azure 容器產品/服務](https://aka.ms/CreateContainerOffer)」中的說明進行操作,以管理遷移的優惠。

<table> <tr> <td>本節說明如何將容器映像發佈到 <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>。  
容器供應項目類型支援當作 <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> 執行個體或 <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container Instances</a> 佈建，並裝載於 <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> 存放庫的 Docker 容器映像。 </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>供應項目元件

本節概略介紹發佈容器的要素，預計會做為 Azure Marketplace 發佈者的指南。 發佈將分為以下幾個主要部分：

- [先決條件](./cpp-prerequisites.md)：列出建立或發佈容器供應項目前，需符合的技術與商業需求。
- [建立供應項目](./cpp-create-offer.md)：列出使用 Cloud Partner 入口網站建立新容器供應項目所需的步驟。
- [準備技術資產](./cpp-create-technical-assets.md)：如何在 Azure Marketplace 上建立容器解決方案的技術資產作為供應項目。
- [發佈供應項目](./cpp-publish-offer.md)：如何提交供應項目以發佈至 Azure Marketplace。

## <a name="container-publishing-process"></a>容器發佈流程

下圖說明大致的虛擬機器發佈流程。
![發佈供應項目的步驟](./media/containers-offer-process.png)

發佈容器供應項目主要步驟如下：

1. 建立供應項目：提供有關供應項目的詳細資訊。 這些資訊包括：供應項目說明、行銷資料、支援資訊，以及資產規格。
2. 建立商業及技術資產：建立商業資產 (法律文件和行銷資料) 和相關聯解決方案的技術資產 (裝載於 Azure Container Registry 的容器映像)。
3. 建立 SKU：建立與供應項目相關聯的 SKU。 您需要針對預計發佈的每個映像提供個別的 SKU。
4. 認證及發佈供應項目：供應項目和技術資產完成後，便可以提交供應項目。 提交作業將啟動發佈程序。 在此程序中，解決方案會經過測試、驗證、認證，然後在 Azure Marketplace「上線」。

## <a name="next-steps"></a>後續步驟

在考慮進行下列步驟前，您必須符合將容器發佈至 Microsoft Azure Marketplace 的[技術和商務需求](./cpp-prerequisites.md)。
