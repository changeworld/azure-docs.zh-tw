---
title: 快速入門:使用專用 IP 位址連接到虛擬機器:Azure 堡壘
description: 在本文中,瞭解如何從虛擬機創建 Azure Bastion 主機,以及如何使用專用 IP 位址安全地連接。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619258"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入門:使用專用 IP 位址和 Azure 堡壘連接到虛擬機器

此快速入門文章介紹如何使用專用 IP 位址連接到虛擬機器。 通過 Bastion 進行連接時,虛擬機器不需要公共 IP 位址。 本文中的步驟可説明您通過門戶中的虛擬機將堡壘部署到虛擬網路。 預配服務後,RDP/SSH 體驗將提供給同一虛擬網路中的所有虛擬機。

## <a name="prerequisites"></a><a name="prereq"></a>Prerequisites

* Azure 虛擬網路。
* 位於打開埠 3389 的虛擬網路中的 Azure 虛擬機器。

### <a name="example-values"></a>範例值

|**名稱** | **ReplTest1** |
| --- | --- |
| 名稱 |  VNet1 堡壘 |
| 區域 | eastus |
| 虛擬網路 |  VNet1 |
| • 子網名稱 | Azure 堡壘子網 |
| AzureBastion Subnet 位址 |  10.1.254.0/27 |
| 公用 IP 位址 |  新建 |
| 公用 IP 位址名稱 | VNet1BastionPIP  |
| 公共 IP 位址 SKU |  標準  |
| 指派  | 靜態 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>建立 Bastion 主機

當您使用現有虛擬機器在門戶中創建堡壘主機時,各種設置將自動預設以對應於虛擬機器和/或虛擬網路。

1. 開啟[Azure 門戶](https://portal.azure.com)。 轉到虛擬機,然後單擊"**連接**"。

   ![虛擬機器設定](./media/quickstart-host-portal/vm-settings.png)
1. 從下拉清單中,選擇 **「堡壘**」。。
1. 在"連接"頁上,選擇 **"使用堡壘**"。

   ![選擇堡壘](./media/quickstart-host-portal/select-bastion.png)

1. 在「堡壘」頁上,填寫以下設置欄位:

   * **名稱**: 命名堡壘主機
   * **子網**:將部署堡壘資源的虛擬網路中的子網。 子網必須使用**名稱 AzureBastion Subnet 建立**。 該名稱使 Azure 知道要將堡壘資源部署到哪個子網。 這與閘道網不同。 使用至少 /27 或更大的子網(/27、/26、/25 等)。
   
      * 選擇 **"管理子網設定**",然後選擇 **「子網**」。
      * 在「新增子網」頁上,鍵入**AzureBastion 子網**。
      * 在 CIDR 表示法中指定位址範圍。 例如,10.1.254.0/27。
      * 選擇 **"確定"** 以建立子網。 在頁面頂部,導航回堡壘以完成其餘設置。

         ![導覽到堡壘設定](./media/quickstart-host-portal/navigate-bastion.png)
   * **公共 IP 位址**:這是將存取 RDP/SSH 的堡壘資源的公共 IP(透過連接埠 443)。 創建新的公共 IP 或使用現有 IP。 公共 IP 位址必須與您正在創建的堡壘資源位於同一區域。
   * **公共 IP 位址名稱**:公共 IP 位址資源的名稱。
1. 在驗證螢幕上,按一下「**創建**」。。 等待大約 5 分鐘,以便創建和部署堡壘資源。

   ![建立堡壘主機](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>連線

將堡壘部署到虛擬網路後,螢幕將更改為連接頁。

1. 鍵入虛擬機的使用者名稱和密碼。 然後,選擇 **"連接**"。

   ![連線](./media/quickstart-host-portal/connect.png)
1. 通過 Bastion 連接到此虛擬機器的 RDP 連接將直接在 Azure 門戶(透過 HTML5)中使用埠 443 和堡壘服務打開。

   ![RDP 連線](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>清除資源

使用虛擬網路與虛擬機器完成後,請刪除資源群組及其包含的所有資源:

1. 在門戶頂部的 **「搜索**」框中輸入*TestRG1,* 然後從搜尋結果中選擇**TestRG1。**

2. 選取 [刪除資源群組]****。

3. 輸入*TESTRG1*以**鍵入資源群組名稱**,然後選擇 **「刪除**」。

## <a name="next-steps"></a>後續步驟

在此快速入門中,您為虛擬網路創建了一個堡壘主機,然後通過堡壘主機安全地連接到虛擬機器。

* 要瞭解有關 Azure 堡壘的詳細資訊,請閱讀[堡壘概述](bastion-overview.md)和[堡壘常見問題 。](bastion-faq.md)
* 要將網路安全組與 Azure 堡壘子網一起使用,請參閱[使用 NSG。](bastion-nsg.md)
* 有關包含 Azure 堡壘主機設定說明的說明,請參閱[教程](bastion-create-host-portal.md)。
* 要連接到虛擬機器縮放集,請參考[Azure Bastion 連接到虛擬機器規模集](bastion-connect-vm-scale-set.md)。