---
title: 設定 Vm 的私人 IP 位址-Azure 入口網站
description: 了解如何使用 Azure 入口網站設定虛擬機器的私人 IP 位址。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461543"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>使用 Azure 入口網站設定 VM 的私人 IP 位址

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

下列範例步驟會預期已經建立簡單的環境。 如果您想要執行本檔中所顯示的步驟，請先[建立虛擬網路](quick-create-portal.md#create-a-virtual-network)。 不過，在步驟3中，請改為使用這些值：

| 設定 | 值 |
| ------- | ----- |
| 名稱 | *TestVNet* |
| 位址空間 | *192.168.0.0/16* |
| 資源群組 | **TestRG** （如有必要，請選取 [**建立新**的] 加以建立） |
| 子網路 - 名稱 | *前端* |
| 子網路 - 位址範圍 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>建立用來測試靜態私人 IP 位址的 VM
當您以 Resource Manager 部署模式建立 VM 時，您無法使用 Azure 入口網站設定靜態私人 IP 位址。 相反地，您會先建立 VM。 然後您可以將其私人 IP 設定為靜態。

若要在名為*TestVNet*的虛擬網路的*前端*子網中建立名為*DNS01*的 VM，請遵循下列步驟：

1. 從 [ [Azure 入口網站](https://portal.azure.com)] 功能表中，選取 [**建立資源**]。

    ![建立資源，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. 選取 [**計算** > ] [**虛擬機器**]。

    ![建立 VM，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. 在 [**基本**] 中，指定專案的值，如下表所述。 然後選取 **[&nbsp;下&nbsp;一步：磁片]** ，然後選取 **[下一步：&nbsp;&nbsp;網路]**

    | Item | 值 |
    | --- | --- |
    | **訂用帳戶** | 您目前的訂用帳戶 |
    | **資源群組** | **TestRG** （從下拉式清單中選取） |
    | **虛擬機器名稱** | *DNS01* |
    | **區域** | (美國) 美國東部  |
    | **影像** | **Windows Server 2019 Datacenter** |
    | **大小** | **B1ls**的**VM 大小**，**提供****標準** |
    | **使用者名稱** | 您的系統管理員帳戶的使用者名稱 |
    | **密碼** | 系統管理員帳戶之使用者名稱的密碼 |
    | **確認密碼** | 再次輸入密碼 |

    ![[基本] 索引標籤，建立虛擬機器，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. 在 [**網路**功能] 中，指定專案的值（如下表所述），然後選取 **[下一步]**。

    | Item | 值 |
    | --- | --- |
    | **虛擬網路** | **TestVNet** |
    | **子網路** | **前端** |

    ![[網路功能] 索引標籤，建立虛擬機器，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. 在 [**管理**] 中的 [**診斷儲存體帳戶**] 底下，選擇 [ **vnetstorage**]。 如果該儲存體帳戶未出現在清單中，請**選取 [新建]**，並指定*vnetstorage*的**名稱**，然後選取 **[確定]**。 最後，選取 **[&nbsp;+&nbsp;審查**] [建立]。

    ![[管理] 索引標籤，建立虛擬機器，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. 在 [**審查 + 建立**] 中，查看總覽資訊，然後選取 [**建立**]。

    ![查看 + 建立索引標籤，建立虛擬機器，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

建立 VM 之後，就會出現下列訊息。

![部署完成訊息，建立虛擬機器，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>取得 VM 的私人 IP 位址資訊
若要查看新 VM 的私人 IP 位址資訊：

1. 移至 [ [Azure 入口網站](https://portal.azure.com)] 以尋找您的 VM。 搜尋並選取 [虛擬機器]  。

    ![虛擬機器，搜尋方塊，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 選取新 VM 的名稱（**DNS01**）。

    ![虛擬機器清單，Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. 選擇 [**網路**]，然後選取列出的唯一網路介面。

    ![網路介面、網路、虛擬機器、Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. 選擇 [ **ip**設定]，然後選取表格中所列的 ip 設定。

    ![IP 設定、網路介面、網路、虛擬機器、Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. 在 [**私人 IP 位址設定**] 的 [ **TestVNet/前端**虛擬網路/子網] 底下，記下**指派**值（[**動態**] 或 [**靜態**]）和 [ **IP 位址**]。

    ![動態或靜態指派、舊的私人 IP 位址設定、IP 配置、網路介面、網路、虛擬機器、Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至新的 VM：

1. 在 [IP 設定] 頁面中，將私人 IP 位址的指派設定為 [**靜態**]。
2. 將您的私人**IP 位址**變更為*192.168.1.101*，然後選取 [**儲存**]。
   
    ![動態或靜態指派、新的私人 IP 位址設定、IP 配置、網路介面、網路、虛擬機器、Azure 入口網站](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> 如果您在選取 [**儲存**] 之後發現指派仍然設定為 [**動態**]，表示您輸入的 IP 位址已在使用中。 請嘗試另一個 IP 位址。

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>移除 VM 的靜態私人 IP 位址
若要從您的 VM 移除靜態私人 IP 位址：

在 [IP 設定] 頁面中，將私人 IP 位址的 [指派] 設定為 [**動態**]，然後選取 [**儲存**]。

## <a name="set-ip-addresses-within-the-operating-system"></a>設定作業系統內的 IP 位址

從 VM 的作業系統內，您不應該以靜態方式指派指派給 Azure VM 的*私人*IP。 只在必要時執行私人 IP 的靜態指派，例如[將許多 IP 位址指派給 vm](virtual-network-multiple-ip-addresses-portal.md)時。 如果您手動設定作業系統內的私人 IP 位址，請確定它符合指派給 Azure[網路介面](virtual-network-network-interface-addresses.md#change-ip-address-settings)的私人 ip 位址。 否則，您可能會失去與 VM 的連線能力。 深入了解[私人 IP 位址](virtual-network-network-interface-addresses.md#private)設定。

此外，您絕對不要手動指派在虛擬機器作業系統內指派給 Azure 虛擬機器的*公用*IP 位址。

## <a name="next-steps"></a>後續步驟

了解如何管理 [IP 位址設定](virtual-network-network-interface-addresses.md)。
