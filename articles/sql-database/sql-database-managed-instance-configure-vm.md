---
title: 連接用戶端 VM-受控實例
description: 使用 Azure 虛擬機器中的 SQL Server Management Studio 連線到 Azure SQL Database 受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 8b5dce0b43fac7cfd0e974f26451338ca1541f8f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80528410"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>快速入門：設定 Azure VM 以連線到 Azure SQL Database 受控執行個體

此快速入門示範了如何設定 Azure 虛擬機器，以使用 SQL Server Management Studio (SSMS) 連線到 Azure SQL Database 受控執行個體。 如需如何使用點對站連線從內部部署用戶端電腦進行連線的快速入門，請參閱[設定點對站連線](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>先決條件

此快速入門會使用在[建立受控執行個體](sql-database-managed-instance-get-started.md)中建立的資源作為起點。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>在受控執行個體 VNet 中建立新的子網路

下列步驟可在受控執行個體 VNet 中建立新的子網路，以便 Azure 虛擬機器可以連線到受控執行個體。 受控執行個體子網路專門用於受控執行個體。 您無法在該子網路中建立 Azure 虛擬機器等其他資源。

1. 您在[建立受控執行個體](sql-database-managed-instance-get-started.md)快速入門中建立過受控執行個體的資源群組，請開啟該資源群組。 選取受控執行個體的虛擬網路。

   ![受控執行個體資源](./media/sql-database-managed-instance-configure-vm/resources.png)

2. 選取 [子網路]****，然後選取 [+ 子網路]**** 來建立新的子網路。

   ![受控執行個體子網路](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. 使用此資料表中的資訊填妥表單：

   | 設定| 建議的值 | 描述 |
   | ---------------- | ----------------- | ----------- |
   | **名稱** | 任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。|
   | **位址範圍 (CIDR 區塊)** | 有效範圍 | 針對此快速入門，使用預設值即可。|
   | **網路安全性群組** | 無 | 針對此快速入門，使用預設值即可。|
   | **路由表** | 無 | 針對此快速入門，使用預設值即可。|
   | **服務端點** | 已選取 0 | 針對此快速入門，使用預設值即可。|
   | **子網路委派** | 無 | 針對此快速入門，使用預設值即可。|

   ![為用戶端 VM 新增受控執行個體子網路](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. 選取 [確定]**** 以在受控執行個體 VNet 中建立這個額外的子網路。

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>在 VNet 的新子網路中建立虛擬機器

下列步驟顯示如何在建立受控執行個體的相同 VNet 中建立虛擬機器。

## <a name="prepare-the-azure-virtual-machine"></a>準備 Azure 虛擬機器

由於 SQL 受控執行個體是放置在您的私人虛擬網路中，因此您必須使用已安裝的 SQL 用戶端工具 (例如 SQL Server Management Studio 或 Azure Data Studio) 建立 Azure VM。 此工具可讓您連線到受控執行個體，並執行查詢。 此快速入門使用 SQL Server Management Studio。

要使用所有的必要工具建立用戶端虛擬機器，最簡單的方式是使用 Azure Resource Manager 範本。

1. 請確定您已在另一個瀏覽器索引標籤中登入 Azure 入口網站。然後，選取下列按鈕以建立用戶端虛擬機器，並安裝 SQL Server Management Studio：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. 使用下表中的資訊填妥表單：

   | 設定| 建議的值 | 描述 |
   | ---------------- | ----------------- | ----------- |
   | **訂用帳戶** | 有效的訂用帳戶 | 必須是您有權限在其中建立新資源的訂用帳戶。 |
   | **資源群組** |您在[建立受控執行個體](sql-database-managed-instance-get-started.md)快速入門中指定的資源群組。|此資源群組必須是 VNet 所在的資源群組。|
   | **位置** | 資源群組的位置 | 根據選取的資源群組填入此值。 |
   | **虛擬機器名稱**  | 任何有效名稱 | 如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。|
   |**管理員使用者名稱**|任何有效的使用者名稱|如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 請勿使用 "serveradmin"，因為這是保留的伺服器層級角色。<br>您隨時要[連線至 VM](#connect-to-virtual-machine) 時，都可以使用這個使用者名稱。|
   |**密碼**|任何有效密碼|密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。<br>您隨時要[連線至 VM](#connect-to-virtual-machine) 時，都可以使用這個密碼。|
   | **虛擬機器大小** | 任何有效大小 | 在此快速入門中，使用此範本的預設值 **Standard_B2s** 就已足夠。 |
   | **位置**|[resourceGroup().location]。| 請勿變更此值。 |
   | **虛擬網路名稱**|您在其中建立受控執行個體的虛擬網路。|
   | **子網路名稱**|您在上一個程序中建立的子網路名稱| 請勿選擇您在其中建立受控執行個體的子網路。|
   | **成品位置** | [deployment().properties.templateLink.uri] | 請勿變更此值。 |
   | **構件位置 Sas 權杖** | 保留空白 | 請勿變更此值。 |

   ![建立用戶端 VM](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   如果您使用[建立受控執行個體](sql-database-managed-instance-get-started.md)中建議的 VNet 名稱與預設子網路，則不需要變更最後兩個參數。 否則，您應該將這些值變更為您在設定網路環境時輸入的值。

3. 選取 [我同意上方所述的條款及條件]**** 核取方塊。
4. 選取 [購買]**** 以在您的網路中部署 Azure VM。
5. 選取 [通知]**** 圖示以檢視部署的狀態。

> [!IMPORTANT]
> 在虛擬機器建立大約 15 分鐘之前請不要繼續，讓建立後指令碼有時間可以安裝 SQL Server Management Studio。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

下列步驟顯示如何使用遠端桌面連線來連線到新建立的虛擬機器。

1. 部署完成之後，請移至虛擬機器資源。

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. 選取 [連線]  。

   將顯示遠端桌面通訊協定檔案 (.rdp 檔案) 表單，其中包含虛擬機器的公用 IP 位址與連接埠號碼。

   ![RDP 表單](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. 選取 [下載 RDP 檔案]  。

   > [!NOTE]
   > 您也可以使用 SSH 來連線到您的 VM。

4. 關閉**連線至虛擬機器**表單。
5. 若要連線至您的 VM，請開啟下載的 RDP 檔案。
6. 出現提示時，請選取 [連接]****。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)。

7. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後選擇 [確定]****。

8. 您可能會在登入過程中收到憑證警告。 選擇 [是]**** 或 [繼續]**** 以繼續進行連線。

您已連線到 [伺服器管理員] 儀表板中的虛擬機器。

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>使用 SSMS 連線到受控執行個體

1. 在虛擬機器中，開啟 SQL Server Management Studio (SSMS)。

   由於這是第一次啟動 SSMS，因此需要一些時間才能完成其設定。
2. 在 [連接到伺服器]**** 對話方塊方塊中，於 [伺服器名稱]**** 方塊中輸入受控執行個體的完整 [主機名稱]****。 選取 [SQL Server 驗證]****，提供您的使用者名稱和密碼，然後選取 [連線]****。

    ![SSMS 連線](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

連線之後，您可以檢視 [資料庫] 節點中的系統和使用者資料庫，以及 [安全性]、[伺服器物件]、[複寫]、[管理]、[SQL Server 代理程式] 和 [XEvent Profiler] 節點中的各種物件。

## <a name="next-steps"></a>後續步驟

- 如需顯示如何使用點對站連線從內部部署用戶端電腦連線的快速入門，請參閱[設定點對站](sql-database-managed-instance-configure-p2s.md)連線。
- 如需有關應用程式連線選項的概觀，請參閱[將您的應用程式連線至受控執行個體](sql-database-managed-instance-connect-app.md)。
- 若要將現有 SQL Server 資料庫從內部部署還原到受控執行個體，您可以使用 [Azure 資料庫移轉服務 (DMS) 來進行移轉](../dms/tutorial-sql-server-to-managed-instance.md)，或使用 [T-SQL RESTORE 命令](sql-database-managed-instance-get-started-restore.md)從資料庫備份檔案還原。
