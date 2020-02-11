---
title: 設定網域獨立的工作組可用性群組
description: 瞭解如何在 Azure 中的 SQL Server 虛擬機器上設定 Active Directory 網域獨立的工作組 Always On 可用性群組。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122671"
---
# <a name="configure-a-workgroup-availability-group"></a>設定工作組可用性群組 

本文說明使用 Always On 可用性群組建立 Active Directory 網域獨立叢集所需的步驟;這也稱為工作組叢集。 本文著重于準備和設定工作組和可用性群組的相關步驟，並 glosses 其他文章中所涵蓋的步驟，例如如何建立叢集，或部署可用性群組。 


## <a name="prerequisites"></a>必要條件

若要設定工作組可用性群組，您需要下列各項：
- 至少有兩個 Windows Server 2016 （或更新版本）的虛擬機器執行 SQL Server 2016 （或更新版本），部署到相同的可用性設定組或不同的可用性區域，使用靜態 IP 位址。 
- 子網中至少有4個可用 IP 位址的區域網路。 
- 系統管理員群組中每部電腦上的帳戶，在 SQL Server 內也具有 sysadmin 許可權。 
- 開啟埠： TCP 1433、TCP 5022、TCP 59999。 

如需參考，請參閱本文中使用的下列參數，但可以視需要修改： 

| **名稱** | **參數** |
| :------ | :---------------------------------- |
| **節點**   | AGNode1 （10.0.0.4） |
| **Node2**   | AGNode2 （10.0.0.5） |
| **叢集名稱** | AGWGAG （10.0.0.6） |
| **接聽程式** | AGListener （10.0.0.7） | 
| **DNS 尾碼** | ag.wgcluster.example.com | 
| **工作組名** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>設定 DNS 尾碼 

在此步驟中，請設定這兩部伺服器的 DNS 尾碼。 例如： `ag.wgcluster.example.com` 。 這可讓您使用您想要連接的物件名稱，做為您網路內的完整位址，例如 `AGNode1.ag.wgcluster.example.com`。 

若要設定 DNS 尾碼，請遵循下列步驟：

1. RDP 至您的第一個節點，並開啟伺服器管理員。 
1. 選取 [**本機伺服器**]，然後在 [**電腦名稱稱**] 下選取虛擬機器的名稱。 
1. 選取 [將**此電腦重新命名**] 底下的 [**變更 ...** ]。 
1. 將工作組名稱的名稱變更為有意義的內容，例如 `AGWORKGROUP`： 

   ![變更工作組名稱](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. 選取 [**更多 ...** ] 以開啟 [ **DNS 尾碼和 NetBIOS 電腦名稱稱**] 對話方塊。 
1. 在 [這部**電腦的主要 dns 尾碼**] 底下輸入 dns 尾碼的名稱，例如 `ag.wgcluster.example.com`，然後選取 **[確定]** ： 

   ![新增 DNS 尾碼](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. 確認**完整電腦名稱稱**現在顯示 DNS 尾碼，然後選取 **[確定]** 以儲存變更： 

   ![新增 DNS 尾碼](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. 當系統提示您時，請將伺服器重新開機。 
1. 在要用於可用性群組的任何其他節點上重複這些步驟。 

## <a name="edit-host-file"></a>編輯主機檔案

因為沒有 active directory，所以沒有任何方法可以驗證 windows 連接。 因此，藉由使用文字編輯器編輯主機檔案來指派信任。 

若要編輯主機檔案，請遵循下列步驟：

1. 透過 RDP 連線到您的虛擬機器。 
1. 使用 [檔案**瀏覽器**] 移至 `c:\windows\system32\drivers\etc`。 
1. 以滑鼠右鍵按一下**hosts**檔案，然後使用 [**記事本**] （或任何其他文字編輯器）開啟檔案。
1. 在檔案結尾，以 `IP Address, DNS Suffix #comment` 的形式新增每個節點、可用性群組和接聽程式的專案，如下所示： 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![將 IP 位址、叢集和接聽程式的專案新增至主機檔案](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>設定權限

由於沒有管理許可權的 Active Directory，因此您必須手動允許非內建的本機系統管理員帳戶來建立叢集。 

若要這麼做，請在每個節點上的系統管理 PowerShell 會話中執行下列 PowerShell Cmdlet： 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

在此步驟中，您將建立容錯移轉叢集。 如果您不熟悉這些步驟，您可以從[容錯移轉叢集教學](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)課程加以遵循。

教學課程與工作組叢集應執行的事項之間有顯著的差異：
- 取消核取 [**儲存**]，然後在執行叢集驗證時**儲存空間直接存取**。 
- 將節點新增至叢集時，請新增完整名稱，例如：
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 取消核取 **[將所有合格的存放裝置新增到**叢集]。 

建立叢集之後，請指派靜態叢集 IP 位址。 若要執行此作業，請遵循下列步驟：

1. 在其中一個節點上，開啟**容錯移轉叢集管理員**，選取叢集，以滑鼠右鍵按一下 [叢集**核心資源**] 底下的 [ **\<ClusterNam] >** ，然後選取 [**屬性**]。 

   ![啟動叢集名稱的屬性](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. 選取 [ **Ip 位址**] 底下的 [ip 位址]，然後選取 [**編輯**]。 
1. 選取 [**使用靜態**]，提供叢集的 IP 位址，然後選取 **[確定]** ： 

   ![提供叢集的靜態 IP 位址](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. 確認您的設定看起來正確，然後選取 **[確定]** 以儲存它們：

   ![驗證叢集屬性](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>建立雲端見證 

在此步驟中，設定雲端共用見證。 如果您不熟悉這些步驟，請參閱[容錯移轉叢集教學](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)課程。 

## <a name="enable-availability-group-feature"></a>啟用可用性群組功能 

在此步驟中，請啟用可用性群組功能。 如果您不熟悉這些步驟，請參閱[可用性群組教學](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)課程。 

## <a name="create-keys-and-certificate"></a>建立金鑰和憑證

在此步驟中，建立 SQL 登入在加密端點上使用的憑證。 在每個節點上建立資料夾，以保存憑證備份，例如 `c:\certs`。 

若要設定第一個節點，請遵循下列步驟： 

1. 開啟**SQL Server Management Studio**並連接到您的第一個節點，例如 `AGNode1`。 
1. 開啟**新的查詢**視窗，並在更新為複雜且安全的密碼之後，執行下列 Transact-sql （t-sql）語句：

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. 接下來，建立 HADR 端點，並藉由執行下列 Transact-sql （T-sql）語句來使用憑證來進行驗證：

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. 使用 [檔案**瀏覽器**] 移至您的憑證所在的檔案位置，例如 `c:\certs`。 
1. 手動從第一個節點複製一份憑證，例如 `AGNode1Cert.crt`，然後將它傳輸到第二個節點上的相同位置。 

若要設定第二個節點，請遵循下列步驟： 

1. 使用**SQL Server Management Studio**（例如 `AGNode2`）連接到第二個節點。 
1. 在**新的查詢**視窗中，在更新為複雜且安全的密碼之後，執行下列 Transact-sql （t-sql）語句： 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. 接下來，建立 HADR 端點，並藉由執行下列 Transact-sql （T-sql）語句來使用憑證來進行驗證：

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. 使用 [檔案**瀏覽器**] 移至您的憑證所在的檔案位置，例如 `c:\certs`。 
1. 手動從第二個節點複製一份憑證，例如 `AGNode2Cert.crt`，然後將它傳輸到第一個節點上的相同位置。 

如果叢集中有任何其他節點，請同時在該處重複這些步驟，並修改個別的憑證名稱。 

## <a name="create-logins"></a>建立登入

憑證驗證是用來跨節點同步處理資料。 若要這樣做，請建立另一個節點的登入、建立登入的使用者、建立用於登入的憑證以使用已備份的憑證，然後在鏡像端點上授與 connect。 

若要這麼做，請先在第一個節點上執行下列 Transact-sql （T-sql）查詢，例如 `AGNode1`： 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

接下來，在第二個節點上執行下列 Transact-sql （T-sql）查詢，例如 `AGNode2`： 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

如果叢集中有任何其他節點，請同時在該處重複這些步驟，修改個別的憑證和使用者名稱。 

## <a name="configure-availability-group"></a>設定可用性群組

在此步驟中，請設定您的可用性群組，並在其中新增您的資料庫。 此時請勿建立接聽程式。 如果您不熟悉這些步驟，請參閱[可用性群組教學](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)課程。 請務必起始容錯移轉和容錯回復，以確認所有專案都正常運作。 

   > [!NOTE]
   > 如果在同步處理過程中發生失敗，您可能需要授與 `NT AUTHORITY\SYSTEM` 系統管理員許可權，才能在第一個節點上建立叢集資源，例如暫時 `AGNode1`。 

## <a name="configure-load-balancer"></a>設定負載平衡器

在最後一個步驟中，使用[Azure 入口網站](virtual-machines-windows-portal-sql-alwayson-int-listener.md)或[PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)來設定負載平衡器


## <a name="next-steps"></a>後續步驟

您也可以使用[AZ SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md)來設定可用性群組。 


