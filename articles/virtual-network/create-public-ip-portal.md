---
title: 建立公用 IP-Azure 入口網站
description: 瞭解如何在 Azure 入口網站中建立公用 IP
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: bd35d3288591a01c14e306960601eef6dea7e761
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954136"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立公用 IP 位址

本文說明如何使用 Azure 入口網站建立公用 IP 位址資源。 如需有關這可以關聯哪些資源的詳細資訊，基本和標準 SKU 之間的差異，以及其他相關資訊，請參閱 [公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)。  在此範例中，我們只會將焦點放在 IPv4 位址;如需 IPv6 位址的詳細資訊，請參閱 [Azure VNet 的 ipv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)。

# <a name="standard-sku---using-zones"></a>[**標準 SKU-使用區域**](#tab/option-create-public-ip-standard-zones)

使用下列步驟來建立名為 **myStandardZRPublicIP** 的標準區域冗余公用 IP 位址。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [建立資源]。 
3. 在搜尋方塊中，輸入「公用 IP 位址」。
4. 在搜尋結果中，選取「公用 IP 位址」。 接下來，在 [公用 IP 位址] 頁面中，選取 [建立]。
5. 在 [ **建立公用 IP 位址** ] 頁面上，輸入或選取下列資訊： 

    | 設定                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 選取 IPv4                 |    
    | SKU                     | 選取 [標準]         |
    | 階層 (（如有顯示） * )                   | 選取 **區域**         |
    | 名稱                    | 輸入 *myStandardZRPublicIP*          |
    | IP 位址指派   | 請注意，這會鎖定為「靜態」                                        |
    | 閒置超時 (分鐘)   | 將值保留為4        |
    | DNS 名稱標籤          | 將值保留為空白    |
    | 訂用帳戶            | 選取您的訂用帳戶。   |
    | 資源群組          | 選取 [ **建立新** 的]，輸入 myResourceGroup，然後選取 **[確定]** |
    | 位置                | 選取 **美國東部 2**      |
    | 可用性區域       | 選取 **區域-多餘** 或挑選特定區域 (請參閱下方的附注)  |

請注意，這些只是具有 [可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)之區域中的有效選項。   (您也可以在這些區域中選取特定區域，不過它將無法在區域失敗時復原。 ) 

\* = 階層與 [跨區域 Load Balancer](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) 功能（目前為預覽狀態）相關。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-create-public-ip-basic)

使用下列步驟來建立名為 **myBasicPublicIP** 的基本靜態公用 IP 位址。  基本公用 Ip 沒有可用性區域的概念。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [建立資源]。 
3. 在搜尋方塊中，輸入「公用 IP 位址」。
4. 在搜尋結果中，選取「公用 IP 位址」。 接下來，在 [公用 IP 位址] 頁面中，選取 [建立]。
5. 在 [ **建立公用 IP 位址** ] 頁面上，輸入或選取下列資訊： 

    | 設定                 | 值                       |
    | ---                     | ---                         |
    | IP 版本              | 選取 IPv4                 |    
    | SKU                     | 選取 [標準]         |
    | 名稱                    | 輸入 *myBasicPublicIP*          |
    | IP 位址指派   | 選擇 **靜態** (請參閱下面的附注)                                      |
    | 閒置超時 (分鐘)   | 將值保留為4        |
    | DNS 名稱標籤          | 將值保留為空白    |
    | 訂用帳戶            | 選取您的訂用帳戶。   |
    | 資源群組          | 選取 [ **建立新** 的]，輸入 myResourceGroup，然後選取 **[確定]** |
    | 位置                | 選取 **美國東部 2**      |

如果可接受 IP 位址在一段時間內變更，就可以選取 **動態** ip 指派。

---

## <a name="additional-information"></a>其他資訊 

如需上列個別欄位的詳細資訊，請參閱 [管理公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)。

## <a name="next-steps"></a>後續步驟
- 將[公用 IP 位址與虛擬機器](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)產生關聯
- 深入了解 Azure 中的[公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 深入了解所有[公用 IP 位址設定](virtual-network-public-ip-address.md#create-a-public-ip-address)。