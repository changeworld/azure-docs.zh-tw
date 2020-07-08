---
title: 設定網域獨立的工作群組可用性群組
description: 瞭解如何在 Azure 中的 SQL Server 虛擬機器上，設定 Active Directory 網域獨立工作組 Always On 可用性群組。
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
ms.openlocfilehash: 93819332def05022272eabc130e0f2240938f244
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955500"
---
# <a name="configure-a-workgroup-availability-group"></a>設定工作群組可用性群組 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

此文章說明建立具有 Always On 可用性群組之 Active Directory 網域獨立叢集的必要步驟；這也稱為工作群組叢集。 此文章著重於準備並設定工作群組與可用性群組的相關步驟，並避免涉及其他文章中涵蓋的步驟，例如如何建立叢集，或如何部署可用性群組。 


## <a name="prerequisites"></a>必要條件

若要設定工作群組可用性群組，您需要下列項目：
- 至少兩部執行 SQL Server 2016 (或更新版本)、部署至相同的可用性設定組或不同的可用性區域，且使用靜態 IP 位址的 Windows Server 2016 (或更新版本) 虛擬機器。 
- 子網路中至少有 4 個可用 IP 位址的區域網路。 
- 系統管理員群組中的每部機器上有一個在 SQL Server 內也具有 sysadmin 權限的帳戶。 
- 開啟連接埠：TCP 1433、TCP 5022、TCP 59999。 

可參考此文章中使用的下列參數，但可以視需要修改： 

| **名稱** | **參數** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **叢集名稱** | AGWGAG (10.0.0.6) |
| **接聽程式** | AGListener (10.0.0.7) | 
| **DNS 尾碼** | ag.wgcluster.example.com | 
| **工作群組名稱** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>設定 DNS 尾碼 

在此步驟中，設定這兩部伺服器的 DNS 尾碼。 例如： `ag.wgcluster.example.com` 。 這可讓您使用要連線物件的名稱作為網路內的完整位址，例如 `AGNode1.ag.wgcluster.example.com`。 

若要設定 DNS 尾碼，請遵循下列步驟：

1. RDP 傳入您的第一個節點，然後開啟伺服器管理員。 
1. 選取 [本機伺服器]，然後在 [電腦名稱] 下選取虛擬機器的名稱。 
1. 在 [如果您要重新命名這台電腦...] 底下，選取 [變更]。 
1. 將工作群組名稱的變更為有意義的名稱，例如 `AGWORKGROUP`： 

   ![變更工作群組名稱](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. 選取 [其他] 以開啟 [DNS 尾碼和 NetBIOS 電腦名稱] 對話方塊。 
1. 在 [這部電腦的主要 DNS 尾碼] 下輸入 DNS 尾碼的名稱 (例如 `ag.wgcluster.example.com`)，然後選取 [確定]： 

   ![新增 DNS 尾碼](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. 確認 [完整電腦名稱] 現在已顯示 DNS 尾碼，然後選取 [確定] 以儲存變更： 

   ![新增 DNS 尾碼](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. 請在系統提示時，將伺服器重新開機。 
1. 在要用於可用性群組的任何其他節點上重複這些步驟。 

## <a name="edit-a-host-file"></a>編輯主機檔案

因為沒有 Active Directory，所以沒有任何方法可以驗證 Windows 連線。 因此，透過使用文字編輯器編輯主機檔案來指派信任。 

若要編輯主機檔案，請遵循下列步驟：

1. RDP 入您的虛擬機器。 
1. 使用 [檔案總管] 移至 `c:\windows\system32\drivers\etc`。 
1. 以滑鼠右鍵按一下 **hosts** 檔案，並使用 [記事本] \(或任何其他文字編輯器\) 開啟該檔案。
1. 在檔案結尾，以 `IP Address, DNS Suffix #comment` 的形式為每個節點、可用性群組與接聽程式新增項目，如下所示： 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![將 IP 位址、叢集與接聽程式的項目新增至主機檔案](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>設定權限

由於沒有 Active Directory 可管理權限，因此您需要手動允許非內建的本機系統管理員帳戶建立叢集。 

若要這樣做，請在每個節點上的系統管理 PowerShell 工作階段中執行下列 PowerShell Cmdlet： 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

在此步驟中，您將會建立容錯移轉叢集。 如果您不熟悉這些步驟，可以遵循[容錯移轉叢集教學課程](failover-cluster-instance-storage-spaces-direct-manually-configure.md)中的步驟。

教學課程與工作群組叢集應執行的事項之間有顯著的差異：
- 在執行叢集驗證時，請取消選取 [儲存體] 與 [儲存空間直接存取]。 
- 將節點新增至叢集時，請新增完整名稱，例如：
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 取消選取 [新增適合的儲存裝置到叢集]。 

建立叢集之後，請指派靜態叢集 IP 位址。 若要這樣做，請依照下列步驟執行：

1. 在其中一個節點上，開啟 [容錯移轉叢集管理員]、選取叢集、以滑鼠右鍵按一下 [叢集核心資源] 下的 [名稱: \<ClusterNam>]，然後選取 [屬性]。 

   ![啟動叢集名稱的屬性](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. 選取 [IP 位址]  下的 IP 位址，然後選取 [編輯]。 
1. 選取 [使用靜態]、提供叢集的 IP 位址，然後選取 [確定]： 

   ![提供叢集的靜態 IP 位址](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. 確認您的設定正確無誤，然後選取 [確定] 加以儲存：

   ![驗證叢集屬性](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>建立雲端見證 

在此步驟中，設定雲端共用見證。 如果您不熟悉這些步驟，請參閱[為容錯移轉叢集部署雲端見證](/windows-server/failover-clustering/deploy-cloud-witness)。 

## <a name="enable-the-availability-group-feature"></a>啟用可用性群組功能 

在此步驟中，啟用可用性群組功能。 如果您不熟悉這些步驟，請參閱[可用性群組教學課程](availability-group-manually-configure-tutorial.md#enable-availability-groups)。 

## <a name="create-keys-and-certificates"></a>建立金鑰和憑證

在此步驟中，建立 SQL 登入在加密端點上使用的憑證。 在每個節點上建立資料夾以保存憑證備份，例如 `c:\certs`。 

若要設定第一個節點，請遵循下列步驟： 

1. 開啟 [SQL Server Management Studio] 並連線到您的第一個節點，例如 `AGNode1`。 
1. 在更新為複雜且安全的密碼之後，開啟 [新增查詢] 視窗，然後執行下列 Transact-SQL (T-SQL) 陳述式：

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

1. 接下來，建立 HADR 端點，並透過執行以下 Transact-SQL (T-SQL) 陳述式來使用憑證進行驗證：

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

1. 使用 [檔案總管] 移至您憑證所在的檔案位置，例如 `c:\certs`。 
1. 手動從第一個節點複製憑證 (例如 `AGNode1Cert.crt`)，然後將其傳輸到第二個節點上的相同位置。 

若要設定第二個節點，請遵循下列步驟： 

1. 使用 [SQL Server Management Studio] 連線到第二個節點，例如 `AGNode2`。 
1. 在更新為複雜且安全的密碼之後，於 [新增查詢] 視窗中執行下列 Transact-SQL (T-SQL) 陳述式： 

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

1. 接下來，建立 HADR 端點，並透過執行以下 Transact-SQL (T-SQL) 陳述式來使用憑證進行驗證：

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

1. 使用 [檔案總管] 移至您憑證所在的檔案位置，例如 `c:\certs`。 
1. 手動從第二個節點複製憑證 (例如 `AGNode2Cert.crt`)，然後將其傳輸到第一個節點上的相同位置。 

如果叢集中有任何其他節點，請同時在那些節點上重複這些步驟，並修改個別的憑證名稱。 

## <a name="create-logins"></a>建立登入

憑證驗證是用來跨節點同步處理資料的。 若要能夠跨節點同步處理資料，請建立另一個節點的登入、建立登入的使用者、建立用於登入的憑證以使用已備份的憑證，然後在鏡像端點上授與連線。 

若要這樣做，請先在第一個節點 (例如 `AGNode1`) 上執行下列 Transact-SQL (T-SQL) 查詢： 

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

接下來，在第二個節點 (例如 `AGNode2`) 上執行下列 Transact-SQL (T-SQL) 查詢： 

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

如果叢集中有任何其他節點，請同時在那些節點上重複這些步驟，並修改個別的憑證與使用者名稱。 

## <a name="configure-an-availability-group"></a>設定可用性群組

在此步驟中，設定您的可用性群組，並在群組中新增您的資料庫。 此時請勿建立接聽程式。 如果您不熟悉這些步驟，請參閱[可用性群組教學課程](availability-group-manually-configure-tutorial.md#create-the-availability-group)。 請務必起始容錯移轉與容錯回復，以確認所有事項都正常運作。 

   > [!NOTE]
   > 如果在同步程序中發生失敗，您可能需要暫時授與 `NT AUTHORITY\SYSTEM` sysadmin 權限以在第一個節點 (例如 `AGNode1`) 上建立叢集資源。 

## <a name="configure-a-load-balancer"></a>設定負載平衡器

在最後一個步驟中，請使用[Azure 入口網站](availability-group-load-balancer-portal-configure.md)或[PowerShell](availability-group-listener-powershell-configure.md)來設定負載平衡器。


## <a name="next-steps"></a>後續步驟

您也可以使用 [Az SQL VM CLI](availability-group-az-cli-configure.md) 來設定可用性群組。 


