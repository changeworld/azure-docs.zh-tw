---
title: 配置可用性組（Azure 快速入門範本）
description: 使用 Azure 快速啟動範本創建 Windows 容錯移轉叢集、將 SQL Server VM 加入群集、創建攔截器以及配置 Azure 中的內部負載等化器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: edf810dfc975eebaf261eac7b89106c9e29c759c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022389"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure 快速入門範本在 Azure VM 上配置 SQL Server 的可用性組
本文介紹如何使用 Azure 快速入門範本在 Azure 中為 SQL Server 虛擬機器部分自動化 SQL Server 虛擬機器的可用性組配置的部署。 此過程使用了兩個 Azure 快速入門範本： 

   | [範本] | 描述 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | 創建 Windows 容錯移轉叢集，並將 SQL Server VM 加入到該群集中。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 創建可用性組攔截器並配置內部負載等化器。 僅當使用**101 sql-vm-ag 設置**範本創建 Windows 容錯移轉叢集時，才能使用此範本。 |
   | &nbsp; | &nbsp; |

可用性組配置的其他部分必須手動完成，例如創建可用性組和創建內部負載等化器。 本文提供自動和手動步驟的順序。
 

## <a name="prerequisites"></a>Prerequisites 
要使用快速入門範本自動設置"始終打開"可用性組，您必須具備以下先決條件： 
- [Azure 訂閱](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- 運行 SQL Server [2016（或更高版本）的 Azure 中](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)一個或多個域聯接的 VM 位於相同的可用性集或可用性區域中，並已[註冊到 SQL VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)。  
- 兩個可用（任何實體不使用）IP 位址：一個用於內部負載等化器，另一個用於可用性組同一子網中的可用性組攔截器。 如果使用現有負載等化器，則只需要一個可用的 IP 位址。  

## <a name="permissions"></a>權限
使用 Azure 快速入門範本配置"始終打開的可用性"組需要以下許可權： 

- 在域中具有 **"創建電腦物件"** 許可權的現有域使用者帳戶。  例如，域管理員帳戶通常具有足夠的許可權（例如： account@domain.com _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 服務的域使用者帳戶。 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>第 1 步：使用快速入門範本創建容錯移轉叢集並將 SQL Server VM 加入群集 
在 SQL Server VM 資來源提供者註冊 SQL Server VM 後，可以將 SQL Server VM 加入到*SqlVirtualMachine 組*。 此資源定義 Windows 容錯移轉叢集的中繼資料。 中繼資料包括版本、版本、完全限定的功能變數名稱、用於管理群集和 SQL Server 服務的 Active Directory 帳戶以及作為雲見證的存儲帳戶。 

將 SQL Server VM 新增至 SqlVirtualMachineGroup** 資源群組，會啟動 Windows 容錯移轉叢集服務建立叢集，然後將 SQL Server VM 加入該叢集。 此步驟通過**101 sql-vm-ag 設置**快速入門範本實現自動化。 您可以使用以下步驟實現它：

1. 轉到[**101 sql-vm-ag 設置**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup)快速入門範本。 然後，選擇 **"部署到 Azure"** 以在 Azure 門戶中打開快速入門範本。
1. 填寫所需的欄位以配置 Windows 容錯移轉叢集的中繼資料。 您可以將可選欄位留空。

   下表顯示了範本的必要值： 

   | **領域** | 值 |
   | --- | --- |
   | **訂閱** |  您的 SQL Server VM 所在的訂用帳戶。 |
   |**資源組** | 您的 SQL Server VM 所屬的資源群組。 | 
   |**容錯移轉叢集名稱** | 新 Windows 容錯移轉叢集所需的名稱。 |
   | **現有 VM 清單** | 要參與可用性組的 SQL Server VM，並將成為此新群集的一部分。 用逗號和空格分隔這些值（例如 *：SQLVM1、SQLVM2*）。 |
   | **SQL Server 版本** | SQL Server VM 的 SQL Server 版本。 從下拉清單中選擇它。 目前，僅支援 SQL Server 2016 和 SQL Server 2017 映射。 |
   | **現有完整網域名稱** | SQL Server VM 所在網域的現有 FQDN。 |
   | **現有網域帳戶** | 在範本部署期間創建[CNO](/windows-server/failover-clustering/prestage-cluster-adds)時，在域中具有 **"創建電腦物件"** 許可權的現有域使用者帳戶。 例如，域管理員帳戶通常具有足夠的許可權（例如： account@domain.com *此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。*| 
   | **網域帳戶密碼** | 前述網域使用者帳戶的密碼。 | 
   | **現有 SQL 服務帳戶** | 在可用性組部署期間控制[SQL Server 服務的](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)域使用者帳戶（例如： account@domain.com。 |
   | **SQL 服務密碼** | 控制 SQL Server 服務的網域使用者帳戶所使用的密碼。 |
   | **雲端見證名稱** | 將創建和用於雲見證的新 Azure 存儲帳戶。 您可以修改此名稱。 |
   | **\_工件位置** | 此欄位為預設設定，不應該修改。 |
   | **\_工件位置 Sas 權杖** | 此欄位有意留空。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選擇 **"我同意上述條款及條件"** 核取方塊。 然後選擇 **"購買"** 以完成快速入門範本的部署。 
1. 要監視部署，請從頂部導航橫幅中的 **"通知**"鈴圖示中選擇部署，或者轉到 Azure 門戶中的**資源組**。 選擇 **"設置"** 下的 **"部署**"，然後選擇**Microsoft.範本**部署。 

>[!NOTE]
> 範本部署期間提供的憑據僅存儲部署長度。 部署完成後，這些密碼將被刪除。 如果向群集添加更多 SQL Server VM，系統將要求您再次提供它們。 


## <a name="step-2-manually-create-the-availability-group"></a>第 2 步：手動創建可用性組 
通過使用[SQL 伺服器管理工作室](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)[、PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[Transact-SQL，](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)按照通常方式手動創建可用性組。 

>[!IMPORTANT]
> 此時*不要*創建攔截器，因為**101-sql-vm-aglistener 設置**快速入門範本在步驟 4 中會自動執行此操作。 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>第 3 步：手動創建內部負載等化器
始終打開可用性組攔截器需要 Azure 負載等化器的內部實例。 內部負載等化器為可用性組攔截器提供"浮動"IP 位址，以便更快地進行容錯移轉和重新連接。 如果可用性組中的 SQL Server VM 屬於同一可用性集的一部分，則可以使用基本負載等化器。 否則，您需要使用標準負載等化器。 

> [!IMPORTANT]
> 內部負載等化器應與 SQL Server VM 實例位於同一虛擬網路中。 

您只需創建內部負載等化器。 在步驟 4 中 **，101-sql-vm-aglistener 設置**快速啟動範本處理配置的其餘部分（如後端池、運行狀況探測和負載平衡規則）。 

1. 在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。 
2. 在資源組中，選擇 **"添加**"。
3. 搜尋 **負載平衡器**。 在搜尋結果中，選擇**由 Microsoft**發佈的**負載等化器**。
4. 在 **"負載等化器"** 邊欄選項卡上，選擇 **"創建**"。
5. 在 [建立負載平衡器]**** 對話方塊中，依下列方式設定負載平衡器︰

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |輸入表示負載等化器的文本名稱。 例如，輸入**sqlLB**。 |
   | **類型** |**內部**︰大部分的實作都會使用內部負載平衡器，可讓相同虛擬網路內的應用程式連線到可用性群組。  </br> **外部**：允許應用程式通過公共互聯網連接連接到可用性組。 |
   | **虛擬網路** | 選取 SQL Server 執行個體所在的虛擬網路。 |
   | **子** | 選取 SQL Server 執行個體所在的子網路。 |
   | **IP 位址指派** |**靜態** |
   | **私人 IP 位址** | 從子網路指定可用的 IP 位址。 |
   | **訂閱** |如果您有多個訂用帳戶，此欄位才會出現。 選取您想要與此資源相關聯的訂用帳戶。 它通常與可用性組的所有資源相同的訂閱。 |
   | **資源組** |選取 SQL Server 執行個體所在的資源群組。 |
   | **位置** |選取 SQL Server 執行個體所在的 Azure 位置。 |
   | &nbsp; | &nbsp; |

6. 選取 [建立]****。 


>[!IMPORTANT]
> 每個 SQL Server VM 的公共 IP 資源應具有與標準負載等化器相容的標準 SKU。 要確定 VM 公共 IP 資源的 SKU，請轉到**資源組**，為 SQL Server VM 選擇**公共 IP 位址**資源，並在 **"概述"** 窗格中查找**SKU**下的值。 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>步驟 4：使用快速入門範本創建可用性組攔截器並配置內部負載等化器

使用**101-sql-vm-aglistener 設置**快速啟動範本創建可用性組攔截器並自動設定內部負載等化器。 該範本提供 Microsoft.SqlVirtualMachine/Sql虛擬電腦群組/可用性組攔截器資源。 **101-sql-vm-aglistener-setup** 快速入門範本會透過 SQL VM 資源提供者執行下列動作：

- 為接聽程式建立新的前端 IP 資源 (根據在部署期間提供的 IP 位址值)。 
- 配置群集和內部負載等化器的網路設置。 
- 為內部負載平衡器、運行狀況探測和負載平衡規則配置後端池。
- 使用給定的 IP 位址和名稱創建可用性組攔截器。
 
>[!NOTE]
> 僅當使用 101 sql-vm-ag 設置範本創建 Windows 容錯移轉叢集時，才能使用**101-sql-vm-ag-listener**設置。 **101-sql-vm-ag-setup**
   
   
要配置內部負載等化器並創建可用性組攔截器，請執行以下操作：
1. 轉到[101 sql-vm-aglistener 設置](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup)快速入門範本，然後選擇 **"部署到 Azure"** 以在 Azure 門戶中啟動快速入門範本。
1. 填寫配置內部負載等化器所需的欄位，並創建可用性組攔截器。 您可以將可選欄位留空。 

   下表顯示了範本的必要值： 

   | **領域** | 值 |
   | --- | --- |
   |**資源組** | 您的 SQL Server VM 和可用性群組所屬的資源群組。 | 
   |**現有容錯移轉叢集名稱** | 您的 SQL Server VM 加入的叢集名稱。 |
   | **現有 SQL 可用性群組**| SQL Server VM 所屬可用性群組的名稱。 |
   | **現有 VM 清單** | 屬於前述可用性群組的 SQL Server VM 名稱。 用逗號和空格分隔名稱（例如 *：SQLVM1、SQLVM2*）。 |
   | **聽眾** | 要分配給攔截器的 DNS 名稱。 預設情況下，此範本指定名稱"aglistener"，但您可以更改它。 名稱長度不應該超過 15 個字元。 |
   | **接聽程式通訊埠** | 您希望攔截器使用的埠。 通常，此埠應預設為 1433。 這是範本指定的埠號。 但是，如果預設埠已更改，攔截器埠應改用該值。 | 
   | **接聽程式 IP** | 您希望攔截器使用的 IP 位址。 此位址將在範本部署期間創建，因此提供尚未使用的位址。  |
   | **現有子網路** | SQL Server VM 的內部子網的名稱（例如：*預設值*）。 您可以通過訪問**資源組**、選擇虛擬網路、在 **"設置"** 窗格中選擇**子網**以及複製**名稱**下的值來確定此值。 |
   | **現有內部負載平衡器** | 您在步驟 3 中創建的內部負載等化器的名稱。 |
   | **探頭埠** | 您希望內部負載等化器使用的探頭埠。 預設情況下，範本使用 59999，但您可以更改此值。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選擇 **"我同意上述條款及條件"** 核取方塊。 選擇 **"購買"** 以完成快速入門範本的部署。 
1. 要監視部署，請從頂部導航橫幅中的 **"通知**"鈴圖示中選擇部署，或者轉到 Azure 門戶中的**資源組**。 選擇 **"設置"** 下的 **"部署**"，然後選擇**Microsoft.範本**部署。 

>[!NOTE]
>如果部署中途失敗，則需要在重新部署**101 sql-vm-aglistener 設置**快速入門範本之前，使用 PowerShell 手動[刪除新創建的攔截器](#remove-the-availability-group-listener)。 

## <a name="remove-the-availability-group-listener"></a>刪除可用性組攔截器
如果以後需要刪除範本配置的可用性組攔截器，則必須通過 SQL VM 資來源提供者。 由於攔截器是通過 SQL VM 資來源提供者註冊的，因此只需通過 SQL 伺服器管理工作室刪除攔截器是不夠的。 

最好的方法是通過使用 PowerShell 中的以下程式碼片段通過 SQL VM 資來源提供者將其刪除。 這樣做會從 SQL VM 資來源提供者中刪除可用性組攔截器中繼資料。 它還從可用性組物理上刪除攔截器。 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>常見錯誤
本節將討論一些已知問題和可能的解決方法。 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>可用性群組 '\<AG-Name>' 已有可用性群組接聽程式
在 Azure 快速入門範本中使用的可用性組攔截器中所使用的選定可用性組已包含攔截器。 它實際上在可用性組中，或者其中繼資料保留在 SQL VM 資來源提供者中。 在重新部署**101 sql-vm-aglistener 設置**快速啟動範本之前，請使用[PowerShell](#remove-the-availability-group-listener)刪除攔截器。 

### <a name="connection-only-works-from-primary-replica"></a>只能從主要複本進行連線
此行為可能來自失敗的**101-sql-vm-aglistener 設置**範本部署，該部署使內部負載等化器的配置處於不一致狀態。 請確認後端集區有列出可用性設定組，且健康情況探查和負載平衡都有其規則。 如果缺少任何內容，則內部負載等化器的配置不一致。 

要解決此問題，請使用[PowerShell](#remove-the-availability-group-listener)刪除攔截器，通過 Azure 門戶刪除內部負載等化器，然後從步驟 3 重新開始。 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - 只能更新 SQL 虛擬機器清單
如果攔截器是通過 SQL 伺服器管理工作室 （SSMS） 刪除的，但未從 SQL VM 資來源提供者中刪除，則部署**101-sql-vm-aglistener 設置**範本時可能會出現此錯誤。 通過 SSMS 刪除攔截器不會從 SQL VM 資來源提供者中刪除攔截器的中繼資料。 攔截器必須通過[PowerShell](#remove-the-availability-group-listener)從資來源提供者中刪除。 

### <a name="domain-account-does-not-exist"></a>網域帳戶不存在
此錯誤可能有兩個原因。 指定的域帳戶不存在，或者缺少[使用者主體名稱 （UPN）](/windows/desktop/ad/naming-properties#userprincipalname)資料。 **101-sql-vm-ag 設置**範本期望 UPN 表單中的域帳戶（即*user@domain.com*），但某些域帳戶可能缺少它。 當本地使用者在伺服器升級到網域控制站時，或通過 PowerShell 創建使用者時，通常會遷移為第一個域管理員帳戶時，就會發生這種情況。 

驗證該帳戶是否存在。 如果是，則可能遇到第二種情況。 要解決此問題，可以執行以下操作：

1. 在網域控制站上，從 [伺服器管理員]**** 的 [工具]**** 選項中開啟 [Active Directory 使用者和電腦]**** 視窗。 
2. 通過選擇左側窗格中的 **"使用者"** 轉到帳戶。
3. 按右鍵帳戶，然後選擇 **"屬性**"。
4. 選擇"**帳戶**"選項卡。如果 **"使用者登錄名稱**"框為空，則這是錯誤的原因。 

    ![空白的使用者帳戶表示缺少 UPN](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. 填寫 **"使用者登錄名稱**"框以匹配使用者的名稱，並從下拉清單中選擇正確的域。 
6. 選取 [套用]**** 儲存變更，並選取 [確定]**** 關閉對話方塊。 

進行這些更改後，請嘗試再次部署 Azure 快速入門範本。 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL 伺服器 VM 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL 伺服器 VM 的常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL 伺服器 VM 的定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL 伺服器 VM 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切換 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)



