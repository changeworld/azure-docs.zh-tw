---
title: 在 Azure 中建立 Linux SQL Server 2017 VM | Microsoft Docs
description: 本教學課程會示範如何在 Azure 入口網站中建立 Linux SQL Server 2017 虛擬機器。
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: conceptual
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ba4eed4dcfd6d8e86c21f1ee5214108c44a8c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060240"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>在 Azure 入口網站中佈建 Linux SQL Server 虛擬機器

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

在本快速入門教學課程中，您會使用 Azure 入口網站來建立已安裝 SQL Server 2017 的 Linux 虛擬機器。

在本教學課程中，您會了解如何：

* [從資源庫建立 Linux SQL VM](#create)
* [透過 SSH 連線到您新的 VM](#connect)
* [變更 SA 密碼](#password)
* [設定遠端連線](#remote)

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a>建立已安裝 SQL Server 的 Linux VM

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在左窗格中選取 [建立資源]****。

1. 在 [建立資源]**** 窗格中，選取 [計算]****。

1. 選取 [精選]**** 標題旁邊的 [查看全部]****。

   ![查看所有 VM 映像](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. 在 [搜尋] 方塊中，輸入**SQL Server 2019**，然後選取**Enter**以開始搜尋。

1. 選取 [**作業系統** > **Redhat**] 來限制搜尋結果。

    ![SQL Server 2019 VM 映射的搜尋篩選](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. 從搜尋結果中選取 SQL Server 2019 Linux 映射。 本教學課程使用**RHEL74 上的 SQL Server 2019**。

   > [!TIP]
   > Developer Edition 可讓您使用 Enterprise Edition 的功能來進行測試或開發，但無須支付 SQL Server 授權費用。 您只需支付執行 Linux VM 的費用。

1. 選取 [建立]  。 


### <a name="set-up-your-linux-vm"></a>設定您的 Linux VM

1. 在 [基本]**** 索引標籤中，選取您的 [訂用帳戶]**** 和 [資源群組]****。 

    ![基本概念視窗](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. 在 [虛擬機器名稱]**** 中，輸入新 Linux VM 的名稱。
1. 接著，輸入或選取下列值：
   * **區域**：選取適合您的 Azure 區域。
   * **可用性選項**：選擇最適合您的應用程式和資料的 [可用性] 和 [冗余] 選項。
   * **變更大小**：選取此選項以挑選電腦大小，完成時，請選擇 [**選取**]。 如需關於 VM 機器大小的詳細資訊，請參閱 [Linux VM 大小](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes)。

     ![選擇 VM 大小](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > 針對開發和功能測試，請使用 **DS2** 或更高等級的 VM 大小。 針對效能測試，使用 **DS13** 或更高版本。

   * **驗證類型**：選取 [ **SSH 公開金鑰**]。

     > [!Note]
     > 您可以選擇使用 SSH 公開金鑰或密碼進行驗證。 SSH 較為安全。 如需有關如何產生 SSH 金鑰的指示，請參閱[在 Linux 和 Mac 上為 Azure 中的 Linux VM 建立 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys)。

   * **Username**：輸入 VM 的系統管理員名稱。
   * **SSH 公開金鑰**：輸入您的 RSA 公用金鑰。
   * **公用輸入埠**：選擇 [**允許選取的埠**]，然後挑選 [**選取公用輸入埠**] 清單中的 [ **SSH （22）** ] 埠。 在本快速入門中，必須執行此步驟，才能連線並完成 SQL Server 設定。 如果您想要從遠端連線到 SQL Server，您必須手動允許在建立虛擬機器之後，透過網際網路連接的預設埠（1433 Microsoft SQL Server）的流量。

     ![輸入連接埠](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. 對下列額外索引標籤中的設定進行您想要的任何變更，或保留預設設定。
    * **磁碟**
    * **網路功能**
    * **管理**
    * **客體設定**
    * **標記**

1. 選取 [檢閱 + 建立]  。
1. 在 [檢閱 + 建立]**** 窗格中，選取 [建立]****。

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> 連線至 Linux VM

如果您已經使用 BASH 殼層，請使用 **ssh** 命令連線到 Azure VM。 在下列命令中，取代 VM 使用者名稱和 IP 位址以連線到您的 Linux VM。

```bash
ssh azureadmin@40.55.55.555
```

您可以在 Azure 入口網站中找到您 VM 的 IP 位址。

![Azure 入口網站中的 IP 位址](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

如果您是在 Windows 上執行且沒有 BASH 殼層，請安裝 SSH 用戶端，例如 PuTTY。

1. [下載並安裝 PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 執行 PuTTY。

1. 在 [PuTTY 組態] 畫面上，輸入您 VM 的公用 IP 位址。

1. 選取 [開啟]****，並在提示時輸入您的使用者名稱和密碼。

如需有關連線至 Linux VM 的詳細資訊，請參閱[使用入口網站在 Azure 上建立 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal)。

> [!Note]
> 如果您看到有關未將伺服器主機金鑰快取在登錄中的 PuTTY 安全性警示，請從下列選項中做選擇。 如果您信任此主機，請選取 [是]**** 以將金鑰新增至 PuTTy 的快取並繼續連線。 如果您只想要繼續連線一次，而不想要將金鑰新增至快取，請選取 [否]****。 如果您不信任此主機，請選取 [取消]**** 以放棄連線。

## <a name="change-the-sa-password"></a><a id="password"></a>變更 SA 密碼

新的虛擬機器會使用隨機的 SA 密碼安裝 SQL Server。 請在使用 SA 登入來連線到 SQL Server 之前，先重設此密碼。

1. 連線到您的 Linux VM 之後，開啟新的命令終端機。

1. 使用下列命令變更 SA 密碼：

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   出現提示時，輸入新的 SA 密碼和密碼確認。

1. 重新啟動 SQL Server 服務。

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>將工具新增至您的路徑 (選擇性)

依預設會安裝數個 SQL Server [套件](sql-server-linux-virtual-machines-overview.md#packages)，包括 SQL Server 命令列工具套件。 工具套件包含 **sqlcmd** 和 **bcp** 工具。 為了方便起見，您可以選擇性地將工具路徑 `/opt/mssql-tools/bin/` 新增至您的 **PATH** 環境變數。

1. 執行下列命令，修改登入工作階段和互動式/非登入工作階段的 **PATH**：

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a>設定遠端連線

如果您需要在 Azure VM 上遠端連線到 SQL Server，就必須設定網路安全性群組上的輸入規則。 此規則可允許 SQL Server 所接聽之連接埠 (預設為 1433) 上的流量。 下列步驟說明如何使用此步驟的 Azure 入口網站。

> [!TIP]
> 如果您在佈建期間於設定中選取了輸入連接埠 [MS SQL (1433)]****，則系統會為您執行這些變更。 您可以移至下一節，了解如何設定防火牆。

1. 在入口網站中，選取 [虛擬機器] ****，然後選取 SQL Server VM。
1. 在左側導覽窗格的 [設定]**** 底下，選取 [網路]****。
1. 在 [網路] 視窗中，選取 [輸入連接埠規則]**** 底下的 [新增輸入連接埠]****。

   ![輸入連接埠規則](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. 在 [服務]**** 清單中，選取 [MS SQL]****。

    ![MS SQL 安全性群組規則](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. 按一下 [確定] **** 以儲存 VM 的規則。

### <a name="open-the-firewall-on-rhel"></a>開啟 RHEL 上的防火牆

本教學課程會引導您建立 Red Hat Enterprise Linux (RHEL) VM。 如果您需要遠端連線至 RHEL VM，也需要開啟 Linux 防火牆上的通訊埠 1433。

1. [連線](#connect)到您的 RHEL VM。

1. 在 BASH 殼層中，執行下列命令：

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>後續步驟

既然您在 Azure 中擁有 SQL Server 2017 虛擬機器，就可以在本機使用 **sqlcmd** 連線，以執行 Transact-SQL 查詢。

如果您已針對遠端 SQL Server 連線設定 Azure VM，則應該能夠從遠端連線。 如需如何從 Windows 遠端連線到 Linux 上 SQL Server 的範例，請參閱[使用 Windows 上的 SSMS 連線到 Linux 上的 SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms)。 若要使用 Visual Studio Code 連線，請參閱[使用 Visual Studio Code 來建立和執行 SQL Server 的 Transact-SQL 指令碼](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

如需更多關於 Linux 上 SQL Server 的一般資訊，請參閱 [Linux 上的 SQL Server 2017 概觀](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)。 如需使用 SQL Server 2017 Linux 虛擬機器的詳細資訊，請參閱 [Azure 上的 SQL Server 2017 虛擬機器概觀](sql-server-linux-virtual-machines-overview.md)。
