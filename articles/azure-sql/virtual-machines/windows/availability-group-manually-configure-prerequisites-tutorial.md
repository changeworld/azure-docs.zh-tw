---
title: 教學課程：可用性群組的必要條件
description: 本教學課程說明如何設定在 Azure 虛擬機器上建立 SQL Server Always On 可用性群組的必要條件。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: b72e894b7280a2d3e0fa978125e53ae79b2d20e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669353"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>在 Azure 虛擬機器上的 SQL Server 上建立 Always On 可用性群組的必要條件

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本教學課程說明如何完成在[Azure 虛擬機器（vm）上建立 SQL Server Always On 可用性群組](availability-group-manually-configure-tutorial.md)的必要條件。 當您完成必要條件時，您會在單一資源群組中有一個網域控制站、兩個 SQL Server Vm 和一個見證伺服器。

**估計時間**：可能需要幾個小時的時間才能完成必要條件。 這個時間大部分都是花在建立虛擬機器。

下圖說明您在本教學課程中建置的項目。

![可用性群組](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>檢閱可用性群組文件

本教學課程假設您對 SQL Server Always On 可用性群組有基本的了解。 如果您不熟悉這項技術，請參閱 [Always On 可用性群組概觀 (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)。


## <a name="create-an-azure-account"></a>建立 Azure 帳戶

您需要 Azure 帳戶。 您可以[申請免費 Azure 帳戶](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic)或[啟用 Visual Studio 訂閱者權益](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)。

## <a name="create-a-resource-group"></a>建立資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **+** 即可在入口網站中建立新的物件。

   ![新增物件](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. 在 [Marketplace] 搜尋視窗中輸入**資源群組**。

   ![資源群組](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. 選取 [**資源群組**]。
5. 選取 [建立]。
6. 在 [資源群組名稱] 下方，輸入資源群組的名稱。 例如，輸入 **sql-ha-rg**。
7. 如果您有多個 Azure 訂用帳戶，請確認此訂用帳戶是您要在其中建立可用性群組的 Azure 訂用帳戶。
8. 選取位置。 此位置是您要建立可用性群組的 Azure 區域。 本文會在一個 Azure 位置建置所有資源。
9. 確認已核取 [釘選到儀表板] 。 這個選擇性設定會在 Azure 入口網站儀表板上放置資源群組的捷徑。

   ![資源群組](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. 選取 [**建立**] 以建立資源群組。

Azure 會建立資源群組，並在入口網站中釘選資源群組的捷徑。

## <a name="create-the-network-and-subnets"></a>建立網路和子網路

下一個步驟是在 Azure 資源群組中建立網路和子網路。

此解決方案會使用具有兩個子網路的一個虛擬網路。 [虛擬網路概觀](../../../virtual-network/virtual-networks-overview.md)提供有關 Azure 中網路的詳細資訊。

若要在 Azure 入口網站中建立虛擬網路：

1. 在您的資源群組中，選取 [ **+ 新增**]。 

   ![新項目](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. 搜尋 **虛擬網路**。

     ![搜尋虛擬網路](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. 選取 [虛擬網路]。
4. 在 [**虛擬網路**] 上，選取 [ **Resource Manager** ] 部署模型，然後選取 [**建立**]。

    下表顯示虛擬網路的設定：

   | **欄位** | 值 |
   | --- | --- |
   | **名稱** |autoHAVNET |
   | **位址空間** |10.33.0.0/24 |
   | **子網路名稱** |管理 |
   | **子網路位址範圍** |10.33.0.0/29 |
   | **訂用帳戶** |指定您打算使用的訂用帳戶。 如果您只有一個訂用帳戶，則**訂用帳戶**為留白。 |
   | **資源群組** |選擇 [使用現有的]，然後挑選資源群組的名稱。 |
   | **位置** |指定 Azure 位置。 |

   您的位址空間和子網路位址範圍可能與此表有所不同。 視您的訂用帳戶而定，入口網站會建議可用的位址空間和對應的子網路位址範圍。 如果沒有足夠的位址空間可供使用，請使用不同的訂用帳戶。

   此範例會使用子網路名稱 **Admin**。此子網路用於網域控制站。

5. 選取 [建立]。

   ![設定虛擬網路](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Azure 會讓您回到入口網站儀表板，並在建立完新網路時通知您。

### <a name="create-a-second-subnet"></a>建立第二個子網路

新的虛擬網路有一個子網路，名為 **Admin**。網域控制站會使用此子網路。 SQL Server VM 會使用名為 **SQL** 的第二個子網路。 若要設定此子網路：

1. 在儀表板上，選取您建立的資源群組 [ **SQL-HA-RG**]。 在 [資源] 之下的資源群組中尋找此網路。

    如果看不到**SQL-HA-RG** ，請選取 [**資源群組**] 並依資源組名篩選來尋找它。

2. 選取資源清單中的 [ **autoHAVNET** ]。 
3. 在 [autoHAVNET] 虛擬網路上的 [設定] 下方，選取 [子網路]。

    請注意您已建立的子網路。

   ![設定虛擬網路](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. 若要建立第二個子網，請選取 [ **+ 子網**]。
6. 在 [新增子網路] 上，於 [名稱] 下方輸入 **sqlsubnet** 來設定子網路。 Azure 會自動指定有效的 [位址範圍] 。 確認此位址範圍中至少有 10 個位址。 在生產環境中，您可能需要更多位址。
7. 選取 [確定]。

    ![設定虛擬網路](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

下表摘要說明網路組態設定︰

| **欄位** | 值 |
| --- | --- |
| **名稱** |**autoHAVNET** |
| **位址空間** |這個值取決於您訂用帳戶中的可用位址空間。 一般的值是 10.0.0.0/16。 |
| **子網路名稱** |**admin** |
| **子網路位址範圍** |這個值取決於您訂用帳戶中的可用位址範圍。 一般的值是 10.0.0.0/24。 |
| **子網路名稱** |**sqlsubnet** |
| **子網路位址範圍** |這個值取決於您訂用帳戶中的可用位址範圍。 一般的值是 10.0.1.0/24。 |
| **訂用帳戶** |指定您打算使用的訂用帳戶。 |
| **資源群組** |**SQL-HA-RG** |
| **位置** |指定您為資源群組選擇的相同位置。 |

## <a name="create-availability-sets"></a>建立可用性設定組

建立虛擬機器之前，您必須建立可用性設定組。 可用性設定組可縮短計劃性或非計劃性維護事件的停機時間。 Azure 可用性設定組是 Azure 置於實體容錯網域和更新網域上的邏輯資源群組。 容錯網域會確保可用性設定組的成員具有個別的電源和網路資源。 更新網域會確保可用性設定組的成員不會同時停機來進行維護。 如需詳細資訊，請參閱[管理虛擬機器的可用性](../../../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

您需要兩個可用性設定組。 一個用於網域控制站。 第二個用於 SQL Server VM。

若要建立可用性設定組，請移至資源群組，然後選取 [**新增**]。 輸入**可用性設定組**以篩選結果。 選取結果中的 [**可用性設定組**]，然後選取 [**建立**]。

根據下表中的參數設定兩個可用性設定組：

| **欄位** | 網域控制站可用性設定組 | SQL Server 可用性設定組 |
| --- | --- | --- |
| **名稱** |adavailabilityset |sqlavailabilityset |
| **資源群組** |SQL-HA-RG |SQL-HA-RG |
| **容錯網域** |3 |3 |
| **更新網域** |5 |3 |

建立可用性設定組之後，請回到 Azure 入口網站中的資源群組。

## <a name="create-domain-controllers"></a>建立網域控制站

在您建立網路、子網路與可用性設定組之後，即可建立網域控制站的虛擬機器。

### <a name="create-virtual-machines-for-the-domain-controllers"></a>建立網域控制站的虛擬機器

若要建立和設定網域控制站，請回到 **SQL-HA-RG** 資源群組。

1. 選取 [新增]。 
2. 輸入 **Windows Server 2016 資料中心**。
3. 選取 [ **Windows Server 2016 Datacenter**]。 在**Windows Server 2016 Datacenter**中，確認部署模型為**Resource Manager**，然後選取 [**建立**]。 

重複上述步驟以建立兩部虛擬機器。 為兩部虛擬機器命名︰

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > **ad-secondary-dc** 虛擬機器是選擇性選項，可為 Active Directory Domain Services 提供高可用性。
  >

下表顯示這兩部機器的設定：

| **欄位** | 值 |
| --- | --- |
| **名稱** |第一網域控制站：ad-primary-dc。</br>第二網域控制站：ad-secondary-dc。 |
| **VM 磁碟類型** |SSD |
| **使用者名稱** |DomainAdmin |
| **密碼** |Contoso!0000 |
| **訂用帳戶** |*您的訂用帳戶* |
| **資源群組** |SQL-HA-RG |
| **位置** |*您的位置* |
| **大小** |DS1_V2 |
| **Storage** | [使用受控磁碟] - [是] |
| **虛擬網路** |autoHAVNET |
| **子網路** |admin |
| **公用 IP 位址** |*與 VM 同名* |
| **網路安全性群組** |*與 VM 同名* |
| **可用性設定組** |adavailabilityset </br>**容錯網域**：2 </br>**更新網域**：2|
| **診斷** |啟用 |
| **診斷儲存體帳戶** |*自動建立* |

   >[!IMPORTANT]
   >您只可以在建立 VM 時才可將 VM 置於可用性設定組。 在建立 VM 之後，即無法變更可用性設定組。 請參閱[管理虛擬機器的可用性](../../../virtual-machines/linux/manage-availability.md)。

Azure 會建立虛擬機器。

在虛擬機器建立後，設定網域控制站。

### <a name="configure-the-domain-controller"></a>設定網域控制站

在接下來的步驟中，請將 **ad-primary-dc** 電腦設定為 corp.contoso.com 的網域控制站。

1. 在入口網站中開啟 [SQL-HA-RG] 資源群組，然後選取 [ad-primary-dc] 機器。 在 [ **ad-主要-Dc**] 上，選取 **[連線]** 以開啟用於遠端桌面存取的 RDP 檔案。

    ![連接到虛擬機器](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. 使用所設定的系統管理員帳戶 ( **\DomainAdmin**) 和密碼 (**Contoso!0000**) 來登入。
3. 根據預設，應會顯示 [伺服器管理員]  儀表板。
4. 選取儀表板上的 [**新增角色及功能**] 連結。

    ![伺服器總管 - 新增角色](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. 連續選取 [下一步]，直到到達 [伺服器角色] 區段。
6. 選取 [Active Directory Domain Services] 和 [DNS 伺服器] 角色。 出現提示時，請新增這些角色所需的任何其他功能。

   > [!NOTE]
   > Windows 會顯示無靜態 IP 位址的警告。 如果您要測試設定，請選取 [**繼續**]。 針對生產環境情況，請在 Azure 入口網站中將 IP 位址設定為靜態，或[使用 PowerShell 來設定網域控制站機器的靜態 IP 位址](../../../virtual-network/virtual-networks-reserved-private-ip.md)。
   >

    ![[新增角色] 對話方塊](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. 選取 **[下一步]** ，直到您到達 [**確認**] 區段為止。 選取 [必要時自動重新啟動目的地伺服器] 核取方塊。
8. 選取 [安裝]。
9. 功能安裝完畢後，請回到 [伺服器管理員]  儀表板。
10. 在左側窗格中選取新的 [AD DS]  選項。
11. 選取黃色警告列上的 [**更多**] 連結。

    ![DNS 伺服器 VM 上的 AD DS 對話方塊](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. 在 [**所有伺服器工作詳細資料**] 對話方塊的 [**動作**] 欄中，選取 [**將此伺服器升級為網域控制站**]。
13. 在 **Active Directory 網域服務組態精靈**中，使用下列值：

    | **頁面** | 設定 |
    | --- | --- |
    | **部署組態** |**新增樹系**<br/> **根網域名稱** = corp.contoso.com |
    | **網域控制站選項** |**DSRM 密碼** = Contoso!0000<br/>**確認密碼** = Contoso!0000 |

14. 選取 **[下一步**] 以流覽嚮導中的其他頁面。 在 [先決條件檢查] 頁面上，確認是否出現下列訊息：**已順利通過所有先決條件檢查**。 您可以檢閱任何適用的警告訊息，但是仍可以繼續進行安裝。
15. 選取 [安裝]。 **ad-primary-dc** 虛擬機器會自動重新開機。

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>請注意主要網域控制站的 IP 位址

使用 DNS 的主要網域控制站。 請注意主要網域控制站 IP 位址。

取得主要網域控制站 IP 位址的方法之一是透過 Azure 入口網站。

1. 在 Azure 入口網站中，開啟資源群組。

2. 選取主域控制站。

3. 在網域主控站上，選取 [**網路介面**]。

![網路介面](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

請注意此伺服器的私人 IP 位址。

### <a name="configure-the-virtual-network-dns"></a>設定虛擬網路 DNS

建立第一個網域控制站並啟用第一部伺服器上的 DNS 後，請為 DNS 設定虛擬網路以使用此伺服器。

1. 在 [Azure 入口網站中，選取 [虛擬網路]。

2. 在 [**設定**] 底下，選取 [ **DNS 伺服器**]。

3. 選取 [**自訂**]，然後輸入主域控制站的私人 IP 位址。

4. 選取 [儲存]。

### <a name="configure-the-second-domain-controller"></a>設定第二個網域控制站

在主要網域控制站重新開機之後，您可以設定第二個網域控制站。 這個選擇性步驟適用於高可用性。 請依照下列步驟設定第二個網域控制站：

1. 在入口網站中開啟 [SQL-HA-RG] 資源群組，然後選取 [ad-secondary-dc] 機器。 在 [ **ad-次要-Dc**] 上，選取 **[連線]** 以開啟用於遠端桌面存取的 RDP 檔案。
2. 使用所設定的系統管理員帳戶 (**BUILTIN\DomainAdmin**) 和密碼 (**Contoso!0000**) 來登入 VM。
3. 將慣用 DNS 伺服器位址變更為網域控制站的位址。
4. 在 [**網路和共用中心**] 中，選取網路介面。

   ![Linux](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. 選取 [屬性] 。
6. 選取 **[網際網路通訊協定第4版（TCP/IPv4）** ]，然後選取 [**屬性**]。
7. 選取 [**使用下列的 dns 伺服器位址**]，然後在 [**慣用 DNS 伺服器**] 中指定主域控制站的位址。
8. 選取 **[確定]**，然後按一下 [**關閉**] 以認可變更。 現在您可以將 VM 加入 **corp.contoso.com**。

   >[!IMPORTANT]
   >如果您在變更 DNS 設定之後失去遠端桌面的連線，請移至 Azure 入口網站，然後重新啟動虛擬機器。

9. 從遠端桌面到次要網域控制站，開啟**伺服器管理員儀表板**。
10. 選取儀表板上的 [**新增角色及功能**] 連結。

    ![伺服器總管 - 新增角色](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. 連續選取 [下一步]，直到到達 [伺服器角色] 區段。
12. 選取 [Active Directory Domain Services] 和 [DNS 伺服器] 角色。 出現提示時，請新增這些角色所需的任何其他功能。
13. 功能安裝完畢後，請回到 [伺服器管理員]  儀表板。
14. 在左側窗格中選取新的 [AD DS]  選項。
15. 選取黃色警告列上的 [**更多**] 連結。
16. 在 [**所有伺服器工作詳細資料**] 對話方塊的 [**動作**] 欄中，選取 [**將此伺服器升級為網域控制站**]。
17. 在 [部署組態] 下，選取 [將網域控制站新增至現有網域]。

    ![部署設定](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. 按一下 [選取]。
19. 使用系統管理員帳戶 (**CORP.CONTOSO.COM\domainadmin**) 和密碼 (**Contoso!0000**) 來進行連線。
20. 在 **[從樹系中選取網域**] 中，選擇您的網域，然後選取 **[確定]**。
21. 在 [網域控制站選項] 中，使用預設值並設定 DSRM 密碼。

    >[!NOTE]
    >[DNS 選項] 頁面可能警告您，指出無法建立此 DNS 伺服器的委派。 您在非生產環境中可以忽略這個警告。
    >

22. 選取 **[下一步**]，直到對話到達**必要條件**檢查為止。 然後選取 [**安裝**]。

在伺服器完成組態變更之後，重新啟動伺服器。

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>將私人 IP 位址新增至 VPN DNS 伺服器的第二個網域控制站

在 Azure 入口網站中的虛擬網路下，變更 DNS Server 以包含次要網域控制站的 IP 位址。 此設定讓 DNS 服務能夠進行備援。

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> 設定網域帳戶

在接下來的步驟中，您將設定 Active Directory 帳戶。 下表顯示帳戶：

| |安裝帳戶<br/> |sqlserver-0 <br/>SQL Server 和 SQL Agent 服務帳戶 |sqlserver-1<br/>SQL Server 和 SQL Agent 服務帳戶
| --- | --- | --- | ---
|**First Name** |安裝 |SQLSvc1 | SQLSvc2
|**使用者 SamAccountName** |安裝 |SQLSvc1 | SQLSvc2

使用下列步驟建立每個帳戶。

1. 登入 **ad-primary-dc** 電腦。
2. 在**伺服器管理員**中，選取 [**工具**]，然後選取 [ **Active Directory 管理中心**]。   
3. 從左窗格中選取 [corp (本機)]。
4. 在**適當的**[工作] 窗格中，選取 [**新增**]，然後選取 [**使用者**]。

   ![Active Directory 管理中心](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >設定每個帳戶的複雜密碼。<br/> 對於非生產環境，將使用者帳戶設定為永不過期。
   >

5. 選取 **[確定]** 以建立使用者。
6. 針對三個帳戶重複上述步驟。

### <a name="grant-the-required-permissions-to-the-installation-account"></a>將必要權限授與安裝帳戶

1. 在 [Active Directory 管理中心] 中，選取左窗格中的 [企業 (本機)]。 然後**在右側的 [工作] 窗格**中，選取 [**屬性**]。

    ![公司使用者內容](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. 選取 [**擴充**功能]，然後選取 [**安全性**] 索引標籤上的 [**高級**] 按鈕。
3. 在 [ **corp 的 Advanced Security 設定**] 對話方塊中，選取 [**新增**]。
4. 按一下 [**選取主體**]，搜尋**CORP\Install**，然後選取 **[確定]**。
5. 選取 [讀取全部內容] 核取方塊。

6. 選取 [建立電腦物件] 核取方塊。

     ![公司使用者權限](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. 選取 [確定]  ，然後再選取一次 [建立]  。 關閉 [公司] 內容視窗。

現在您已設定完 Active Directory 和使用者物件，請建立兩個 SQL Server VM 和一個見證伺服器 VM。 然後，將三個 VM 都加入網域。

## <a name="create-sql-server-vms"></a>建立 SQL Server VM

建立第三部額外的虛擬機器。 此解決方案需要兩個有 SQL Server 執行個體的虛擬機器。 第三虛擬機器的功能為見證。 Windows Server 2016 可以使用[雲端見證](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。 不過，為了與舊版作業系統一致，本文使用見證的虛擬機器。  

繼續之前，請先考慮下列設計決策。

* **儲存體 - Azure 受控磁碟**

   為虛擬機器儲存體使用 Azure 受控磁碟。 Microsoft 為 SQL Server 虛擬機器建議受控磁碟。 受控磁碟會在背景中處理儲存體。 此外，當具有受控磁碟的虛擬機器在相同的可用性設定組時，Azure 會分散儲存資源以提供適當的備援。 如需其他資訊，請參閱 [Azure 受控磁碟概觀 (英文)](../../../virtual-machines/linux/managed-disks-overview.md)。 如需可用性設定組中受控磁碟的具體資訊，請參閱[在可用性設定組中使用 VM 的受控磁碟 (英文)](../../../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)。

* **網路 - 生產環境中的私人 IP 位址**

   本教學課程針對虛擬機器使用公用 IP 位址。 公用 IP 位址可讓您透過網際網路直接與虛擬機器進行遠端連線，並讓設定步驟更容易。 在生產環境中，Microsoft 僅建議使用私人 IP 位址，以降低 SQL Server 執行個體 VM 資源出現弱點的機率。

### <a name="create-and-configure-the-sql-server-vms"></a>建立及設定 SQL Server VM

接下來，建立三個 Vm-兩個 SQL Server Vm，另一個 VM 用於一個額外的叢集節點。 若要建立每個 Vm，請回到**SQL-HA-RG**資源群組，然後選取 [**新增**]。 搜尋適當的資源庫專案，選取 [**虛擬機器**]，然後選取 [**從元件庫**]。 使用下表中的資訊可協助您建立 VM：


| 頁面 | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 選取適當的資源庫項目 |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |
| 虛擬機器組態 **基本** |**名稱** = cluster-fsw<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置**= 您的 Azure 位置 |**名稱** = sqlserver-0<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置**= 您的 Azure 位置 |**名稱** = sqlserver-1<br/>**使用者名稱** = DomainAdmin<br/>**密碼** = Contoso!0000<br/>**訂用帳戶** = 您的訂用帳戶<br/>**資源群組** = SQL-HA-RG<br/>**位置**= 您的 Azure 位置 |
| 虛擬機器組態 **大小** |**大小** = DS1\_V2 (1 vCPU，3.5 GB) |**大小** = DS2\_V2 (2 vCPU，7 GB)</br>大小必須支援 SSD 儲存體 (高階磁磁支援。 )) |**大小** = DS2\_V2 (2 vCPU，7 GB) |
| 虛擬機器組態 **設定** |**儲存體**：使用受控磁碟。<br/>**虛擬網路** = autoHAVNET<br/>**子網路** = sqlsubnet(10.1.1.0/24)<br/>**公用 IP 位址**自動產生。<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |**儲存體**：使用受控磁碟。<br/>**虛擬網路** = autoHAVNET<br/>**子網路** = sqlsubnet(10.1.1.0/24)<br/>**公用 IP 位址**自動產生。<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |**儲存體**：使用受控磁碟。<br/>**虛擬網路** = autoHAVNET<br/>**子網路** = sqlsubnet(10.1.1.0/24)<br/>**公用 IP 位址**自動產生。<br/>**網路安全性群組** = 無<br/>**監視診斷** = 啟用<br/>**診斷儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性設定組** = sqlAvailabilitySet<br/> |
| 虛擬機器組態 **SQL Server 設定** |不適用 |**SQL 連線** = 私用 (在虛擬網路內)<br/>**連接埠** = 1433<br/>**SQL 驗證** = 停用<br/>**儲存體組態** = 一般<br/>**自動修補** = 星期日 2:00<br/>**自動備份** = 停用</br>**Azure 金鑰保存庫整合** = 已停用 |**SQL 連線** = 私用 (在虛擬網路內)<br/>**連接埠** = 1433<br/>**SQL 驗證** = 停用<br/>**儲存體組態** = 一般<br/>**自動修補** = 星期日 2:00<br/>**自動備份** = 停用</br>**Azure 金鑰保存庫整合** = 已停用 |

<br/>

> [!NOTE]
> 此處建議的機器大小適用于在 Azure 虛擬機器中測試可用性群組。 為獲得生產工作負載的最佳效能，請參閱[Azure 虛擬機器中 SQL Server 的效能最佳作法](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中 SQL Server 機器大小和設定的建議。
>

將三個 VM 都佈建完畢之後，您必須將它們加入 **corp.contoso.com** 網域，並將 CORP\Install 系統管理權限授與這些機器。

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>將伺服器加入網域

您現在可以將 VM 加入 **corp.contoso.com**。 為 SQL Server VM 和檔案共用見證伺服器執行下列步驟：

1. 遠端連線虛擬機器與 **BUILTIN\DomainAdmin**。
2. 在 [伺服器管理員]**** 中，選取 [本機伺服器]****。
3. 選取 [**工作組**] 連結。
4. 在 [**電腦名稱稱**] 區段中，選取 [**變更**]。
5. 選取 [網域] 核取方塊，然後在文字方塊中輸入 **corp.contoso.com**。 選取 [確定]。
6. 在 [Windows 安全性] 快顯對話方塊中，指定預設的網域系統管理員帳戶 (**CORP\DomainAdmin**) 和密碼 (**Contoso!0000**) 的認證。
7. 當您看到「歡迎使用 corp.contoso.com 網域」訊息時，請選取 **[確定]**。
8. 選取 [**關閉**]，然後在快顯對話方塊中選取 [**立即重新開機**]。

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>將 Corp\Install 使用者新增為每個叢集 VM 的系統管理員

每個虛擬機器以網域的成員重新啟動之後，新增 **CORP\Install** 作為本機系統管理員群組的成員。

1. 等候 VM 重新啟動，然後從主要網域控制站重新啟動 RDP 檔案，以使用 **CORP\DomainAdmin** 帳戶來登入 **sqlserver-0**。

   >[!TIP]
   >請確定您是使用網域系統管理員帳戶來登入。 在先前步驟中，您使用的是內建的系統管理員帳戶。 現在伺服器在網域中，使用網域帳戶。 在您的 RDP 工作階段中，指定 *DOMAIN*\\*username*。
   >

2. 在**伺服器管理員**中，選取 [**工具**]，然後選取 [**電腦管理**]。
3. 在 [電腦管理 ] 視窗中，展開 [本機使用者和群組]，然後選取 [群組]。
4. 按兩下 [系統管理員]  群組。
5. 在 [系統**管理員屬性**] 對話方塊中，選取 [**新增**] 按鈕。
6. 輸入使用者**CORP\Install**，然後選取 **[確定]**。
7. 選取 **[確定]** 以關閉 [**系統管理員屬性**] 對話方塊。
8. 在 **sqlserver-1** 和 **cluster-fsw** 上重複上述步驟。

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>設定 SQL Server 服務帳戶

在每個 SQL Server VM 上，設定 SQL Server 服務帳戶。 使用您設定網域帳戶時所建立的帳戶。

1. 開啟 **SQL Server 組態管理員**。
2. 以滑鼠右鍵按一下 [SQL Server] 服務，然後選取 [**屬性**]。
3. 設定帳戶和密碼。
4. 在其他 SQL Server VM 上重複上述步驟。  

針對 SQL Server 可用性群組，每個 SQL Server VM 必須都以網域帳戶執行。

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>在每個 SQL Server VM 上建立安裝帳戶登入

使用安裝帳戶 (CORP\install) 來設定可用性群組。 這個帳戶必須是每個 SQL Server VM 上 [系統管理員 (sysadmin)] 固定伺服器角色的成員。 下列步驟會建立安裝帳戶的登入︰

1. 使用 *\<MachineName\>\DomainAdmin* 帳戶透過「遠端桌面通訊協定」(RDP) 連線到伺服器。

1. 開啟 SQL Server Management Studio，然後連線到 SQL Server 的本機執行個體。

1. 在 [**物件總管**中，選取 [**安全性**]。

1. 以滑鼠右鍵按一下 [登入]。 選取 **[新增登**入]。

1. 在 [登入 - 新增]**** 中，選取 [搜尋]****。

1. 選取 [**位置**]。

1. 輸入網域系統管理員網路認證。

1. 使用安裝帳戶。

1. 將登入設定為 [系統管理員 (sysadmin)] 固定伺服器角色的成員。

1. 選取 [確定]。

在其他 SQL Server VM 上重複上述步驟。

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>將容錯移轉叢集功能新增至兩個 SQL Server VM

若要新增「容錯移轉叢集」功能，請在兩個 SQL Server VM 上執行下列步驟：

1. 使用 *CORP\install* 帳戶透過「遠端桌面通訊協定」(RDP) 連線到 SQL Server 虛擬機器。 開啟 [伺服器管理員儀表板] 。
2. 選取儀表板上的 [**新增角色及功能**] 連結。

    ![伺服器總管 - 新增角色](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. 連續選取 [下一步]，直到到達 [伺服器功能] 區段。
4. 在 [功能] 中，選取 [容錯移轉叢集]。
5. 新增任何其他必要的功能。
6. 選取 [**安裝**] 以新增功能。

在另一個 SQL Server VM 上重複上述步驟。

  >[!NOTE]
  > 此步驟 (以及實際將 SQL Server VM 加入到容錯移轉叢集) 現在可以使用 [Azure SQL VM CLI](availability-group-az-cli-configure.md) 與 [Azure 快速入門範本](availability-group-quickstart-template-configure.md)來自動化。
  >


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> 在每個 SQL Server VM 上設定防火牆

方案需要在防火牆中開啟下列 TCP 通訊埠︰

- **SQL Server 的 VM**：埠1433，適用于 SQL Server 的預設實例。
- **Azure 負載平衡器探查：** 任何可用的埠。 範例會經常使用 59999。
- **資料庫鏡像端點：** 任何可用的埠。 範例會經常使用 5022。

您必須在兩個 SQL Server VM 上都開啟防火牆連接埠。

開啟連接埠的方法取決於您使用的防火牆解決方案。 下一節將說明如何在「Windows 防火牆」中開啟連接埠。 在每個 SQL Server VM 上開啟必要的連接埠。

### <a name="open-a-tcp-port-in-the-firewall"></a>在防火牆中開啟 TCP 通訊埠

1. 在第一個 SQL Server 上 [開始] 畫面中啟動 [具備進階安全性的 Windows 防火牆]。
2. 在左窗格上，選取 [輸入規則]。 在右窗格中，選取 [**新增規則**]。
3. 針對 [規則類型]，選擇 [連接埠]。
4. 針對連接埠，指定 [TCP] 並輸入適當的連接埠號碼。 請參閱下列範例：

   ![SQL 防火牆](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. 選取 [下一步]。
6. 在 [**動作**] 頁面上，保持選取 **[允許連接**]，然後選取 **[下一步]**。
7. 在 [**設定檔**] 頁面上，接受預設設定，然後選取 **[下一步]**。
8. 在 [**名稱**] 頁面上，于 [**名稱**] 文字方塊中指定規則名稱（例如**Azure LB 探查**），然後選取 **[完成]**。

在第二個 SQL Server VM 上重複上述步驟。

## <a name="configure-system-account-permissions"></a>設定系統帳戶權限

若要為系統帳戶建立帳戶並授與適當權限，請在每個 SQL Server 執行個體上完成下列步驟：

1. 在每個 SQL Server 執行個體上建立適用於 `[NT AUTHORITY\SYSTEM]` 的帳戶。 下列指令碼會建立此帳戶：

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. 在每個 SQL Server 執行個體上，將下列權限授與 `[NT AUTHORITY\SYSTEM]`：

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   下列指令碼會授與這些權限：

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>後續步驟

* [在 Azure 上建立 SQL Server Always On 可用性群組虛擬機器](availability-group-manually-configure-tutorial.md)
