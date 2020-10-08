---
title: 使用 SSMS 設定點對站連線
titleSuffix: Azure SQL Managed Instance
description: 使用 SQL Server Management Studio (SSMS)，利用點對站連線從內部部署用戶端電腦連線至 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 1bcaaed394d8e802a9660e2fdf0e37994ee795a7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91617684"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>快速入門：設定從內部部署到 Azure SQL 受控執行個體的點對站連線
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

此快速入門示範如何使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)，利用點對站連線從內部部署用戶端電腦連線至 Azure SQL 受控執行個體。 如需點對站連線的詳細資訊，請參閱[關於點對站 VPN](../../vpn-gateway/point-to-site-about.md)。

## <a name="prerequisites"></a>必要條件

此快速入門：

- 使用在[建立受控執行個體](instance-create-quickstart.md)中建立的資源作為起點。
- 您的內部部署用戶端電腦上需要 PowerShell 5.1 與 Azure PowerShell 1.4.0 或更新版本。 如有需要，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)的指示。
- 您的內部部署用戶端電腦上需要最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)。

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>將 VPN 閘道連結至虛擬網路

1. 在您的內部部署用戶端電腦上開啟 PowerShell。

2. 複製此 PowerShell 指令碼。 此指令碼會將 VPN 閘道連結到您在[建立受控執行個體](instance-create-quickstart.md)快速入門中建立的 SQL 受控執行個體虛擬網路。 此指令碼會使用 Azure PowerShell Az 模組，並針對 Windows 或 Linux 主機執行下列動作：

   - 在用戶端機器上建立並安裝憑證
   - 計算未來的 VPN 閘道子網路 IP 範圍
   - 建立閘道子網路
   - 部署將 VPN 閘道連結至 VPN 子網路的 Azure Resource Manager 範本

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. 在 PowerShell 視窗中貼上指令碼，並提供必要的參數。 `<subscriptionId>`、`<resourceGroup>` 和 `<virtualNetworkName>` 的值應符合在[建立受控執行個體](instance-create-quickstart.md)快速入門中使用的值。 `<certificateNamePrefix>` 的值可以是您自己選擇的字串。

4. 執行 PowerShell 指令碼。

> [!IMPORTANT]
> 在 PowerShell 指令碼完成之前，請勿繼續。

## <a name="create-a-vpn-connection"></a>建立 VPN 連線

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟您在虛擬網路閘道中所建立的資源群組，然後開啟虛擬網路閘道資源。
3. 選取 [點對站設定]****，然後選取 [下載 VPN 用戶端]****。

    ![下載 VPN 用戶端](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. 在內部部署用戶端電腦上，從 zip 檔案解壓縮檔案，然後開啟含解壓縮檔案的資料夾。
5. 開啟 **WindowsAmd64** 資料夾，然後開啟 **VpnClientSetupAmd64.exe** 檔案。
6. 如果您收到 [Windows 已保護您的電腦]**** 訊息，請按一下 [其他資訊]****，然後按一下 [仍要執行]****。

    ![安裝 VPN 用戶端](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. 在 [使用者帳戶控制]  對話方塊中，按一下 [是]**** 以繼續。
8. 在參考您虛擬網路的對話方塊中選取 [是]，為您的虛擬網路安裝 VPN 用戶端。

## <a name="connect-to-the-vpn-connection"></a>連線到 VPN 連線

1. 移至內部部署用戶端電腦上 [網路與網際網路] 的 [VPN]，並選取您的 SQL 受控執行個體虛擬網路，以建立對此 VNet 的連線。 在下圖中，VNet 的名稱為 **MyNewVNet**。

    ![VPN 連線](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. 選取 [連接]。
3. 在對話方塊中，選取 [連線]****。

    ![VPN 連線](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. 出現連線管理員需要較高的權限以更新路由表的提示時，請選擇 [繼續]。
5. 在 [使用者帳戶控制] 對話方塊中，按選取 [是]**** 以繼續。

   您已建立對 SQL 受控執行個體 VNet 的 VPN 連線。

    ![VPN 連線](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>使用 SSMS 連線

1. 在內部部署用戶端電腦上，開啟 SQL Server Management Studio。
2. 在 [連線到伺服器] 對話方塊中，於 [伺服器名稱] 方塊中輸入受控執行個體的完整**主機名稱**。
3. 選取 [SQL Server 驗證]****，提供您的使用者名稱和密碼，然後選取 [連線]****。

    ![SSMS 連線](./media/point-to-site-p2s-configure/ssms-connect.png)  

連線之後，您可以檢視 [資料庫] 節點中的系統和使用者資料庫。 您也可以檢視 [安全性]、[伺服器物件]、[複寫]、[管理]、[SQL Server 代理程式] 和 [XEvent Profiler] 節點中的各種物件。

## <a name="next-steps"></a>後續步驟

- 如需如何從 Azure 虛擬機器連線的快速入門，請參閱[設定點對站連線](point-to-site-p2s-configure.md)。
- 如需有關應用程式連線選項的概觀，請參閱[將您的應用程式連線至 SQL 受控執行個體](connect-application-instance.md)。
- 若要將現有 SQL Server 資料庫從內部部署還原到受控執行個體，您可以使用 [Azure 資料庫移轉服務進行移轉](../../dms/tutorial-sql-server-to-managed-instance.md)，或使用 [T-SQL RESTORE 命令](restore-sample-database-quickstart.md)從資料庫備份檔案還原。
