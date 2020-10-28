---
title: 使用 Azure 入口網站布建 Windows 虛擬機器
description: 本指南涵蓋使用 Azure 入口網站在 Windows 虛擬機器上布建 SQL Server 的可用選項。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6658dcd54c7290bbbad0c24c1cc0debd5ead8b18
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789857"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>如何使用 Azure 入口網站以 SQL Server 布建 Windows 虛擬機器

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本指南涵蓋的選項可供使用 Azure 入口網站在 Windows 虛擬機器上布建 SQL Server (VM) 。 本文所涵蓋的設定選項比 [SQL SERVER VM 快速入門](sql-vm-create-portal-quickstart.md)更深入，在單一設定方面更深入。 

使用本指南建立您自己的 SQL Server VM。 或者，將本指南當做 Azure 入口網站中可用選項的參考資料。

> [!TIP]
> 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](frequently-asked-questions-faq.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> SQL Server 虛擬機器資源庫映像

當您建立 SQL Server 虛擬機器時，您可以從虛擬機器資源庫選取其中一個預先設定的映像。 下列步驟示範如何選取其中一個 SQL Server 2017 映像。

1. 在 Azure 入口網站的左側功能表中，選取 [Azure SQL]。 如果 **Azure SQL** 不在清單中，請選取 [所有服務]，然後在搜尋方塊中輸入 Azure SQL。 

   您也可以選取 **AZURE SQL** 旁的星號，將其儲存為我的最愛，並將其新增為左側導覽中的專案。 

1. 選取 [+ 新增] 以開啟 [選取 SQL 部署選項] 頁面。 您可選取 [顯示詳細資料]，以檢視其他資訊。 
1. 在 [ **SQL 虛擬機器** ] 磚的 [SQL Server 映射搜尋] 方塊中輸入 *2017* ，然後從下拉式清單中選取 [ **免費 SQL Server 授權： Windows Server 2016 上的 SQL Server 2017 Developer** ]。 

   ![選取 SQL VM 映像](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > 本文會使用 Developer 版本，因為它是一種功能完整的免費版本 SQL Server 用於開發測試。 您只需支付執行 VM 的費用。 不過，您在本逐步介紹中可以自由選擇您要使用的任何映像。 如需可用映像的說明，請參閱 [SQL Server Windows 虛擬機器概觀](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)。

   > [!TIP]
   > SQL Server 的授權成本已併入您建立之 VM 的每秒鐘價格中，並且根據版本和核心而有所不同。 不過，SQL Server Developer edition 可免費進行開發和測試，而不是生產環境。 此外，針對輕量工作負載 (小於 1 GB 的記憶體，且儲存體) 小於 10 GB，則可免費取得 SQL Express。 您也可以自備授權 (BYOL)，僅支付 VM 費用。 這些映像的名稱前面會有 {BYOL}。 
   >
   > 如需這些選項的詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](pricing-guidance.md)。


1. 選取 [建立]。


## <a name="1-configure-basic-settings"></a>1.設定基本設定

在 [基本概念] 索引標籤中提供下列資訊：

* 在 [專案詳細資料] 下方，確認已選取正確的訂用帳戶。 
* 在 [資源群組] 區段中，從清單選取現有的資源群組，或選擇 [新建] 來建立新的資源群組。 資源群組是 Azure (虛擬機器、儲存體帳戶、虛擬網路等) 中相關資源的集合。 

  ![訂用帳戶](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > 如果您只是測試或了解 Azure 中的 SQL Server 部署，使用新的資源群組很有幫助。 完成測試之後，請刪除資源群組以自動刪除此 VM 以及與該資源群組相關聯的所有資源。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../../active-directory-b2c/overview.md)。


* 在 [執行個體詳細資料] 下方：

    1. 輸入唯一的 **虛擬機器名稱** 。  
    1. 選擇 [區域] 的位置。 
    1. 針對本指南，請將 [可用性選項] 保留在設為 [不需要基礎結構備援]。 如需關於可用性選項的詳細資訊，請參閱[可用性](../../../virtual-machines/availability.md)。 
    1. 在 **映像** 清單中，選取免費的 _SQL Server 授權：_ Windows Server 2016 上的 SQL Server 2017 Developer 的映像。  
    1. 選擇為虛擬機器的 **大小** [變更大小]，然後選取 **A2 Basic** 供應項目。 在資源使用完畢後請務必加以清除，以防止產生任何非預期的費用。 對於生產工作負載，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳做法](performance-guidelines-best-practices.md)中建議的機器大小和組態。

    ![執行個體詳細資料](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> [選擇大小] 視窗上顯示的估計每月成本不包含 SQL Server 授權成本。 這估計只是 VM 單獨的成本。 若為 SQL Server 的 Express 和 Developer 版本，此為預估的總成本。 若為其他版本，請參閱 [Windows 虛擬機器價格頁面](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)，然後選取您的目標 SQL Server 版本。 另請參閱 [SQL Server Azure VM 的定價指導](pricing-guidance.md)和[虛擬機器大小](../../../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json)。

* 在 [ **系統管理員帳戶** ] 下，提供使用者名稱和密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

   ![系統管理員帳戶](./media/create-sql-vm-portal/basics-administrator-account.png)

* 在 [輸入連接埠規則] 下方選擇 [允許選取的連接埠]，然後從下拉式清單中選取 [RDP (3389)]。 

   ![輸入連接埠規則](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2.設定選用功能

### <a name="disks"></a>磁碟

在 [磁碟] 索引標籤上，設定磁碟選項。 

* 在 [OS 磁碟類型] 下方，從下拉式清單選取想要為 OS 設定的磁碟類型。 建議針對生產系統使用 [進階]，但此選項無法提供基本 VM 使用。 若要使用進階 SSD，請變更虛擬機器大小。 
* 在 [進階] 下方，選取位於使用 [受控磁碟] 下方的 [是]。

   > [!NOTE]
   > Microsoft 建議使用 SQL Server 適用的受控磁碟。 受控磁碟會在背景中處理儲存體。 此外，當具有受控磁碟的虛擬機器在相同的可用性設定組時，Azure 會分散儲存資源以提供適當的備援。 如需詳細資訊，請參閱 [Azure 受控磁碟概觀](../../../virtual-machines/managed-disks-overview.md)。 如需可用性設定組中受控磁碟的具體資訊，請參閱[在可用性設定組中使用 VM 的受控磁碟](../../../virtual-machines/manage-availability.md)。

![SQL VM 磁碟設定](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>網路功能

在 [網路] 索引標籤上，請設定網路選項。 

* 建立新的 **虛擬網路** ，或使用您 SQL Server VM 的現有虛擬網路。 同時指定 **子網路** 。 

* 在 [ **NIC 網路安全性群組** ] 底下，選取 [基本] 安全性群組或 [advanced] 安全性群組。 選擇 [基本] 選項可讓您選取 SQL Server VM 的輸入埠，這是在 [ **基本** ] 索引標籤上設定的相同值。選取 [advanced] 選項可讓您選擇現有的網路安全性群組，或建立一個新的群組。 

* 您可以對網路設定進行其他變更，或保留預設值。

![SQL VM 網路設定](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>監視

在 [ **監視** ] 索引標籤上，設定監視和自動關機。 

* Azure 根據預設會啟用為 VM 所指定相同儲存體帳戶的 **開機診斷** 。 您可以在此索引標籤上變更這些設定，並啟用 **OS 來賓診斷** 。 
* 您也可以在此索引標籤上啟用 **系統指派的受控識別** 和 **自動關機** 。 

![SQL VM 管理設定](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3.進行 SQL Server 設定

在 [SQL Server 設定] 索引標籤上，設定 SQL Server 的特定設定和最佳化。 您可以針對 SQL Server 設定下列設定：

- [連線能力](#connectivity)
- [驗證](#authentication)
- [Azure Key Vault 整合](#azure-key-vault-integration)
- [儲存體組態](#storage-configuration)
- [自動修補](#automated-patching)
- [自動備份](#automated-backup)
- [機器學習服務](#machine-learning-services)


### <a name="connectivity"></a>連線能力

在 [SQL 連線] 底下，指定您要對此 VM 上的 SQL Server 執行個體進行的存取類型。 基於本逐步介紹的目的，選取 [公用 (網際網路)] 以允許從網際網路上的機器或服務連線至 SQL Server。 在已選取此選項的情況下，Azure 會自動設定防火牆和網路安全性群組以允許所選取連接埠上的流量。

> [!TIP]
> 根據預設，SQL Server 會在已知通訊埠 **1433** 上接聽。 為了提高安全性，變更前一個對話方塊中的連接埠，以在非預設連接埠 (例如 1401) 上接聽。 如果您變更埠，則必須從任何用戶端工具（例如 SQL Server Management Studio (SSMS) ）使用該埠進行連接。

![SQL VM 安全性](./media/create-sql-vm-portal/azure-sqlvm-security.png)

若要透過網際網路連接到 SQL Server，您也必須啟用下一節所述的「SQL Server 驗證」。

如果您偏好不要啟用透過網際網路連線到 Database Engine 的功能，請選擇下列其中一個選項：

*  只允許從 VM 內連接到 SQL Server。
*  允許從相同虛擬網路中的電腦或服務連接到 SQL Server。

一般情況下，選擇您的案例允許的最嚴格連線能力，即可改善安全性。 但是，所有選項都是透過網路安全性群組進行安全性的 (NSG) 規則和 SQL/Windows 驗證。 您可以在建立 VM 之後編輯 NSG。 如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 安全性考量](security-considerations-best-practices.md)。

### <a name="authentication"></a>驗證

如果您需要 SQL Server Authentication，請在 [ **SQL Server 設定** ] 索引標籤的 [ **SQL 驗證** ] 底下選取 [ **啟用** ]。

![SQL Server 驗證](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> 如果您打算透過網際網路存取 SQL Server () 的公用連線選項，您必須在這裡啟用 SQL 驗證。 SQL Server 的公用存取需要 SQL 驗證。

如果您啟用 [SQL Server 驗證]，請指定 [登入名稱] 和 [密碼]。 此登入名稱會設定為 SQL Server Authentication 登入，以及 **系統管理員（sysadmin** ）固定伺服器角色的成員。 如需驗證模式的詳細資訊，請參閱[選擇驗證模式](/sql/relational-databases/security/choose-an-authentication-mode)。

如果您不想要啟用 SQL Server 驗證，可以使用 VM 上的本機系統管理員帳戶來連接到 SQL Server 實例。

### <a name="azure-key-vault-integration"></a>Azure 金鑰保存庫整合

若要將安全性祕密儲存在 Azure 中以進行加密，請選取 [SQL Server 設定]，向下捲動至 [Azure 金鑰保存庫整合]。 選取 [啟用]，並填入所要求的資訊。 

![Azure 金鑰保存庫整合](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

下表列出設定 Azure Key Vault (AKV) 整合所需的參數。

| 參數 | DESCRIPTION | 範例 |
| --- | --- | --- |
| **金鑰保存庫 URL** |金鑰保存庫的位置。 |`https://contosokeyvault.vault.azure.net/` |
| **主體名稱** |Azure Active Directory 服務主體名稱。 此名稱也稱為「用戶端識別碼」。 |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **主體密碼** |Azure Active Directory 服務主體密碼。 此密碼也稱為「用戶端密碼」。 |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **認證名稱** |**認證名稱** ： AKV 整合會在 SQL Server 中建立認證，並允許 VM 存取金鑰保存庫。 選擇此認證的名稱。 |`mycred1` |

如需詳細資訊，請參閱 [在 Azure VM 上設定 SQL Server 的 Azure 金鑰保存庫整合](azure-key-vault-integration-configure.md)。

### <a name="storage-configuration"></a>儲存體組態

在 [SQL Server 設定] 索引標籤上，於 [儲存體設定] 下方，選取 [變更設定] 來開啟 [效能最佳化儲存體設定] 頁面並指定儲存體要求。

![SQL VM 儲存體設定](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

在 [儲存體最佳化] 底下，選取下列其中一個選項：

*  是預設設定，支援大多數工作負載。
* **交易處理** 會最佳化儲存體以最佳化傳統資料庫 OLTP 工作負載。
*  可將儲存體最佳化來處理分析和報告工作負載。

![SQL VM 儲存體設定](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

您可選擇保留預設值，或手動變更儲存體拓撲以符合 IOPS 需求。 如需詳細資訊，請參閱[儲存體設定](storage-configuration.md)。 

### <a name="sql-server-license"></a>SQL Server 授權

如果您是軟體保證客戶，您可以使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 攜帶您自己的 SQL Server 授權並節省資源。 

![SQL VM 授權](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>自動修補

**Automated patching** 。 自動修補可讓 Azure 自動修補 SQL Server 和作業系統。 請為維護期間指定一週當中的某一天、時間及持續時間。 Azure 會在此維護期間執行修補。 維護時段排程會使用 VM 地區設定。 如果您不想要讓 Azure 自動修補 SQL Server 和作業系統，請選取 [ **停** 用]。  

![SQL VM 自動化修補](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補](automated-patching.md)。

### <a name="automated-backup"></a>自動備份

在 [自動備份] 底下，可以為所有資料庫啟用自動資料庫備份。 預設會停用自動備份。

當您啟用 SQL 自動備份時，您可以進行下列設定：

* 備份的保留期限 (天數)
* 要用於備份的儲存體帳戶
* 備份的加密選項和密碼
* 備份系統資料庫
* 設定備份排程

若要加密備份，請選取 [ **啟用** ]。 然後指定 [密碼] 。 Azure 會建立憑證來加密備份，並使用指定的密碼來保護該憑證。 根據預設，排程會自動進行設定，但是可透過選取 [手動] 來建立手動排程。 

![SQL VM 自動化備份](./media/create-sql-vm-portal/automated-backup.png)

如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的自動化備份](automated-backup-sql-2014.md)。


### <a name="machine-learning-services"></a>機器學習服務

您可選擇啟用[機器學習服務](/sql/advanced-analytics/)。 此選項可讓您在 SQL Server 2017 中搭配使用機器學習與 Python 和 R。 請在 [SQL Server 設定] 視窗上選取 [啟用]。


## <a name="4-review--create"></a>4.檢閱 + 建立

在 [ **審核 + 建立** ] 索引標籤上：
1. 檢閱摘要。
1. 選取 [ **建立** ] 以建立為此 VM 指定的 SQL Server、資源群組和資源。

您可以從 Azure 入口網站監視部署。 畫面頂端的 [通知]  按鈕會顯示基本的部署狀態。

> [!NOTE]
> Azure 部署 SQL Server VM 的時間範例：使用預設設定布建至美國東部區域的測試 SQL Server VM 大約需要12分鐘才能完成。 根據您的區域和選取的設定，您可能會遇到更快速或更慢的部署時間。

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>透過遠端桌面開啟 VM

使用下列步驟，透過遠端桌面通訊協定 (RDP) 連接到 SQL Server 的虛擬機器：

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

連線到 SQL Server 虛擬機器之後，您可以啟動 SQL Server Management Studio，然後使用您的本機系統管理員認證透過「Windows 驗證」進行連線。 如果您已啟用 SQL Server 驗證，您也可以使用您在佈建期間所設定的 SQL 登入和密碼以 SQL 驗證連線。

存取電腦可讓您根據您的需求直接變更電腦和 SQL Server 設定。 例如，您可以設定防火牆設定或變更 SQL Server 組態設定。

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>遠端連線到 SQL Server

在本逐步介紹中，您選取了虛擬機器的 [公用] 存取權和 [SQL Server 驗證]。 這些設定會自動設定虛擬機器，以允許透過網際網路來自任何用戶端的 SQL Server 連線 (假設它們有正確的 SQL 登入)。

> [!NOTE]
> 如果您未在佈建期間選取 [公用]，您可以在佈建之後，透過入口網站變更 SQL 連線設定。 如需詳細資訊，請參閱[變更 SQL 連線設定](ways-to-connect-to-sql.md#change)。

下列各節說明如何透過網際網路連線到您的 SQL Server VM 執行個體。

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > 這個範例會使用通用連接埠 1433。 但是，如果在部署 SQL Server VM 的期間指定了不同的連接埠 (例如 1401)，則需要變更此值。 


## <a name="next-steps"></a>後續步驟

如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) 和[常見問題集](frequently-asked-questions-faq.md)。