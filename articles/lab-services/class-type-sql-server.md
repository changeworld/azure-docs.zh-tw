---
title: 設定實驗室以使用 Azure SQL Database 進行管理和開發 |Azure 實驗室服務
description: 瞭解如何設定實驗室，以使用 Azure SQL Database 進行管理和開發。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9fc0a965869207ba8d1b4eb6f45e878ae4b93c3a
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079017"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>設定實驗室以使用 SQL Server 管理和開發

本文說明如何在 Azure 實驗室服務中設定基本 SQL Server 管理和開發類別的實驗室。  資料庫概念是在大學的大部分電腦科學部門所教授的其中一個簡介課程。 結構化查詢語言 (SQL)  (SQL) 是國際標準。  SQL 是關聯資料庫管理的標準語言，包括新增、存取和管理資料庫中的內容。  最值得注意的是，其快速處理、經過證實的可靠性、便利性和使用彈性。

在本文中，我們將示範如何在實驗室中設定具有[Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)和[Azure Data Studio](https://github.com/microsoft/azuredatastudio)的虛擬機器範本。  在此實驗室中，我們將在整個實驗室中使用一個共用的[SQL Server 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)。 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)是 Azure 提供的平臺即服務 (PaaS) 資料庫引擎供應專案。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶，才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱[設定實驗室帳戶的教學](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)課程。 您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

針對實驗室帳戶啟用下表所述的設定。 如需如何啟用 marketplace 映射的詳細資訊，請參閱[指定可供實驗室建立者使用的 marketplace 映射](specify-marketplace-images.md)。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
| Marketplace 映像 | 啟用「Windows 10 企業版 N (x64) 」映射上的「Visual Studio 2019 社區 (最新版本) ，以在您的實驗室帳戶中使用。 |

### <a name="shared-resource-configuration"></a>共用資源設定

若要在實驗室服務中使用共用資源，您必須先建立虛擬網路和資源本身。  若要建立虛擬網路並將其連線至實驗室，請遵循[如何在 Azure 實驗室服務中使用共用資源來建立實驗室](how-to-create-a-lab-with-shared-resource.md)。  請記住，實驗室服務外部的任何資源都會分開計費，而且不會包含在實驗室成本預估中。

>[!WARNING]
>在建立實驗室之前，應該先設定實驗室的共用資源。  如果在建立實驗室*之前*，未將 vnet[對等互連至實驗室帳戶](how-to-connect-peer-virtual-network.md)，實驗室將無法存取共用資源。

既然已經處理了網路功能的一面，讓我們建立一個 SQL Server 資料庫。  我們將建立[單一資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)，因為它是 Azure SQL Database 的最快速部署選項。  如需其他部署選項，請建立[彈性集](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)區、[受控實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)或[SQL 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/sql/quickstart-sql-vm-create-portal)。

1. 從 [Azure 入口網站] 功能表中，選擇 [**建立新資源**]。
2. 選擇**SQL Database** ，然後按一下 [**建立**] 按鈕。
3. 在 [**建立 SQL database** ] 表單的 [**基本**] 索引標籤上，選取資料庫的資源群組。  我們將使用*sqldb-rg*。
4. 在 [**資料庫名稱**] 中，輸入*classlabdb*。
5. 在**伺服器**設定底下，按一下 [**建立新**的] 以建立新的伺服器來保存資料庫。
6. 在 [**新增伺服器**] 飛出視窗中，輸入伺服器名稱。  我們將使用*classlabdbserver*。  伺服器名稱必須是全域唯一的。
7. 在 [**伺服器管理員登**入] 中輸入*azureuser* 。
8. 輸入易記的密碼。  密碼長度至少必須為八個字元，且包含特殊字元。
9. 選擇**位置**的 [區域]。  可能的話，請輸入與實驗室帳戶相同的位置，並對等互連 vnet 以將延遲降至最低。
10. 按一下 **[確定]** 以返回 [**建立 SQL Database** ] 表單。
11. 按一下 [**計算 + 儲存體**] 設定下的 [**設定資料庫**] 連結。
12. 視需要修改類別的資料庫設定。  您可以選擇 [已布建] 和 [無伺服器] 選項。  在此範例中，我們將使用自動縮放無伺服器選項，最大虛擬核心為4，min 虛擬核心為1。 我們會將自動暫停設定保持在最少1小時。 按一下 [套用]。
13. 按 **[下一步：網路]** 按鈕。
14. 在 [網路功能] 索引標籤上，針對 [連線**方法**] 選擇 [私人端點]。
15. 在 [**私人端點**] 區段下，按一下 [**新增私人端點**]。
16. 在 [**建立私人端點**] 飛出視窗中，選擇與您的虛擬網路對等互連至實驗室帳戶相同的資源群組。
17. 針對 [**位置**]，選擇與虛擬網路相同的位置。
18. 針對 [**名稱**]，輸入*labsql-endpt*。
19. 將 [目標 subresource] 設定為 [SqlServer]。
20. 針對 [**虛擬網路**]，選擇與實驗室帳戶相同的 [虛擬網路] 對等互連。
21. 針對 [**子網**]，選擇您想要主控端點的子網。  指派給端點的 IP 會來自指派給該子網的範圍。
22. 將 [**與私人 DNS 整合**] 設定為 [**否**]。 為了簡單起見，我們將使用 Azure 的 DNS，而不是私人 DNS 區域或我們自己的 DNS 伺服器。
23. 按一下 [確定]。
24. 按一下 **[下一步**其他設定]。
25. 針對 [**使用現有的資料**] 設定，選擇 [**範例**]。  建立資料庫時，將會使用來自 AdventureWorksLT 資料庫的資料。
26. 按一下 [檢閱 + 建立]。
27. 按一下頁面底部的 [新增] 。

一旦 SQL Database 部署成功完成，我們就可以在實驗室範本機器上建立實驗室並安裝軟體。

### <a name="lab-settings"></a>實驗室設定

設定教室實驗室時，請使用下表中的設定。 如需如何建立教室實驗室的詳細資訊，請參閱[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
| 虛擬機器大小 | 中。 此大小最適合用於關聯式資料庫、記憶體內部快取及分析。 |
| 虛擬機器映像 | Visual Studio 2019 社區 (Windows 10 企業版 N 的最新發行)  (x64)  |

現在我們已建立實驗室，讓我們以所需的軟體修改範本機器。

## <a name="visual-studio"></a>Visual Studio

上面選擇的影像包含[Visual Studio 2019 的群體](https://visualstudio.microsoft.com/vs/community/)。  映射上已安裝所有工作負載和工具集。  使用 [Visual Studio 安裝程式安裝您想要的[任何選用工具](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019)。  登[入 Visual Studio](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio)以解除鎖定「社區」版本。

Visual Studio 包括**資料儲存和處理**工具組，其中包括 SQL SERVER DATA TOOLS (SSDT) 。  如需有關 SSDT 功能的詳細資訊，請參閱[SQL Server Data Tools 總覽](https://docs.microsoft.com/sql/ssdt/sql-server-data-tools?view=sql-server-ver15)。  若要確認與類別共用 SQL Server 的連接將會成功，請參閱[連接到資料庫和流覽現有的物件](https://docs.microsoft.com/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15)。 如果出現提示，請將範本機器 IP 新增至可連線到您 SQL Server 實例的[允許電腦清單](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)。

Visual Studio 支援數個工作負載，包括**Web & 雲端**和**桌面 &** 行動工作負載。  這兩個工作負載都支援 SQL Server 做為資料來源。 如需使用 ASP.NET Core SQL Server 的詳細資訊，請參閱 Azure App Service 教學課程[中的建立 ASP.NET Core 和 SQL Database 應用程式](https://docs.microsoft.com/azure/app-service/tutorial-dotnetcore-sqldb-app)。  使用[SqlClient](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)程式庫從[Xamarin](https://docs.microsoft.com/xamarin)應用程式連線到 SQL Database。

## <a name="install-azure-data-studio"></a>安裝 Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio)是一種多資料庫、跨平臺的桌面環境，適用于使用 Windows、MacOS 和 Linux 上的內部部署和雲端資料平臺系列的資料專業人員。

1. 下載[適用于 Windows 的 Azure Data Studio*系統*安裝程式](https://go.microsoft.com/fwlink/?linkid=2127432)。 若要尋找其他支援作業系統的安裝程式，請移至[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)下載頁面。
2. 在 [**授權合約**] 頁面上，選取 [**我接受合約**]。 按一下 [下一步] 。
3. 在 [選取目的地位置] 頁面上，按一下 [下一步]。
4. 在 [選取 [開始] 功能表資料夾] 頁面上，按一下 [下一步]。
5. 如果您想要桌面圖示，請在 [**選取其他**工作] 頁面上，核取 [**建立桌面圖示**]。  按一下 [下一步] 。
6. 在 [**準備安裝**] 上，按 **[下一步]**。
7. 等候安裝程式執行。  按一下 [完成] 。

現在，我們已安裝 Azure Data Studio，讓我們設定 Azure SQL Database 的連接。

1. 在 Azure Data Studio 的 [**歡迎使用**] 頁面上，按一下 [**新增連接**] 連結。
2. 在 [連線**詳細資料**] 方塊中，填入所需的資訊。
    - 將**伺服器**設定為*classlabdbserver.database.windows.net*
    - 將**使用者**名稱設定為*azureuser*
    - 將 [**密碼**] 設定為用來建立資料庫的密碼。
    - 勾選 [**記住密碼**]。
    - 針對 [**資料庫**]，選取 [ *classlabdb*]。
3. 按一下 [ **連接**]。

## <a name="install-sql-server-management-studio"></a>安裝 SQL Server Management Studio

[SQL Server Management Studio (SSMS) ](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)是用於管理任何 SQL 基礎結構的整合式環境。  SSMS 是資料庫管理員用來部署、監視和升級資料基礎結構的工具。

1. [下載 Sql Server Management Studio](https://aka.ms/ssmsfullsetup)。 下載完成後，啟動安裝程式。
2. 在 [**歡迎使用**] 頁面上，按一下 [**安裝**]。
3. 在 [**安裝完成**] 頁面上，按一下 [**關閉**]。
4. 啟動 Sql Server Management Studio。  
5. 在 [相依性設定**流程**] 頁面上，按一下 [**關閉**]。

未安裝 SSMS，您可以[連接並查詢 SQL Server](https://docs.microsoft.com/sql/ssms/tutorials/connect-query-sql-server)。 設定連接時，請使用下列值：

- 伺服器類型：資料庫引擎
- 伺服器名稱： *classlabdbserver.database.windows.net*
- 驗證： SQL Server 驗證
- 登入： *azureuser*
- Password：用來建立資料庫的密碼。

## <a name="cost-estimate"></a>成本預估

讓我們來討論這個類別的可能成本預估。 估計不包含執行 SQL Server 的成本。  如需有關資料庫定價的目前詳細資料，請參閱[SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database)。

我們將使用25名學生的課程。 已排程的類別時間有20小時。 此外，每個學生在排程的類別時間以外，會取得家庭或指派10小時的配額。 我們選擇的虛擬機器大小為「中」，也就是42實驗室單位。

以下是此類別的可能成本預估範例：

25名學生 \* (20 個排程時數 \+) * 0.42 美元/小時 = 315.00 美元

>[!IMPORTANT]
>成本預估僅供範例之用。 如需定價的目前詳細資料，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立、管理及發佈範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
