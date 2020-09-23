---
title: 設定 Azure VM 連線能力
titleSuffix: Azure SQL Managed Instance
description: 使用 Azure 虛擬機器中的 SQL Server Management Studio 連接到 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 72d5a722e6c1e6b936c6ec0386036317e887bb92
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969827"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>快速入門：設定 Azure VM 以連接到 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本快速入門說明如何使用 SQL Server Management Studio (SSMS) ，設定 Azure 虛擬機器以連接到 Azure SQL 受控執行個體。 


如需顯示如何改為使用點對站連線從內部部署用戶端電腦連線的快速入門，請參閱 [設定點對站](point-to-site-p2s-configure.md)連線。

## <a name="prerequisites"></a>必要條件

本快速入門會使用建立 [受控實例](instance-create-quickstart.md) 中建立的資源作為起點。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-new-subnet-vnet"></a>建立新的子網 VNet

下列步驟會在 SQL 受控執行個體 VNet 中建立新的子網，讓 Azure 虛擬機器可以連線到受控實例。 SQL 受控執行個體子網專門用於受控實例。 您無法在該子網路中建立 Azure 虛擬機器等其他資源。

1. 開啟您在 [建立受控實例](instance-create-quickstart.md) 快速入門中建立之受控實例的資源群組。 選取受控實例的虛擬網路。

   ![SQL 受控執行個體資源](./media/connect-vm-instance-configure/resources.png)

2. 選取 [子網路]****，然後選取 [+ 子網路]**** 來建立新的子網路。

   ![SQL 受控執行個體子網](./media/connect-vm-instance-configure/subnets.png)

3. 使用此資料表中的資訊填妥表單：

   | 設定| 建議的值 | 描述 |
   | ---------------- | ----------------- | ----------- |
   | **名稱** | 任何有效名稱|如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。|
   | **位址範圍 (CIDR 區塊)** | 有效範圍 | 針對此快速入門，使用預設值即可。|
   | **網路安全性群組** | 無 | 針對此快速入門，使用預設值即可。|
   | **路由表** | 無 | 針對此快速入門，使用預設值即可。|
   | **服務端點** | 0 (已選取) | 針對此快速入門，使用預設值即可。|
   | **子網路委派** | 無 | 針對此快速入門，使用預設值即可。|

   ![用戶端 VM 的新 SQL 受控執行個體子網](./media/connect-vm-instance-configure/new-subnet.png)

4. 選取 **[確定]** ，在 SQL 受控執行個體 VNet 中建立這個額外的子網。

## <a name="create-a-vm-in-the-new-subnet"></a>在新的子網中建立 VM 

下列步驟說明如何在新的子網中建立虛擬機器，以連接到 SQL 受控執行個體。

## <a name="prepare-the-azure-virtual-machine"></a>準備 Azure 虛擬機器

由於 SQL 受控執行個體放在您的私人虛擬網路中，因此您必須使用已安裝的 SQL 用戶端工具（例如 SQL Server Management Studio 或 Azure Data Studio）來建立 Azure VM。 此工具可讓您連接到 SQL 受控執行個體並執行查詢。 此快速入門使用 SQL Server Management Studio。

要使用所有的必要工具建立用戶端虛擬機器，最簡單的方式是使用 Azure Resource Manager 範本。

1. 請確定您已在另一個瀏覽器索引標籤中登入 Azure 入口網站。然後，選取下列按鈕以建立用戶端虛擬機器，並安裝 SQL Server Management Studio：

   [![顯示標示為「部署至 Azure」的按鈕影像。](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. 使用下表中的資訊填妥表單：

   | 設定| 建議的值 | 描述 |
   | ---------------- | ----------------- | ----------- |
   | **訂用帳戶** | 有效的訂用帳戶 | 必須是您有權限在其中建立新資源的訂用帳戶。 |
   | **資源群組** |您在 [建立 SQL 受控執行個體](instance-create-quickstart.md) 快速入門中所指定的資源群組|此資源群組必須是 VNet 所在的資源群組。|
   | **位置** | 資源群組的位置 | 根據選取的資源群組填入此值。 |
   | **虛擬機器名稱**  | 任何有效名稱 | 如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。|
   |**系統管理員使用者名稱**|任何有效的使用者名稱|如需有效的名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 請勿使用 "serveradmin"，因為這是保留的伺服器層級角色。<br>您隨時要[連線至 VM](#connect-to-the-virtual-machine) 時，都可以使用這個使用者名稱。|
   |**密碼**|任何有效密碼|密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。<br>您隨時要[連線至 VM](#connect-to-the-virtual-machine) 時，都可以使用這個密碼。|
   | **虛擬機器大小** | 任何有效大小 | 在此快速入門中，使用此範本的預設值 **Standard_B2s** 就已足夠。 |
   | **位置**|[resourceGroup().location]。| 請勿變更此值。 |
   | **虛擬網路名稱**|您在其中建立受控實例的虛擬網路|
   | **子網路名稱**|您在上一個程序中建立的子網路名稱| 請勿選擇您在其中建立受控實例的子網。|
   | **成品位置** | [deployment().properties.templateLink.uri] | 請勿變更此值。 |
   | **構件位置 Sas 權杖** | 保留空白 | 請勿變更此值。 |

   ![建立用戶端 VM](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   如果您在 [建立 SQL 受控執行個體](instance-create-quickstart.md)中使用建議的 VNet 名稱和預設子網，則不需要變更最後兩個參數。 否則，您應該將這些值變更為您在設定網路環境時輸入的值。

3. 選取 [我同意上方所述的條款及條件]**** 核取方塊。
4. 選取 [購買]**** 以在您的網路中部署 Azure VM。
5. 選取 [通知]**** 圖示以檢視部署的狀態。

> [!IMPORTANT]
> 在虛擬機器建立大約 15 分鐘之前請不要繼續，讓建立後指令碼有時間可以安裝 SQL Server Management Studio。

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

下列步驟說明如何使用遠端桌面連線來連接到新建立的虛擬機器。

1. 部署完成之後，請移至虛擬機器資源。

    ![螢幕擷取畫面顯示已選取的虛擬機器 [總覽] 頁面的 Azure 入口網站，並醒目提示 [連線]。](./media/connect-vm-instance-configure/vm.png)  

2. 選取 [連接]。

   將顯示遠端桌面通訊協定檔案 (.rdp 檔案) 表單，其中包含虛擬機器的公用 IP 位址與連接埠號碼。

   ![RDP 表單](./media/connect-vm-instance-configure/rdp.png)  

3. 選取 [下載 RDP 檔案]。

   > [!NOTE]
   > 您也可以使用 SSH 來連線到您的 VM。

4. 關閉**連線至虛擬機器**表單。
5. 若要連線至您的 VM，請開啟下載的 RDP 檔案。
6. 出現提示時，請選取 [連接]****。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的 [遠端桌面用戶端](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) 。

7. 輸入您在建立虛擬機器時指定的使用者名稱和密碼，然後選擇 **[確定]**。

8. 您可能會在登入過程中收到憑證警告。 選擇 [是]**** 或 [繼續]**** 以繼續進行連線。

您已連線到 [伺服器管理員] 儀表板中的虛擬機器。

## <a name="connect-to-sql-managed-instance"></a>連線到 SQL 受控執行個體 

1. 在虛擬機器中，開啟 SQL Server Management Studio。

   這需要幾分鐘的時間才能開啟，因為它需要完成其設定，因為這是第一次啟動 SSMS。
2. 在 [**連接到伺服器**] 對話方塊中，于 [**伺服器名稱**] 方塊中輸入受控實例的完整**主機名稱**。 選取 [SQL Server 驗證]****，提供您的使用者名稱和密碼，然後選取 [連線]****。

    ![SSMS 連接](./media/connect-vm-instance-configure/ssms-connect.png)  

連線之後，您可以檢視 [資料庫] 節點中的系統和使用者資料庫，以及 [安全性]、[伺服器物件]、[複寫]、[管理]、[SQL Server 代理程式] 和 [XEvent Profiler] 節點中的各種物件。

## <a name="next-steps"></a>下一步

- 如需示範如何使用點對站連線從內部部署用戶端電腦進行連線的快速入門，請參閱 [設定點對站](point-to-site-p2s-configure.md)連線。
- 如需有關應用程式連線選項的概觀，請參閱[將您的應用程式連線至 SQL 受控執行個體](connect-application-instance.md)。
- 若要將現有的 SQL Server 資料庫從內部部署還原到受控實例，您可以使用 [Azure 資料庫移轉服務來進行遷移](../../dms/tutorial-sql-server-to-managed-instance.md) ，或使用 [t-sql restore 命令](restore-sample-database-quickstart.md) 從資料庫備份檔案還原。
