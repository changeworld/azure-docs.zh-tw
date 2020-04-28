---
title: 設定可用性群組（Azure 快速入門範本）
description: 使用 Azure 快速入門範本來建立 Windows 容錯移轉叢集、將 SQL Server Vm 加入叢集、建立接聽程式，以及在 Azure 中設定內部負載平衡器。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74022389"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-an-azure-vm"></a>使用 Azure 快速入門範本，在 Azure VM 上設定 SQL Server 的可用性群組
本文說明如何使用 Azure 快速入門範本，部分自動化部署 Azure 中 SQL Server 虛擬機器的 Always On 可用性群組設定。 此程式會使用兩個 Azure 快速入門範本： 

   | [範本] | 描述 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | 建立 Windows 容錯移轉叢集，並將 SQL Server 的 Vm 加入其中。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 建立可用性群組接聽程式，並設定內部負載平衡器。 只有在使用**101-sql-vm-ag-設定**範本建立 Windows 容錯移轉叢集時，才能使用此範本。 |
   | &nbsp; | &nbsp; |

可用性群組設定的其他部分必須手動完成，例如，建立可用性群組並建立內部負載平衡器。 本文提供自動和手動步驟的順序。
 

## <a name="prerequisites"></a>Prerequisites 
若要使用快速入門範本自動設定 Always On 可用性群組，您必須具備下列必要條件： 
- [Azure 訂](https://azure.microsoft.com/free/)用帳戶。
- 具有網域控制站的資源群組。 
- Azure 中一或多個已加入網域的 Vm，其執行的是位於相同可用性設定組或可用性區域且已[向 SQL VM 資源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md)的[SQL Server 2016 （或更新版本） Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。  
- 有兩個可用（未由任何實體使用） IP 位址：一個用於內部負載平衡器，另一個用於與可用性群組位於相同子網內的可用性群組接聽程式。 如果正在使用現有的負載平衡器，您只需要一個可用的 IP 位址。  

## <a name="permissions"></a>權限
使用 Azure 快速入門範本設定 Always On 可用性群組時，必須具備下列許可權： 

- 在網域中具有「**建立電腦物件**」許可權的現有網域使用者帳戶。  例如，網域系統管理員帳戶通常具有足夠的許可權（例如： account@domain.com）。 _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 服務的網域使用者帳戶。 


## <a name="step-1-create-the-failover-cluster-and-join-sql-server-vms-to-the-cluster-by-using-a-quickstart-template"></a>步驟1：使用快速入門範本建立容錯移轉叢集，並將 SQL Server Vm 加入叢集 
向 SQL VM 資源提供者註冊您的 SQL Server Vm 之後，您可以將 SQL Server Vm 加入*SqlVirtualMachineGroups*。 此資源會定義 Windows 容錯移轉叢集的中繼資料。 中繼資料包括版本、版本、完整功能變數名稱、Active Directory 帳戶來管理叢集和 SQL Server 服務，以及做為雲端見證的儲存體帳戶。 

將 SQL Server VM 新增至 SqlVirtualMachineGroup** 資源群組，會啟動 Windows 容錯移轉叢集服務建立叢集，然後將 SQL Server VM 加入該叢集。 此步驟會使用**101-sql-vm-ag-設定**快速入門範本進行自動化。 您可以使用下列步驟來執行它：

1. 前往 [ [**101-sql-vm-ag-設定**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup)快速入門] 範本。 然後，選取 [**部署至 Azure** ] 以在 Azure 入口網站中開啟快速入門範本。
1. 填寫必要欄位，以設定 Windows 容錯移轉叢集的中繼資料。 您可以將 [選擇性] 欄位保留空白。

   下表顯示範本所需的值： 

   | **欄位** | 值 |
   | --- | --- |
   | **訂用帳戶** |  您的 SQL Server VM 所在的訂用帳戶。 |
   |**資源群組** | 您的 SQL Server VM 所屬的資源群組。 | 
   |**容錯移轉叢集名稱** | 您要用於新的 Windows 容錯移轉叢集的名稱。 |
   | **現有 VM 清單** | 您想要參與可用性群組並屬於此新叢集的 SQL Server Vm。 請以逗號和空格分隔這些值（例如： *SQLVM1、SQLVM2*）。 |
   | **SQL Server 版本** | SQL Server Vm 的 SQL Server 版本。 從下拉式清單中選取它。 目前僅支援 SQL Server 2016 和 SQL Server 2017 映射。 |
   | **現有完整網域名稱** | SQL Server VM 所在網域的現有 FQDN。 |
   | **現有網域帳戶** | 在範本部署期間建立[CNO](/windows-server/failover-clustering/prestage-cluster-adds)網域中具有「**建立電腦物件**」許可權的現有網域使用者帳戶。 例如，網域系統管理員帳戶通常具有足夠的許可權（例如： account@domain.com）。 *此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。*| 
   | **網域帳戶密碼** | 前述網域使用者帳戶的密碼。 | 
   | **現有 SQL 服務帳戶** | 在可用性群組部署期間控制[SQL Server 服務](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)的網域使用者帳戶（例如： account@domain.com）。 |
   | **SQL 服務密碼** | 控制 SQL Server 服務的網域使用者帳戶所使用的密碼。 |
   | **雲端見證名稱** | 將建立並用於雲端見證的新 Azure 儲存體帳戶。 您可以修改這個名稱。 |
   | **\_成品位置** | 此欄位為預設設定，不應該修改。 |
   | **\_構件位置 Sas 權杖** | 此欄位刻意保留空白。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選取 [**我同意上方所述的條款及條件**] 核取方塊。 然後選取 [**購買**] 以完成快速入門範本部署。 
1. 若要監視您的部署，請從頂端流覽橫幅中的 [**通知**] 鐘圖示選取部署，或移至 Azure 入口網站中的 [**資源群組**]。 選取 [**設定**] 底下的 [**部署**]，然後選擇 [ **Microsoft 範本**] 部署。 

>[!NOTE]
> 在範本部署期間提供的認證只會儲存在部署的長度。 部署完成之後，就會移除這些密碼。 如果您將更多 SQL Server Vm 新增至叢集，系統會要求您再次提供。 


## <a name="step-2-manually-create-the-availability-group"></a>步驟2：手動建立可用性群組 
如往常般手動建立可用性群組，方法是使用[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、 [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)或[transact-sql](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)。 

>[!IMPORTANT]
> 此時*請勿建立接聽*程式，因為**101-sql-vm-aglistener 安裝**快速入門範本會在步驟4中自動執行此動作。 

## <a name="step-3-manually-create-the-internal-load-balancer"></a>步驟3：手動建立內部負載平衡器
Always On 可用性群組接聽程式需要 Azure Load Balancer 的內部實例。 「內部負載平衡器」提供可用性群組接聽程式的「浮動」 IP 位址，可讓您更快速地進行容錯移轉和重新連接。 如果可用性群組中的 SQL Server Vm 屬於相同的可用性設定組，您可以使用基本負載平衡器。 否則，您必須使用標準負載平衡器。 

> [!IMPORTANT]
> 內部負載平衡器應該與 SQL Server VM 實例位於相同的虛擬網路中。 

您只需要建立內部負載平衡器。 在步驟4中， **101-sql-vm-aglistener 設定**快速入門範本會處理其餘設定（例如後端集區、健康情況探查和負載平衡規則）。 

1. 在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。 
2. 在資源群組中，選取 [**新增**]。
3. 搜尋 **負載平衡器**。 在搜尋結果中，選取 [ **Load Balancer**]，這是由**Microsoft**所發佈。
4. 在 [ **Load Balancer** ] 分頁上，選取 [**建立**]。
5. 在 [建立負載平衡器]**** 對話方塊中，依下列方式設定負載平衡器︰

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |輸入代表負載平衡器的文字名稱。 例如，輸入**sqlLB**。 |
   | **類型** |**內部**︰大部分的實作都會使用內部負載平衡器，可讓相同虛擬網路內的應用程式連線到可用性群組。  </br> **外部**：允許應用程式透過公用網際網路連線連接到可用性群組。 |
   | **虛擬網路** | 選取 SQL Server 執行個體所在的虛擬網路。 |
   | **子網路** | 選取 SQL Server 執行個體所在的子網路。 |
   | **IP 位址指派** |**靜態** |
   | **私人 IP 位址** | 從子網路指定可用的 IP 位址。 |
   | **訂用帳戶** |如果您有多個訂用帳戶，此欄位才會出現。 選取您想要與此資源相關聯的訂用帳戶。 通常是與可用性群組的所有資源相同的訂用帳戶。 |
   | **資源群組** |選取 SQL Server 執行個體所在的資源群組。 |
   | **位置** |選取 SQL Server 執行個體所在的 Azure 位置。 |
   | &nbsp; | &nbsp; |

6. 選取 [建立]  。 


>[!IMPORTANT]
> 每個 SQL Server VM 的公用 IP 資源都應該要有標準 SKU，才能與標準負載平衡器相容。 若要判斷 VM 公用 IP 資源的 SKU，請移至 [**資源群組**]，選取 SQL Server VM 的**公用 IP 位址**資源，然後在 [**總覽**] 窗格中的 [ **SKU** ] 下找出值。 

## <a name="step-4-create-the-availability-group-listener-and-configure-the-internal-load-balancer-by-using-the-quickstart-template"></a>步驟4：使用快速入門範本來建立可用性群組接聽程式並設定內部負載平衡器

使用**101-sql-vm-aglistener 安裝**快速入門範本來建立可用性群組接聽程式，並自動設定內部負載平衡器。 範本會布建 Microsoft.sqlvirtualmachine/SqlVirtualMachineGroups/AvailabilityGroupListener 資源。 **101-sql-vm-aglistener-setup** 快速入門範本會透過 SQL VM 資源提供者執行下列動作：

- 為接聽程式建立新的前端 IP 資源 (根據在部署期間提供的 IP 位址值)。 
- 設定叢集和內部負載平衡器的網路設定。 
- 設定內部負載平衡器、健康情況探查和負載平衡規則的後端集區。
- 使用指定的 IP 位址和名稱，建立可用性群組接聽程式。
 
>[!NOTE]
> 只有在使用**101-sql-vm-ag-設定**範本建立 Windows 容錯移轉叢集時，您才能使用**101-sql-vm-aglistener 設定**。
   
   
若要設定內部負載平衡器並建立可用性群組接聽程式，請執行下列動作：
1. 移至 [ [101-sql-vm-aglistener-安裝](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup)快速入門] 範本，然後選取 [**部署至 Azure** ] 以啟動 Azure 入口網站中的快速入門範本。
1. 填寫必要欄位來設定內部負載平衡器，並建立可用性群組接聽程式。 您可以將 [選擇性] 欄位保留空白。 

   下表顯示範本所需的值： 

   | **欄位** | 值 |
   | --- | --- |
   |**資源群組** | 您的 SQL Server VM 和可用性群組所屬的資源群組。 | 
   |**現有容錯移轉叢集名稱** | 您的 SQL Server VM 加入的叢集名稱。 |
   | **現有 SQL 可用性群組**| SQL Server VM 所屬可用性群組的名稱。 |
   | **現有 VM 清單** | 屬於前述可用性群組的 SQL Server VM 名稱。 以逗號和空格分隔名稱（例如： *SQLVM1、SQLVM2*）。 |
   | **接聽程式** | 您想要指派給接聽程式的 DNS 名稱。 根據預設，此範本會指定名稱「aglistener」，但您可以變更它。 名稱長度不應該超過 15 個字元。 |
   | **接聽程式通訊埠** | 您想要接聽程式使用的通訊埠。 通常，此埠應為預設值1433。 這是範本所指定的埠號碼。 但是，如果您的預設埠已變更，接聽程式埠就應該改用該值。 | 
   | **接聽程式 IP** | 您想要接聽程式使用的 IP 位址。 此位址會在範本部署期間建立，因此請提供一個尚未使用的位址。  |
   | **現有子網路** | SQL Server Vm 的內部子網名稱（例如：*預設值*）。 若要判斷此值，您可以前往 [**資源群組**]，選取您的虛擬網路，在 [**設定**] 窗格中選取 [**子網**]，然後複製 [**名稱**] 底下的值。 |
   | **現有內部負載平衡器** | 您在步驟3中建立之內部負載平衡器的名稱。 |
   | **探查埠** | 您想要讓內部負載平衡器使用的探查埠。 根據預設，範本會使用59999，但您可以變更此值。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選取 [**我同意上方所述的條款及條件**] 核取方塊。 選取 [**購買**] 以完成快速入門範本部署。 
1. 若要監視您的部署，請從頂端流覽橫幅中的 [**通知**] 鐘圖示選取部署，或移至 Azure 入口網站中的 [**資源群組**]。 選取 [**設定**] 底下的 [**部署**]，然後選擇 [ **Microsoft 範本**] 部署。 

>[!NOTE]
>如果您的部署失敗一半到，則必須先使用 PowerShell 手動[移除新建立的](#remove-the-availability-group-listener)接聽程式，然後再重新部署**101-sql-vm-aglistener 設定**快速入門範本。 

## <a name="remove-the-availability-group-listener"></a>移除可用性群組接聽程式
如果您稍後需要移除範本所設定的可用性群組接聽程式，您必須經過 SQL VM 資源提供者。 因為接聽程式是透過 SQL VM 資源提供者來註冊，所以只要透過 SQL Server Management Studio 刪除它就不足。 

最好的方法是在 PowerShell 中使用下列程式碼片段，透過 SQL VM 資源提供者來刪除它。 這麼做會從 SQL VM 資源提供者中移除可用性群組接聽程式中繼資料。 它也會實際刪除可用性群組中的接聽程式。 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>常見錯誤
本節將討論一些已知問題和可能的解決方法。 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>可用性群組 '\<AG-Name>' 已有可用性群組接聽程式
用於可用性群組接聽程式的 Azure 快速入門範本中所選取的可用性群組已經包含接聽項。 它實際上是在可用性群組內，或其中繼資料仍保留在 SQL VM 資源提供者內。 請先使用[PowerShell](#remove-the-availability-group-listener)移除接聽程式，再重新部署**101-sql-vm-aglistener 設定**快速入門範本。 

### <a name="connection-only-works-from-primary-replica"></a>只能從主要複本進行連線
此行為可能來自于失敗的**101-aglistener 設定**範本部署，但已將內部負載平衡器的設定保留為不一致的狀態。 請確認後端集區有列出可用性設定組，且健康情況探查和負載平衡都有其規則。 如果遺漏任何專案，內部負載平衡器的設定會是不一致的狀態。 

若要解決此行為，請使用[PowerShell](#remove-the-availability-group-listener)移除接聽程式、透過 Azure 入口網站刪除內部負載平衡器，然後在步驟3重新開機。 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - 只能更新 SQL 虛擬機器清單
如果接聽程式已透過 SQL Server Management Studio （SSMS）刪除，但未從 SQL VM 資源提供者中刪除，則當您部署**101-sql-aglistener 設定**範本時，可能會發生此錯誤。 透過 SSMS 刪除接聽程式並不會從 SQL VM 資源提供者中移除接聽程式的中繼資料。 接聽程式必須透過[PowerShell](#remove-the-availability-group-listener)從資源提供者中刪除。 

### <a name="domain-account-does-not-exist"></a>網域帳戶不存在
此錯誤可能有兩個原因。 指定的網域帳戶不存在，或缺少[使用者主要名稱（UPN）](/windows/desktop/ad/naming-properties#userprincipalname)資料。 **101-sql-vm ag 設定**範本需要 UPN 格式的網域帳戶（也就是*user@domain.com*），但某些網域帳戶可能遺失了它。 這通常發生于將伺服器升級為網域控制站時，或當使用者透過 PowerShell 建立時，本機使用者已遷移為第一個網域系統管理員帳戶。 

確認帳戶存在。 如果有，您可能會遇到第二個情況。 若要解決此問題，請執行下列動作：

1. 在網域控制站上，從 [伺服器管理員]**** 的 [工具]**** 選項中開啟 [Active Directory 使用者和電腦]**** 視窗。 
2. 在左窗格中選取 [**使用者**]，移至帳戶。
3. 以滑鼠右鍵按一下帳戶，然後選取 [**屬性**]。
4. 選取 [**帳戶**] 索引標籤。如果 [**使用者登入名稱**] 方塊是空白的，這就是錯誤的原因。 

    ![空白的使用者帳戶表示缺少 UPN](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. 填寫 [**使用者登入名稱**] 方塊以符合使用者的名稱，然後從下拉式清單中選取適當的網域。 
6. 選取 [套用]**** 儲存變更，並選取 [確定]**** 關閉對話方塊。 

在您進行這些變更之後，請嘗試再部署一次 Azure 快速入門範本。 



## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL Server Vm 的總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Vm 的常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server Vm 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server Vm 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [切換 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)



