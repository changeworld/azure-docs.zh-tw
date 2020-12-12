---
title: 設定可用性群組 (Azure 快速入門範本)
description: 使用 Azure 快速入門範本來建立 Windows 容錯移轉叢集、將 SQL Server VM 加入該叢集、建立接聽程式，以及在 Azure 中設定內部負載平衡器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d7dfe010a3f4a1559454c49545af81eb14797bf1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359909"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>使用 Azure 快速入門範本在 Azure VM 上設定 SQL Server 的可用性群組
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文描述如何使用 Azure 快速入門範本，以半自動化的方式為 Azure 中的 SQL Server 虛擬機器 (VM) 部署 Always On 可用性群組設定。 此處理序中會使用兩個 Azure 快速入門範本： 

   | [範本] | 描述 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | 建立 Windows 容錯移轉叢集，並將 SQL Server VM 加入該叢集。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 建立可用性群組接聽程式，並設定內部負載平衡器。 只有在使用 **101-sql-vm-ag-setup** 範本建立 Windows 容錯移轉叢集的情況下，才能使用此範本。 |
   | &nbsp; | &nbsp; |

可用性群組設定的其他部分必須手動完成，例如建立可用性群組，以及建立內部負載平衡器。 本文提供自動和手動步驟的順序。

雖然本文使用 Azure 快速入門範本來設定可用性群組環境，但也可以使用 [Azure 入口網站](availability-group-azure-portal-configure.md)、 [PowerShell 或 Azure CLI](availability-group-az-commandline-configure.md)，或 [手動](availability-group-manually-configure-tutorial.md) 方式進行。 
 

## <a name="prerequisites"></a>必要條件 
若要使用快速入門範本自動設定 Always On 可用性群組，則必須具備下列先決條件： 
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 具有網域控制站的資源群組。 
- 在 Azure 中執行的一或多個已加入網域的 [vm SQL Server 2016 (或更新版本，) Enterprise edition](./create-sql-vm-portal.md) 位於相同的可用性設定組或可用性區域，而且已 [向 SQL IaaS 代理程式擴充功能註冊](sql-agent-extension-manually-register-single-vm.md)。  
- 有兩個可用 (未由任何實體使用) IP 位址：一個用於內部負載平衡器，另一個用於與可用性群組位於相同子網路內的可用性群組接聽程式。 如果正在使用現有的負載平衡器，則只需要一個可用的 IP 位址。  

## <a name="permissions"></a>權限
使用 Azure 快速入門範本設定 Always On 可用性群組時，必須具備下列權限： 

- 在網域中擁有 **建立電腦物件** 權限的現有網域使用者帳戶。  例如，網域系統管理員帳戶通常會有足夠的權限 (例如：account@domain.com)。 _此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。_
- 控制 SQL Server 的網域使用者帳戶。 


## <a name="create-cluster"></a>建立叢集
在 SQL Server Vm 註冊到 SQL IaaS 代理程式擴充功能之後，您就可以將 SQL Server Vm 加入 *SqlVirtualMachineGroups*。 這項資源會定義 Windows 容錯移轉叢集的中繼資料。 中繼資料包括版本、版次、完整網域名稱，用來管理叢集和 SQL Server 的 Active Directory 帳戶，以及作為雲端見證的儲存體帳戶。 

將 SQL Server VM 新增至 SqlVirtualMachineGroup 資源群組，會啟動 Windows 容錯移轉叢集服務建立叢集，然後將 SQL Server VM 加入該叢集。 此步驟會使用 **101-sql-vm-ag-setup** 快速入門範本進行自動化。 您可使用下列步驟進行實作：

1. 移至 [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) 快速入門範本。 然後，選取 [部署至 Azure]，以在 Azure 入口網站中開啟快速入門範本。
1. 填寫必要欄位，以設定 Windows 容錯移轉叢集的中繼資料。 您可將選擇性欄位保留空白。

   下表顯示範本所需的值： 

   | **欄位** | 值 |
   | --- | --- |
   | **訂用帳戶** |  您的 SQL Server VM 所在的訂用帳戶。 |
   |**資源群組** | 您的 SQL Server VM 所屬的資源群組。 | 
   |**容錯移轉叢集名稱** | 您要用於新 Windows 容錯移轉叢集的名稱。 |
   | **現有 VM 清單** | 要加入可用性群組而納入這個新叢集的 SQL Server VM。 請以逗號和空格分隔這些值 (例如：*SQLVM1, SQLVM2*)。 |
   | **SQL Server 版本** | SQL Server VM 的 SQL Server 版本。 從下拉式清單中加以選取。 目前，僅支援 SQL Server 2016 和 SQL Server 2017 映像。 |
   | **現有完整網域名稱** | SQL Server VM 所在網域的現有 FQDN。 |
   | **現有網域帳戶** | 在範本部署期間建立 [CNO](/windows-server/failover-clustering/prestage-cluster-adds) 時，在網域中擁有 **建立電腦物件** 權限的現有網域使用者帳戶。 例如，網域系統管理員帳戶通常會有足夠的權限 (例如：account@domain.com)。 *此帳戶也應該屬於建立叢集的每個 VM 上的本機系統管理員群組一部分。*| 
   | **網域帳戶密碼** | 前述網域使用者帳戶的密碼。 | 
   | **現有 SQL 服務帳戶** | 可用性群組部署期間控制 [SQL Server 服務](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)的網域使用者帳戶 (例如：account@domain.com)。 |
   | **SQL 服務密碼** | 控制 SQL Server 的網域使用者帳戶所使用密碼。 |
   | **雲端見證名稱** | 建立後將用於雲端見證的新 Azure 儲存體帳戶。 您可修改此名稱。 |
   | **\_成品位置** | 此欄位為預設設定，不應該修改。 |
   | **\_成品位置 SAS 權杖** | 此欄位刻意保留空白。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選取 [我同意上方所述的條款及條件] 核取方塊。 然後選取 [購買] 以完成快速入門範本部署。 
1. 若要監視部署，請從頂端瀏覽橫幅中的 **通知** 鈴鐺圖示選取部署，或移至 Azure 入口網站中的 [資源群組]。 選取 [設定] 下的 [部署]，然後選擇 [Microsoft.Template] 部署。 

>[!NOTE]
> 範本部署期間提供的認證僅於部署期間儲存。 部署完成之後，就會移除這些密碼。 如果將更多 SQL Server VM 新增至叢集，則系統會要求再次提供。 



## <a name="validate-cluster"></a>驗證叢集 

若要讓 Microsoft 支援容錯移轉叢集，必須通過叢集驗證。 使用您慣用的方法（例如遠端桌面通訊協定 (RDP) ）連接到 VM，並驗證您的叢集通過驗證後再繼續進行。 若未這麼做，則會讓您的叢集處於不支援的狀態。 

您可以使用容錯移轉叢集管理員 (FCM) 或下列 PowerShell 命令來驗證叢集：

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>建立可用性群組 
像平常一樣，使用 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 或 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 手動建立可用性群組。 

>[!IMPORTANT]
> 此時請勿建立接聽程式，因為 **101-sql-vm-aglistener-setup** 快速入門範本會在步驟 4 中自動執行此動作。 

## <a name="create-load-balancer"></a>建立負載平衡器

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Always On 可用性群組接聽程式需要 Azure Load Balancer 的內部執行個體。 內部負載平衡器會為可用性群組接聽程式提供「浮動」IP 位址，以加快容錯移轉和重新連線的速度。 如果可用性群組中的 SQL Server VM 屬於相同可用性設定組，則可使用基本的負載平衡器。 否則，即須使用標準負載平衡器。 

> [!IMPORTANT]
> 內部負載平衡器應該與 SQL Server VM 執行個體位於相同的虛擬網路中。 

您只需建立內部負載平衡器即可。 在步驟 4 中，**101-sql-vm-aglistener-setup** 快速入門範本會處理其餘設定 (例如後端集區、健康狀態探查和負載平衡規則)。 

1. 在 Azure 入口網站中，開啟包含 SQL Server 虛擬機器的資源群組。 
2. 在資源群組中，選取 [新增]。
3. 搜尋 **負載平衡器**。 在搜尋結果中，選取由 **Microsoft** 發佈的 [負載平衡器]。
4. 在 [負載平衡器] 刀鋒視窗上，選取 [建立]。
5. 在 [建立負載平衡器] 對話方塊中，依下列方式設定負載平衡器︰

   | 設定 | 值 |
   | --- | --- |
   | **名稱** |輸入代表負載平衡器的文字名稱。 例如，輸入 **sqlLB**。 |
   | **型別** |**內部**：大部分的實作都會使用內部負載平衡器，這可讓相同虛擬網路內的應用程式連線到可用性群組。  </br> **外部**：可讓應用程式透過公用網際網路連線來連線到可用性群組。 |
   | **虛擬網路** | 選取 SQL Server 執行個體所在的虛擬網路。 |
   | **子網路** | 選取 SQL Server 執行個體所在的子網路。 |
   | **IP 位址指派** |**靜態** |
   | **私人 IP 位址** | 從子網路指定可用的 IP 位址。 |
   | **訂用帳戶** |如果您有多個訂用帳戶，此欄位才會出現。 選取您想要與此資源相關聯的訂用帳戶。 通常是與可用性群組的所有資源相同的訂用帳戶。 |
   | **資源群組** |選取 SQL Server 執行個體所在的資源群組。 |
   | **位置** |選取 SQL Server 執行個體所在的 Azure 位置。 |
   | &nbsp; | &nbsp; |

6. 選取 [建立]。 


>[!IMPORTANT]
> 每部 SQL Server VM 的公用 IP 資源都應有標準 SKU，以便與標準負載平衡器相容。 若要確認 VM 公用 IP 資源的 SKU，請移至 [資源群組]，並為 SQL Server VM 選取 [公用 IP 位址] 資源，然後在 [概觀] 窗格的 [SKU] 下方找出其值。 

## <a name="create-listener"></a>建立接聽程式 

使用 **101-sql-vm-aglistener-setup** 快速入門範本可自動建立可用性群組接聽程式，並設定內部負載平衡器。 此範本會佈建 Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener 資源。 **101->101-sql-vm-aglistener-setup-安裝** 快速入門範本（透過 Sql IaaS 代理程式擴充功能）會執行下列動作：

- 為接聽程式建立新的前端 IP 資源 (根據在部署期間提供的 IP 位址值)。 
- 設定叢集和內部負載平衡器的網路設定。 
- 設定內部負載平衡器、健康狀態探查和負載平衡規則的後端集區。
- 使用指定的 IP 位址和名稱建立可用性群組接聽程式。
 
>[!NOTE]
> 只有在使用 **101-sql-vm-ag-setup** 範本建立 Windows 容錯移轉叢集的情況下，您才能使用 **101-sql-vm-aglistener-setup**。
   
   
若要設定內部負載平衡器並建立可用性群組接聽程式，請執行下列動作：
1. 移至 [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) 快速入門範本，然後選取 [部署至 Azure]，在 Azure 入口網站中啟動快速入門範本。
1. 填寫必要欄位以設定內部負載平衡器，並建立可用性群組接聽程式。 您可將選擇性欄位保留空白。 

   下表顯示範本所需的值： 

   | **欄位** | 值 |
   | --- | --- |
   |**資源群組** | 您的 SQL Server VM 和可用性群組所屬的資源群組。 | 
   |**現有容錯移轉叢集名稱** | 您的 SQL Server VM 加入的叢集名稱。 |
   | **現有 SQL 可用性群組**| SQL Server VM 所屬可用性群組的名稱。 |
   | **現有 VM 清單** | 屬於前述可用性群組的 SQL Server VM 名稱。 請以逗號和空格分隔名稱 (例如：*SQLVM1, SQLVM2*)。 |
   | **接聽程式** | 您要指派給接聽程式的 DNS 名稱。 根據預設，此範本指定的名稱為 "aglistener"，但可加以變更。 名稱長度不應該超過 15 個字元。 |
   | **接聽程式連接埠** | 您要讓接聽程式使用的連接埠。 通常，此連接埠預設應為 1433。 這是範本所指定的連接埠號碼。 但是，如果預設連接埠已變更，接聽程式連接埠就應改用該值。 | 
   | **接聽程式 IP** | 您要讓接聽程式使用的 IP 位址。 此位址會在範本部署期間建立，因此請提供尚未使用的位址。  |
   | **現有子網路** | SQL Server VM 的內部子網路名稱 (例如：*default*)。 您可移至 [資源群組]，並依序選取虛擬網路和 [設定] 窗格中的 [子網路]，然後複製 [名稱] 下方的值，以確認此值。 |
   | **現有內部負載平衡器** | 您在步驟 3 中建立的內部負載平衡器名稱。 |
   | **探查連接埠** | 您要讓內部負載平衡器使用的探查連接埠。 此範本預設會使用 59999，但可變更此值。 |
   | &nbsp; | &nbsp; |

1. 如果您同意條款及條件，請選取 [我同意上方所述的條款及條件] 核取方塊。 選取 [購買] 以完成快速入門範本部署。 
1. 若要監視部署，請從頂端瀏覽橫幅中的 **通知** 鈴鐺圖示選取部署，或移至 Azure 入口網站中的 [資源群組]。 選取 [設定] 下的 [部署]，然後選擇 [Microsoft.Template] 部署。 

>[!NOTE]
>如果部署中途失敗，則必須使用 PowerShell 以手動方式 [移除新建立的接聽程式](#remove-listener)，再重新部署 **101-sql-vm-aglistener-setup** 快速入門範本。 

## <a name="remove-listener"></a>移除接聽程式
如果您稍後需要移除範本所設定的可用性群組接聽程式，則必須經過 SQL IaaS 代理程式擴充功能。 因為接聽程式是透過 SQL IaaS 代理程式擴充功能註冊的，所以透過 SQL Server Management Studio 來刪除是不夠的。 

最佳方法是在 PowerShell 中使用下列程式碼片段，透過 SQL IaaS 代理程式擴充功能來刪除它。 這樣做會從 SQL IaaS 代理程式擴充功能移除可用性群組接聽程式中繼資料。 同時實際從可用性群組中刪除該接聽程式。 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>常見錯誤
本節將討論一些已知問題和可能的解決方法。 

可用性群組 **' ' 的可用性群組接聽程式 \<AG-Name> 已存在**： Azure 快速入門範本中用於可用性群組接聽程式的選定可用性群組已經包含接聽程式。 它實際上是在可用性群組內，或是其中繼資料仍保留在 SQL IaaS 代理程式擴充功能內。 請先使用 [PowerShell](#remove-listener) 移除接聽程式，再重新部署 **101-sql-vm-aglistener-setup** 快速入門範本。 

**連接僅適用于主要複本** 這種行為可能來自失敗的 **101->101-sql-vm-aglistener-setup-設定** 範本部署，其已將內部負載平衡器設定為不一致的狀態。 請確認後端集區有列出可用性設定組，且健康情況探查和負載平衡都有其規則。 如果遺漏任何項目，內部負載平衡器的設定就會處於不一致狀態。 

若要消除此行為，請使用 [PowerShell](#remove-listener) 移除接聽程式、透過 Azure 入口網站刪除內部負載平衡器，然後重新執行步驟 3。 

**僅限 BadRequest 的 SQL 虛擬機器清單可以更新** 如果已透過 SQL Server Management Studio (SSMS) 刪除接聽程式，但未從 SQL IaaS 代理程式擴充功能刪除接聽程式，則在部署 **101->101-sql-vm-aglistener-setup-安裝程式** 範本時，可能會發生此錯誤。 透過 SSMS 刪除接聽程式並不會從 SQL IaaS 代理程式擴充功能中移除接聽程式的中繼資料。 您必須透過 [PowerShell](#remove-listener)，將接聽程式從資源提供者中刪除。 

**網域帳戶不存在** 此錯誤可能有兩個原因。 指定的網域帳戶不存在，或缺少[使用者主體名稱 (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) 資料。 **101-sql-vm-ag-setup** 範本會預期 UPN 格式 (也就是 user@domain.com) 的網域帳戶，但部分網域帳戶可能沒有。 會發生這種情況通常是由於伺服器已升級為網域控制站，或已透過 PowerShell 建立使用者，而本機使用者經過移轉成為第一個網域系統管理員帳戶。 

請確認該帳戶存在。 存在的話，您可能會發生第二種情況。 若要解決此問題，請執行下列動作：

1. 在網域控制站上，從 [伺服器管理員] 的 [工具] 選項中開啟 [Active Directory 使用者和電腦] 視窗。 
2. 選取左窗格中的 [使用者]，以移至該帳戶。
3. 以滑鼠右鍵按一下該帳戶，然後選取 [內容]。
4. 選取 [帳戶] 索引標籤。如果 [使用者登入名稱] 方塊為空白，這就是錯誤的原因。 

    ![空白的使用者帳戶表示缺少 UPN](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. 填寫 [使用者登入名稱] 方塊以符合使用者的名稱，並從下拉式清單中選取正確的網域。 
6. 選取 [套用] 儲存變更，並選取 [確定] 關閉對話方塊。 

進行這些變更後，請嘗試再次部署 Azure 快速入門範本。 


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [SQL Server VM 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 常見問題集](frequently-asked-questions-faq.md)
* [SQL Server VM 的定價指導方針](pricing-guidance.md)
* [SQL Server VM 版本資訊](../../database/doc-changes-updates-release-notes.md)
* [切換 SQL Server VM 的授權模式](licensing-model-azure-hybrid-benefit-ahb-change.md)