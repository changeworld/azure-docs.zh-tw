---
title: 為 VM 設定專用 IP 位址 - Azure 門戶
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461543"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>使用 Azure 閘戶為 VM 設定專用 IP 位址

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下示例步驟期望已創建一個簡單的環境。 如果要在這個文件中顯示的步驟中執行這些步驟,請先[建立虛擬網路](quick-create-portal.md#create-a-virtual-network)。 但是,在步驟 3 中,改用這些值:

| 設定 | 值 |
| ------- | ----- |
| 名稱 | *測試VNet* |
| 位址空間 | *192.168.0.0/16* |
| 資源群組 | **TestRG(** 如有必要,選擇 **"建立新**" 以建立它 ) |
| 子網路 - 名稱 | *前端* |
| 子網路 - 位址範圍 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>建立測試靜態專用 IP 位址的 VM
在資源管理員部署模式下創建 VM 時,無法使用 Azure 門戶設置靜態專用 IP 位址。 相反,您首先創建 VM。 然後,您可以將其專用 IP 設定為靜態。

要在名為*TestVNet*的虛擬網路*的 FrontEnd*子網中建立名為*DNS01*的 VM,請按照以下步驟操作:

1. 在[Azure 門戶](https://portal.azure.com)功能表中,選擇 **「創建資源**」。

    ![建立資源 Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. 選擇**計算** > **虛擬機器**。

    ![建立 VM、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. 在 **「基礎知識」** 中,指定下表中所述的項的值。 然後選擇**下&nbsp;&nbsp;一步:磁碟"** 然後**選擇「下一個&nbsp;&nbsp;: 網路**」 。

    | Item | 值 |
    | --- | --- |
    | **訂用帳戶** | 您目前訂閱 |
    | **資源群組** | **TestRG(** 從下拉清單中選擇) |
    | **虛擬機器名稱** | *DNS01* |
    | **區域** | (美國) 美國東部  |
    | **影像** | **Windows Server 2019 Datacenter** |
    | **大小** | **B1ls**的**VM 大小**,**Offering****提供標準** |
    | **使用者名稱** | 管理員帳戶的使用者名稱 |
    | **密碼** | 管理員帳戶使用者名稱的密碼 |
    | **確認密碼** | 再次使用密碼 |

    ![基礎知識選項卡,創建虛擬機器,Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. 在 **「網路」** 中,指定下表中所述項的值,然後選擇 **「下一步**」。。

    | Item | 值 |
    | --- | --- |
    | **虛擬網路** | **測試VNet** |
    | **子網路** | **前端** |

    ![網路選項卡,創建虛擬機器,Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. 在 **'管理**' 的管理員下,**在診斷儲存帳戶**下,選擇**vnetstorage**。 如果該儲存帳戶未顯示在清單中,請選擇「**創建新**」,指定*vnetstorage***的名稱**,然後選擇 **「確定**」。 最後,選擇**&nbsp;+&nbsp;「審閱創建**」。。

    ![管理選項卡,建立虛擬機器,Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. 在 **「審閱 」 建立**中,查看概覽資訊,然後選擇 **「創建**」。。

    ![檢視 + 建立選項卡、建立虛擬機器、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

創建 VM 後將顯示以下消息。

![部署完成訊息,建立虛擬機器,Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>以 VM 的專用 IP 位址資訊
要檢視新 VM 的專用 IP 位址資訊,請處理:

1. 轉到[Azure 門戶](https://portal.azure.com)以查找 VM。 搜尋並選取 [虛擬機器]  。

    ![虛擬機器、搜尋框、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 選擇新 VM 的名稱 (**DNS01**)。

    ![虛擬機器清單,Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. 選擇 **"網路**",然後選擇列出的唯一網路介面。

    ![網路介面、網路、虛擬機器、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. 選擇**IP 設定**,然後選擇表中列出的 IP 配置。

    ![IP 設定、網路介面、網路、虛擬機器、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. 在**專用 IP 位址設定**中,在**TestVNet/FrontEnd**虛擬網路/子網下,請注意**配置**值(**動態**或**靜態**)和**IP 位址**。

    ![動態或靜態配置、舊的專用 IP 位址設定、IP 設定、網路介面、網路、虛擬機器、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>將靜態私人 IP 位址新增至現有的 VM
要向新 VM 新增靜態專用 IP 位址,請處理:

1. 在 IP 設定頁中,將專用 IP 位址的設定設定為**靜態**。
2. 將專用**IP 位址**更改為*192.168.1.101*,然後選擇 **"儲存**"。
   
    ![動態或靜態配置、新的專用 IP 位址設定、IP 設定、網路介面、網路、虛擬機器、Azure 門戶](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> 如果在選擇 **「保存」** 後注意到分配仍設置為 **「動態**」,則鍵入的 IP 位址已在使用中。 請嘗試另一個 IP 位址。

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>移除 VM 的靜態私人 IP 位址
要從 VM 中移除靜態專用 IP 位址,請:

在 IP 設定頁中,將專用 IP 位址的分配設置為**動態**,然後選擇 **「儲存**」 。。

## <a name="set-ip-addresses-within-the-operating-system"></a>設定作業系統內的 IP 位址

在 VM 的作業系統中,不應靜態地分配分配給 Azure VM 的*專用*IP。 僅在必要時(如[將許多 IP 位址分配給 VM](virtual-network-multiple-ip-addresses-portal.md)時)進行私有 IP 的靜態分配。 如果在作業系統中手動設置專用 IP 位址,請確保它與分配給 Azure[網路介面](virtual-network-network-interface-addresses.md#change-ip-address-settings)的專用 IP 位址匹配。 否則,您可能會失去與 VM 的連接。 深入了解[私人 IP 位址](virtual-network-network-interface-addresses.md#private)設定。

此外,絕不應手動分配分配給虛擬機內 Azure 虛擬機器*的公共*IP 位址。

## <a name="next-steps"></a>後續步驟

了解如何管理 [IP 位址設定](virtual-network-network-interface-addresses.md)。
