---
title: 配置與域無關的工作組可用性組
description: 瞭解如何在 Azure 中的 SQL Server 虛擬機器上配置獨立于活動目錄域的工作組"始終打開可用性"組。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122671"
---
# <a name="configure-a-workgroup-availability-group"></a>配置工作組可用性組 

本文介紹使用始終打開的可用性組創建活動目錄域無關群集所需的步驟;這也稱為工作組群集。 本文重點介紹與準備和配置工作組和可用性組相關的步驟，並介紹其他文章仲介紹的步驟，例如如何創建群集或部署可用性組。 


## <a name="prerequisites"></a>Prerequisites

要配置工作組可用性組，您需要以下操作：
- 至少兩個運行 SQL Server 2016（或更高）的 Windows Server 2016（或更高）虛擬機器，使用靜態 IP 位址部署到相同的可用性集或不同的可用性區域。 
- 子網上至少有 4 個可用 IP 位址的本地網路。 
- 管理員組中每台電腦上的帳戶，在 SQL Server 中也具有系統管理員許可權。 
- 打開端口：TCP 1433、TCP 5022、TCP 59999。 

作為參考，本文使用以下參數，但可以根據需要進行修改： 

| **名稱** | **參數** |
| :------ | :---------------------------------- |
| **節點1**   | AGNode1 （10.0.0.4） |
| **節點2**   | AGNode2 （10.0.0.5） |
| **群集名稱** | AGWGAG （10.0.0.6） |
| **聽眾** | AGListener （10.0.0.7） | 
| **DNS 尾碼** | ag.wgcluster.example.com | 
| **工作組名稱** | AGWork組 | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>設置 DNS 尾碼 

在此步驟中，為兩台伺服器配置 DNS 尾碼。 例如： `ag.wgcluster.example.com` 。 這允許您將要連接到的物件的名稱用作網路中完全限定的位址，例如`AGNode1.ag.wgcluster.example.com`。 

要配置 DNS 尾碼，請按照以下步驟操作：

1. RDP 進入您的第一個節點並打開伺服器管理員。 
1. 選擇**本機伺服器**，然後在**電腦名稱稱**下選擇虛擬機器的名稱。 
1. 選擇 **"更改..."** 下**要重命名此電腦...** 
1. 將工作組名稱的名稱更改為有意義的內容，例如`AGWORKGROUP`： 

   ![更改工作組名稱](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. 選擇 **"更多..."** 以打開**DNS 尾碼和 NetBIOS 電腦名稱稱**對話方塊。 
1. 在此**電腦的主 DNS 尾碼**下鍵入 DNS 尾碼的名稱，例如`ag.wgcluster.example.com`，然後選擇 **"確定**" ： 

   ![添加 DNS 尾碼](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. 確認 **"完整電腦名稱稱**"現在顯示 DNS 尾碼，然後選擇 **"確定"** 以保存更改： 

   ![添加 DNS 尾碼](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. 系統提示您重新開機伺服器。 
1. 在用於可用性組的任何其他節點上重複這些步驟。 

## <a name="edit-host-file"></a>編輯主機檔

由於沒有活動目錄，因此無法對視窗連接進行身份驗證。 因此，通過使用文字編輯器編輯主機檔來分配信任。 

要編輯主機檔，請按照以下步驟操作：

1. RDP 進入虛擬機器。 
1. 使用**檔資源管理器**轉到`c:\windows\system32\drivers\etc`。 
1. 按右鍵**主機**檔，然後使用**記事本**（或任何其他文字編輯器）打開該檔。
1. 在檔末尾，以類似的形式`IP Address, DNS Suffix #comment`為每個節點、可用性組和攔截器添加一個條目： 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![將 IP 位址、群集和攔截器的條目添加到主機檔](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>設定權限

由於沒有要管理許可權的活動目錄，因此需要手動允許非內置本地管理員帳戶創建群集。 

為此，請在每個節點上的管理 PowerShell 會話中運行以下 PowerShell Cmdlet： 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>建立容錯移轉叢集

在此步驟中，您將創建容錯移轉叢集。 如果您不熟悉這些步驟，則可以從[容錯移轉叢集教程](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)中遵循這些步驟。

本教程與工作組群集應執行哪些工作之間的顯著差異：
- 運行群集驗證時取消選中**存儲**和**存儲空間直接**。 
- 將節點添加到群集時，添加完全限定的名稱，例如：
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- 取消選中**將所有符合條件的存儲添加到群集**。 

創建群集後，分配靜態群集 IP 位址。 若要這樣做，請依照下列步驟執行：

1. 在其中一個節點上，打開**容錯移轉叢集管理器**，選擇群集，按右鍵**群集核心資源**下**的名稱：\<群集nam>，** 然後選擇**屬性**。 

   ![群集名稱的啟動屬性](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. 選擇**IP 位址**下的 IP 位址，然後選擇 **"編輯**"。 
1. 選擇 **"使用靜態**"，提供群集的 IP 位址，然後選擇 **"確定**" ： 

   ![為群集提供靜態 IP 位址](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. 驗證您的設置是否正確，然後選擇 **"確定"** 以保存它們：

   ![驗證群集屬性](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>建立雲端見證 

在此步驟中，配置雲共用見證。 如果您不熟悉這些步驟，請參閱[容錯移轉叢集教程](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)。 

## <a name="enable-availability-group-feature"></a>啟用可用性組功能 

在此步驟中，啟用可用性組功能。 如果您不熟悉這些步驟，請參閱[可用性組教程](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)。 

## <a name="create-keys-and-certificate"></a>創建金鑰和證書

在此步驟中，創建 SQL 登錄在加密終結點上使用的證書。 在每個節點上創建一個資料夾來保存證書備份，如`c:\certs`。 

要配置第一個節點，請按照以下步驟操作： 

1. 打開**SQL 伺服器管理工作室**並連接到第一個節點，`AGNode1`如 。 
1. 在更新為複雜和安全的密碼後打開 **"新查詢**"視窗並運行以下 Transact-SQL （T-SQL） 語句：

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

1. 接下來，創建 HADR 終結點，並通過運行此 Transact-SQL （T-SQL） 語句使用證書進行身份驗證：

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

1. 使用**檔資源管理器**轉到證書所在的檔位置，例如`c:\certs`。 
1. 手動複製證書（如`AGNode1Cert.crt`，從第一個節點，並將其傳輸到第二個節點上的同一位置）。 

要配置第二個節點，請按照以下步驟操作： 

1. 使用**SQL 伺服器管理工作室**連接到第二個節點，`AGNode2`如 。 
1. 在新**查詢**視窗中，在更新為複雜和安全的密碼後運行以下 Transact-SQL （T-SQL） 語句： 

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

1. 接下來，創建 HADR 終結點，並通過運行此 Transact-SQL （T-SQL） 語句使用證書進行身份驗證：

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

1. 使用**檔資源管理器**轉到證書所在的檔位置，例如`c:\certs`。 
1. 手動從第二個節點複製證書，如`AGNode2Cert.crt`，並將其傳輸到第一個節點上的同一位置。 

如果群集中存在任何其他節點，請在那裡重複這些步驟，修改相應的證書名稱。 

## <a name="create-logins"></a>創建登錄名

證書身份驗證用於跨節點同步資料。 為此，請為其他節點創建登錄名，為登錄創建使用者，為登錄創建證書以使用備份證書，然後在鏡像終結點上授予連接。 

為此，請首先在第一個節點上運行以下 Transact-SQL （T-SQL） 查詢，例如`AGNode1`： 

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

接下來，在第二個節點上運行以下 Transact SQL （T-SQL） 查詢，`AGNode2`例如： 

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

如果群集中存在任何其他節點，請在那裡重複這些步驟，修改相應的證書和使用者名。 

## <a name="configure-availability-group"></a>設定可用性群組

在此步驟中，配置可用性組，並將資料庫添加到其中。 此時不要創建攔截器。 如果您不熟悉這些步驟，請參閱[可用性組教程](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)。 請務必啟動容錯移轉和容錯移轉，以驗證一切是否正常工作。 

   > [!NOTE]
   > 如果在同步過程中出現故障，您可能需要授予`NT AUTHORITY\SYSTEM`sysadmin 許可權才能在第一個節點上創建群集資源，例如`AGNode1`臨時節點。 

## <a name="configure-load-balancer"></a>配置負載等化器

在最後一步中，使用[Azure 門戶](virtual-machines-windows-portal-sql-alwayson-int-listener.md)或[PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)配置負載等化器


## <a name="next-steps"></a>後續步驟

您還可以使用 Az [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md)配置可用性組。 


