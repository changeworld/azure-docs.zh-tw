---
title: 在 Azure 堡壘中使用 VM 和 NSG
description: 本文介紹如何將 NSG 訪問與 Azure 堡壘合併
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087266"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>使用 NSG 訪問和 Azure 堡壘

使用 Azure 堡壘時，可以使用網路安全性群組 （NSG）。 有關詳細資訊，請參閱[安全性群組](../virtual-network/security-overview.md)。 

![架構](./media/bastion-nsg/nsg-architecture.png)

在此圖表中：

* 堡壘主機部署到虛擬網路。
* 使用者使用任何 HTML5 瀏覽器連線到 Azure 入口網站。
* 使用者導航到 Azure 虛擬機器到 RDP/SSH。
* 連接集成 - 在瀏覽器內按一下 RDP/SSH 會話
* Azure VM 上不需要公用 IP。

## <a name="network-security-groups"></a><a name="nsg"></a>網路安全性群組

本節顯示使用者和 Azure 堡壘之間的網路流量，以及虛擬網路中的目標 VM 的網路流量：

### <a name="azurebastionsubnet"></a>Azure 堡壘子網

Azure 堡壘專門部署到 Azure Bastion 子網。

* **入口流量：**

   * **來自公共互聯網的入侵流量：** Azure 堡壘將創建一個公共 IP，該公共 IP 需要在公共 IP 上啟用埠 443 才能用於入口流量。 埠 3389/22 不需要在 AzureBastion Subnet 上打開。
   * **來自 Azure 堡壘控制平面的入口流量：** 對於控制平面連接，啟用埠 443 從**閘道管理器**服務標記入站。 這使控制平面（即閘道管理器）能夠與 Azure 堡壘進行對話。

* **出口流量：**

   * **面向 VM 的出入口流量：** Azure 堡壘將通過專用 IP 到達目標 VM。 NSG 需要允許從流量到埠 3389 和 22 的其他目標 VM 子網。
   * **向 Azure 中的其他公共終結點的流量：** Azure 堡壘需要能夠連接到 Azure 中的各種公共終結點（例如，用於存儲診斷日誌和計量日誌）。 因此，Azure 堡壘需要出站到 443 到**Azure 雲服務**標記。

* **目標 VM 子網：** 這是包含要 RDP/SSH 的目標虛擬機器的子網。

   * **來自 Azure 堡壘的入侵流量：** Azure 堡壘將通過專用 IP 到達目標 VM。 RDP/SSH 埠（埠 3389/22）需要在目標 VM 端通過專用 IP 打開。 最佳做法是，可以在此規則中添加 Azure 堡壘子網 IP 位址範圍，以便僅允許 Bastion 能夠在目標 VM 子網中的目標 VM 上打開這些埠。

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>將 NSG 應用於 Azure Bastion 子網

如果創建 NSG 並將其應用於***AzureBastionSubnet，*** 請確保在 NSG 中添加了以下規則。 如果不添加這些規則，NSG 創建/更新將失敗：

* **控制平面連接：** 從閘道管理器進入 443
* **診斷日誌記錄和其他：** 從 443 到 AzureCloud 的出站。 此服務標記中的區域標記尚不受支援。
* **目標 VM：** 3389 和 22 到虛擬網路的出站

NSG 規則示例可用於此[快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)中的參考。

## <a name="next-steps"></a>後續步驟

有關 Azure 堡壘的詳細資訊，請參閱[常見問題解答](bastion-faq.md)。
