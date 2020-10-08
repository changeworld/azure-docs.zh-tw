---
title: 設定 Azure VM 連線
titleSuffix: Azure SQL Managed Instance
description: 使用 SQL Server Management Studio 從 Azure 虛擬機器連線至 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91620251"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>快速入門：設定 Azure VM 以連線到 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

此快速入門說明如何設定 Azure 虛擬機器，以使用 SQL Server Management Studio (SSMS) 連線至 Azure SQL 受控執行個體。 


如需如何改以點對站連線從內部部署用戶端電腦進行連線的快速入門，請參閱[設定點對站連線](point-to-site-p2s-configure.md)。

## <a name="prerequisites"></a>必要條件

此快速入門會使用在[建立受控執行個體](instance-create-quickstart.md)中建立的資源作為起點。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-new-subnet-vnet"></a>建立新的子網路 VNet

下列步驟會在 SQL 受控執行個體 VNet 中建立新的子網路，讓 Azure 虛擬機器可以連線至受控執行個體。 SQL 受控執行個體子網路會供受控執行個體專門。 您無法在該子網路中建立 Azure 虛擬機器等其他資源。

1. 開啟您在[建立受控執行個體](instance-create-quickstart.md)快速入門中建立之受控執行個體的資源群組。 選取受控執行個體的虛擬網路。

   ![SQL 受控執行個體資源](./media/connect-vm-instance-configure/resources.png)

2. 選取 [子網路]****，然後選取 [+ 子網路]**** 來建立新的子網路。

   ![SQL 受控執行個體子網路](./media/connect-vm-instance-configure/subnets.png)

3. 使用此資料表中的資訊填妥表單：

   | 設定| 建議的值 | 描述 |
   | ---------------- | ----------------- | ----------- |
   | **名稱** | 任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。|
   | **位址範圍 (CIDR 區塊)** | 有效範圍 | 針對此快速入門，使用預設值即可。|
   | **網路安全性群組** | 無 | 針對此快速入門，使用預設值即可。|
   | **路由表** | 無 | 針對此快速入門，使用預設值即可。|
   | **服務端點** | 0 (已選取) | 針對此快速入門，使用預設值即可。|
   | **子網路委派** | 無 | 針對此快速入門，使用預設值即可。|

   ![為用戶端 VM 新增 SQL 受控執行個體子網路](./media/connect-vm-instance-configure/new-subnet.png)

4. 選取 [確定]，以在 SQL 受控執行個體 VNet 中建立這個額外的子網路。

## <a name="create-a-vm-in-the-new-subnet"></a>在新的子網路中建立 VM 

下列步驟說明如何在新的子網路中建立虛擬機器，以連線至 SQL 受控執行個體。

## <a name="prepare-the-azure-virtual-machine"></a>準備 Azure 虛擬機器

由於 SQL 受控執行個體是放置在您的私人虛擬網路中，因此您必須使用已安裝的 SQL 用戶端工具 (例如 SQL Server Management Studio 或 Azure Data Studio) 建立 Azure VM。 此工具可讓您連線至 SQL 受控執行個體，並執行查詢。 此快速入門使用 SQL Server Management Studio。

要使用所有的必要工具建立用戶端虛擬機器，最簡單的方式是使用 Azure Resource Manager 範本。

1. 請務必在另一個瀏覽器分頁中登入 Azure 入口網站。接著請選取下列按鈕以建立用戶端虛擬機器，並安裝 SQL Server Management Studio：

   [![顯示標示為「部署至 Azure」的按鈕影像。](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. 使用下表中的資訊填妥表單：

   | 設定| 建議的值 | 描述 |
   | ---------------- | ----------------- | ----------- |
   | **訂用帳戶** | 有效的訂用帳戶 | 必須是您有權限在其中建立新資源的訂用帳戶。 |
   | **資源群組** |您在[建立 SQL 受控執行個體](instance-create-quickstart.md)快速入門中指定的資源群組|此資源群組必須是 VNet 所在的資源群組。|
   | **位置** | 資源群組的位置 | 根據選取的資源群組填入此值。 |
   | **虛擬機器名稱**  | 任何有效名稱 | 如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。|
   |**管理員使用者名稱**|任何有效的使用者名稱|如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 請勿使用 "serveradmin"，因為這是保留的伺服器層級角色。<br>您隨時要[連線至 VM](#connect-to-the-virtual-machine) 時，都可以使用這個使用者名稱。|
   |**密碼**|任何有效密碼|密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。<br>您隨時要[連線至 VM](#connect-to-the-virtual-machine) 時，都可以使用這個密碼。|
   | **虛擬機器大小** | 任何有效大小 | 在此快速入門中，使用此範本的預設值 **Standard_B2s** 就已足夠。 |
   | **位置**|[resourceGroup().location]。| 請勿變更此值。 |
   | **虛擬網路名稱**|您在其中建立受控執行個體的虛擬網路|
   | **子網路名稱**|您在上一個程序中建立的子網路名稱| 請勿選擇您在其中建立受控執行個體的子網路。|
   | **成品位置** | [deployment().properties.templateLink.uri] | 請勿變更此值。 |
   | **構件位置 SAS 權杖** | 保留空白 | 請勿變更此值。 |

   ![建立用戶端 VM](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   如果您使用[建立 SQL 受控執行個體](instance-create-quickstart.md)中建議的 VNet 名稱與預設子網路，則不需要變更最後兩個參數。 否則，您應該將這些值變更為您在設定網路環境時輸入的值。

3. 選取 [我同意上方所述的條款及條件]**** 核取方塊。
4. 選取 [購買]**** 以在您的網路中部署 Azure VM。
5. 選取 [通知]**** 圖示以檢視部署的狀態。

> [!IMPORTANT]
> 在虛擬機器建立大約 15 分鐘之前請不要繼續，讓建立後指令碼有時間可以安裝 SQL Server Management Studio。

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

下列步驟顯示如何使用遠端桌面連線來連線至新建立的虛擬機器。

1. 部署完成之後，請移至虛擬機器資源。

    ![此螢幕擷取畫面顯示已選取虛擬機器的 [概觀] 頁面，並反白顯示 [連線] 的 Azure 入口網站。](./media/connect-vm-instance-configure/vm.png)  

2. 選取 [連接]。

   將顯示遠端桌面通訊協定檔案 (.rdp 檔案) 表單，其中包含虛擬機器的公用 IP 位址與連接埠號碼。

   ![RDP 表單](./media/connect-vm-instance-configure/rdp.png)  

3. 選取 [下載 RDP 檔案]。

   > [!NOTE]
   > 您也可以使用 SSH 來連線到您的 VM。

4. 關閉**連線至虛擬機器**表單。
5. 若要連線至您的 VM，請開啟下載的 RDP 檔案。
6. 出現提示時，請選取 [連接]****。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)。

7. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後選擇 [確定]。

8. 您可能會在登入過程中收到憑證警告。 選擇 [是]**** 或 [繼續]**** 以繼續進行連線。

您已連線到 [伺服器管理員] 儀表板中的虛擬機器。

## <a name="connect-to-sql-managed-instance"></a>連線到 SQL 受控執行個體 

1. 在虛擬機器中，開啟 SQL Server Management Studio。

   由於這是第一次啟動 SSMS，因此需要一些時間才能完成其設定。
2. 在 [連線到伺服器] 對話方塊中，於 [伺服器名稱] 方塊中輸入受控執行個體的完整**主機名稱**。 選取 [SQL Server 驗證]****，提供您的使用者名稱和密碼，然後選取 [連線]****。

    ![SSMS 連線](./media/connect-vm-instance-configure/ssms-connect.png)  

連線之後，您可以檢視 [資料庫] 節點中的系統和使用者資料庫，以及 [安全性]、[伺服器物件]、[複寫]、[管理]、[SQL Server 代理程式] 和 [XEvent Profiler] 節點中的各種物件。

## <a name="next-steps"></a>後續步驟

- 如需如何使用點對站連線從內部部署用戶端電腦進行連線的快速入門，請參閱[設定點對站連線](point-to-site-p2s-configure.md)。
- 如需有關應用程式連線選項的概觀，請參閱[將您的應用程式連線至 SQL 受控執行個體](connect-application-instance.md)。
- 若要將現有 SQL Server 資料庫從內部部署還原到受控執行個體，您可以使用 [Azure 資料庫移轉服務進行移轉](../../dms/tutorial-sql-server-to-managed-instance.md)，或使用 [T-SQL RESTORE 命令](restore-sample-database-quickstart.md)從資料庫備份檔案還原。
