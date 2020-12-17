---
title: 安裝適用于 Azure NetApp Files 的 Azure 應用程式一致快照集工具 |Microsoft Docs
description: 提供安裝您可以搭配 Azure NetApp Files 使用之 Azure 應用程式一致快照集工具的指南。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 5c2182fc80c3745e0238c378c1cade0530393181
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632611"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>安裝 Azure 應用程式一致的快照集工具 (預覽) 

本文提供的指南說明如何安裝 Azure 應用程式一致的快照集工具，以搭配 Azure NetApp Files 使用。

## <a name="introduction"></a>簡介

可下載的自我安裝程式旨在讓您輕鬆地以非根使用者許可權設定和執行快照集工具 (例如 azacsnap) 。 安裝程式將會設定使用者，並將快照集工具放入 users `$HOME/bin` 子目錄， (default = `/home/azacsnap/bin`) 。
自助安裝程式會根據執行安裝 (的使用者設定（例如根) ），嘗試判斷所有檔案的正確設定和路徑。 如果必要條件步驟 (啟用與存放裝置的通訊，而且 SAP Hana) 以 root 的形式執行，則安裝將會複製私密金鑰和 `hdbuserstore` 備份使用者的位置。 不過，啟用與儲存體後端通訊的步驟可能會在安裝後由具知識的系統管理員以手動方式進行 SAP Hana。

## <a name="prerequisites-for-installation"></a>安裝的必要條件

遵循指導方針來設定和執行快照集和嚴重損壞修復命令。 建議您在安裝和使用快照集工具之前，以 root 的形式完成下列步驟。

1. **作業系統已修補**：請參閱 [如何在 Azure 上安裝和設定 SAP Hana (大型) 實例](/azure/virtual-machines/workloads/sap/hana-installation#setting-up-smt-server-for-suse-linux)的修補和 SMT 設定。
1. **已設定時間同步** 處理。 客戶必須提供 NTP 相容的時間伺服器，並據以設定 OS。
1. **已安裝 hana** ：請參閱 [hana 資料庫上的 SAP NetWeaver 安裝](https://blogs.msdn.microsoft.com/saponsqlserver/2017/11/21/sap-netweaver-installation-on-hana-database/)中的 hana 安裝指示。
1. **[啟用與存放裝置的通訊](#enable-communication-with-storage)** (請參閱個別的區段以取得詳細資料) ：客戶必須使用私用/公開金鑰組來設定 SSH，並為每個節點提供公開金鑰，在此節點中，快照集工具是規劃要在儲存體後端上執行，以進行安裝的 Microsoft 作業。
   1. **針對 Azure NetApp Files (參閱個別區段以取得詳細資料)**：客戶必須產生服務主體驗證檔案。
   1. **針對「Azure 大型實例」 (請參閱個別的區段以取得詳細資料)**：客戶必須以私用/公開金鑰組設定 SSH，並為每個要執行快照集工具的每個節點提供公開金鑰，以供在儲存體後端進行設定的 Microsoft 作業。

      使用 SSH 連接到其中一個節點來測試此 (例如 `ssh -l <Storage UserName> <Storage IP Address>`) 。
      輸入 `exit` 以登出儲存提示。

      Microsoft operations 將在布建時提供儲存體使用者和儲存體 IP。
  
1. **[啟用與 SAP Hana 的通訊](#enable-communication-with-sap-hana)** (如需詳細資料，請參閱個別區段) ：客戶必須設定適當的 SAP Hana 使用者，以執行快照集的必要許可權。
   1. 您可以從命令列測試這項設定，如下所示：使用中的文字 `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - 上述範例適用于 SAP Hana 的非 SSL 通訊。

## <a name="enable-communication-with-storage"></a>啟用與存放裝置的通訊

本節說明如何啟用與儲存體的通訊。

### <a name="azure-netapp-files"></a>Azure NetApp Files

建立 RBAC 服務主體

1. 在 Azure Cloud Shell 會話中，確定您已登入要與服務主體建立關聯的訂用帳戶（預設）：

    ```azurecli-interactive
    az account show
    ```

1. 如果訂用帳戶不正確，請使用

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. 根據下列範例使用 Azure CLI 建立服務主體

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. 這應該會產生類似下列範例的輸出：

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > 此命令會自動將 RBAC 參與者角色指派給訂用帳戶層級的服務主體，您可以將範圍縮小至您的測試將在其中建立資源的特定資源群組。

1. 將輸出內容剪下並貼到與 `azureauth.json` 命令儲存在同一個系統上的檔案 `azacsnap` ，並以適當的系統許可權保護檔案。

### <a name="azure-large-instance"></a>Azure 大型實例

與儲存體後端的通訊會透過加密的 SSH 通道來執行。 下列範例步驟是針對此通訊的 SSH 設定提供指引。

1. 修改檔案 `/etc/ssh/ssh_config`

    請參閱下列輸出，其中已 `MACs hmac-sha1` 新增行：

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. 建立私用/公開金鑰組

    使用下列範例命令來產生金鑰組，請勿在產生金鑰時輸入密碼。

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. 將公開金鑰傳送給 Microsoft 作業

    將以下命令的輸出 `cat /root/.ssh/id_rsa.pub` (範例) 傳送至 Microsoft Operations，讓快照集工具能夠與儲存子系統進行通訊。

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>啟用與 SAP Hana 的通訊

快照集工具會與 SAP Hana 通訊，而且需要具有適當許可權的使用者，才能起始和釋放資料庫儲存點。 下列範例顯示 SAP Hana v2 使用者的設定，以及與 `hdbuserstore` SAP Hana 資料庫的通訊。

下列範例命令會在 SAP Hana 2 的 SYSTEMDB 中設定使用者 (AZACSNAP) 。
資料庫，視需要變更 IP 位址、使用者名稱和密碼：

1. 連接到 SYSTEMDB 以建立使用者

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. 建立使用者

    這個範例會在 SYSTEMDB 中建立 AZACSNAP 使用者。

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. 授與使用者許可權

    此範例會將 AZACSNAP 使用者的許可權設定為允許執行資料庫一致儲存體快照集。

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *選用* -防止使用者的密碼過期

    > [!NOTE]
    > 進行此變更之前，請先檢查公司原則。

   此範例會停用 AZACSNAP 使用者的密碼到期，如果沒有這種變更，使用者的密碼將會過期，因而無法正確地取得快照。  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. 設定 SAP Hana 安全使用者存放區 (變更密碼) 此範例會使用 `hdbuserstore` 來自 Linux shell 的命令來設定 SAP Hana 安全使用者存放區。

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. 檢查 SAP Hana 安全使用者存放區以檢查安全使用者存放區是否已正確設定，請使用 `hdbuserstore` 命令來列出類似下列範例的輸出。 有關使用的詳細資訊 `hdbuserstore` 可在 SAP 網站上取得。

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="additional-instructions-for-using-the-log-trimmer-sap-hana-20-and-later"></a>使用 log 修剪器 (SAP Hana 2.0 和更新版本的其他指示) 

如果使用記錄修剪器，則下列範例命令會在 SAP Hana 2.0 資料庫系統上) 的租使用者資料庫 (s 中設定使用者 (AZACSNAP) 。 請記得視需要變更 IP 位址、使用者名稱和密碼：

1. 連接至租使用者資料庫以建立使用者，租使用者專屬的詳細資料為 `<IP_address_of_host>` 和 `<SYSTEM_USER_PASSWORD>` 。  此外，請注意 `30015` 與租使用者資料庫通訊所需 () 埠。

    ```bash
    hdbsql -n <IP_address_of_host>:30015 - i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output  
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql TENANTDB=>
    ```

1. 建立使用者

    這個範例會在 SYSTEMDB 中建立 AZACSNAP 使用者。

    ```sql
    hdbsql TENANTDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. 授與使用者許可權

    此範例會將 AZACSNAP 使用者的許可權設定為允許執行資料庫一致儲存體快照集。

    ```sql
    hdbsql TENANTDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *選用* -防止使用者的密碼過期

    > [!NOTE]
    > 進行此變更之前，請先檢查公司原則。

   此範例會停用 AZACSNAP 使用者的密碼到期，如果沒有這種變更，使用者的密碼將會過期，因而無法正確地取得快照。  

   ```sql
   hdbsql TENANTDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

> [!NOTE]  
> 針對所有租使用者資料庫重複這些步驟。 您可以針對 SYSTEMDB 使用下列 SQL 查詢來取得所有租使用者的連接詳細資料。

```sql
SELECT HOST, SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%'
```

請參閱下列範例查詢和輸出。

```bash
hdbsql -jaxC -n 10.90.0.31:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> " SELECT HOST,SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%' "
```

```output
sapprdhdb80,30013,SYSTEMDB
sapprdhdb80,30015,H81
sapprdhdb80,30041,H82
```

### <a name="using-ssl-for-communication-with-sap-hana"></a>使用 SSL 進行與 SAP Hana 的通訊

此 `azacsnap` 工具會利用 SAP Hana 的 `hdbsql` 命令與 SAP Hana 進行通訊。 這包括加密與 SAP Hana 的通訊時，使用 SSL 選項。 `azacsnap` 使用 `hdbsql` 命令的 SSL 選項，如下所示。

使用選項時，一律會使用下列各項 `azacsnap --ssl` ：

- `-e` -啟用 TLS encryptionTLS/SSL 加密。 伺服器會選擇最高可用。
- `-ssltrustcert` -指定是否驗證服務器的憑證。
- `-sslhostnameincert "*"` -指定用來驗證服務器身分識別的主機名稱。 藉由指定 `"*"` 做為主機名稱，就不會驗證服務器的主機名稱。

SSL 通訊也需要金鑰存放區和信任存放區檔案。  雖然這些檔案可能會儲存在 Linux 安裝上的預設位置，為了確保各種 SAP Hana 系統都會使用正確的金鑰材料 (也就是說，在每個 SAP Hana 系統都使用不同的金鑰存放區和信任存放區檔案的情況下，) `azacsnap` 預期金鑰存放區和信任存放區檔案會儲存在 `securityPath` 設定檔中指定的位置 `azacsnap` 。

#### <a name="key-store-files"></a>金鑰存放區檔案

- 如果使用多個具有相同金鑰內容的 Sid，您可以更輕鬆地在設定檔中定義的 securityPath 位置建立連結 `azacsnap` 。  使用 SSL 確定每個 SID 都存在這些值。
  - 針對 openssl：
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - 針對 commoncrypto：
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- 如果使用多個 Sid 搭配每個 SID 的不同金鑰內容，請複製 (，或將檔案) 移動並重新命名為 Sid 設定檔中定義的 securityPath 位置 `azacsnap` 。
  - 針對 openssl：
    - `mv key.pem <securityPath>/<SID>_keystore`
  - 針對 commoncrypto：
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

當 `azacsnap` 呼叫時 `hdbsql` ，它會新增 `-sslkeystore=<securityPath>/<SID>_keystore` 至命令列。

#### <a name="trust-store-files"></a>信任存放區檔案

- 如果使用多個具有相同金鑰內容的 Sid，請在設定檔中定義的 securityPath 位置建立永久連結 `azacsnap` 。  使用 SSL 確定每個 SID 都存在這些值。
  - 針對 openssl：
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - 針對 commoncrypto：
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- 如果使用多個 Sid 搭配每個 SID 的不同金鑰內容，請複製 (，或將檔案) 移動並重新命名為 Sid 設定檔中定義的 securityPath 位置 `azacsnap` 。
  - 針對 openssl：
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - 針對 commoncrypto：
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`檔案名的元件必須是大寫的 SAP Hana 系統識別碼 (例如，、等等 `H80` `PR1` 。 ) 

當 `azacsnap` 呼叫時 `hdbsql` ，它會新增 `-ssltruststore=<securityPath>/<SID>_truststore` 至命令列。

因此，在 `azacsnap -c test --test hana --ssl openssl` `SID` `H80` 設定檔中執行時，它會執行連接，如下所示 `hdbsql` ：

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\`字元是命令列換行，可改善在命令列上傳遞之多個參數的清晰度。

## <a name="installing-the-snapshot-tools"></a>安裝快照集工具

可下載的自我安裝程式旨在讓您輕鬆地以非根使用者許可權設定和執行快照集工具 (例如 azacsnap) 。 安裝程式將會設定使用者，並將快照集工具放入 users `$HOME/bin` 子目錄， (default = `/home/azacsnap/bin`) 。

自助安裝程式會根據執行安裝 (的使用者設定（例如根) ），嘗試判斷所有檔案的正確設定和路徑。 如果先前的設定步驟 (啟用與存放裝置的通訊，而且 SAP Hana) 以 root 的形式執行，則安裝會將私密金鑰和複製 `hdbuserstore` 到備份使用者的位置。 不過，在安裝之後，有可能會讓有知識的系統管理員手動完成與儲存體後端通訊的步驟，以及 SAP Hana。

> [!NOTE]
> 針對較早的 Azure 大型實例安裝 SAP Hana，預先安裝快照工具的目錄是 `/hana/shared/<SID>/exe/linuxx86_64/hdb` 。

完成 [必要步驟](#prerequisites-for-installation) 之後，現在可以使用自助安裝程式來安裝快照集工具，如下所示：

1. 將下載的自助程式複製到目標系統。
1. 以使用者的身份執行自助安裝程式 `root` ，請參閱下列範例。 如有必要，請使用命令將檔案設為可執行檔 `chmod +x *.run` 。

在沒有任何引數的情況下執行自動安裝程式命令，將會顯示使用安裝程式安裝快照集工具的說明，如下所示：

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> 自助安裝程式可選擇從套件組合解壓縮 (-X) 快照集工具，而不需執行任何使用者建立和設定。 這可讓有經驗的系統管理員手動完成安裝步驟，或複製命令以升級現有的安裝。

### <a name="easy-installation-of-snapshot-tools-default"></a>輕鬆安裝快照集工具 (預設) 

安裝程式已設計為可快速安裝適用于 Azure 上 SAP Hana 的快照集工具。 根據預設，如果安裝程式只以-I 選項執行，則會執行下列步驟：

1. 建立快照集使用者 ' azacsnap '，主目錄，並設定群組成員資格。
1. 設定 azacsnap 使用者的登入 `~/.profile` 。
1. 搜尋 filesystem 以尋找要新增至 azacsnap 的目錄 `$PATH` ，這些通常是 SAP Hana 工具的路徑，例如 `hdbsql` 和 `hdbuserstore` 。
1. 搜尋 filesystem 以尋找要新增至 azacsnap 的目錄 `$LD_LIBRARY_PATH` 。 許多命令都需要設定程式庫路徑才能正確執行，這會針對已安裝的使用者進行設定。
1. 從「根」使用者 (執行安裝) 的使用者，複製 azacsnap 的後端儲存體 SSH 金鑰。 這會假設「根」使用者已設定儲存體的連線能力
    - 請參閱「[啟用與儲存體的通訊](#enable-communication-with-storage)」一節。
1. 複製目標使用者的 SAP Hana 連線安全使用者存放區 azacsnap。 這會假設「根」使用者已設定安全的使用者存放區–請參閱「啟用與 SAP Hana 的通訊」一節。
1. 快照集工具會解壓縮至 `/home/azacsnap/bin/` 。
1. 中的命令 `/home/azacsnap/bin/` 會將其許可權設定 (擁有權和可執行檔位等 ) 。

下列範例會在使用預設安裝選項執行時，顯示正確的安裝程式輸出。

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>卸載快照集工具

如果已使用預設設定來安裝快照集工具，則只需要移除已安裝 (預設 = azacsnap) 命令的使用者。

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>手動安裝快照集工具

在某些情況下，您必須手動安裝工具，但建議使用安裝程式的預設選項來簡化此程式。

以字元開頭的每一行 `#` 都會示範根使用者執行字元之後的範例命令。 `\`行結尾的是 shell 命令的標準行接續字元。

作為根超級使用者，您可以依照下列方式來完成手動安裝：

1. 取得 "sapsys" 群組識別碼，在此案例中為群組識別碼 = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. 使用步驟1中的群組識別碼，建立快照集使用者 ' azacsnap ' 和主目錄，並設定群組成員資格。

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. 請確定使用者 azacsnap 的登入 `.profile` 存在。

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. 搜尋 filesystem 以尋找要新增至 azacsnap $PATH 的目錄，這些通常是 SAP Hana 工具的路徑，例如 `hdbsql` 和 `hdbuserstore` 。

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 將更新的路徑新增至使用者的設定檔

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. 搜尋 filesystem 以尋找要新增至 azacsnap $LD _LIBRARY_PATH 的目錄。

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 將更新的程式庫路徑新增至使用者的設定檔

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. 在 Azure 大型實例上
    1. 從「根」使用者 (執行安裝) 的使用者，複製 azacsnap 的後端儲存體 SSH 金鑰。 這會假設「根」使用者已設定儲存體的連線能力
       > 請參閱「[啟用與儲存體的通訊](#enable-communication-with-storage)」一節。

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. 正確設定 SSH 檔案的使用者權限

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. 在 Azure NetApp Files 上
    1. 依照 `DOTNET_BUNDLE_EXTRACT_BASE_DIR` 每個 .Net Core 單一檔案的解壓縮指導方針，設定使用者的路徑。
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. 複製目標使用者的 SAP Hana 連線安全使用者存放區 azacsnap。 這會假設「根」使用者已設定安全的使用者存放區。
    > 請參閱「[啟用與 SAP Hana 的通訊](#enable-communication-with-sap-hana)」一節。

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. 為檔案設定正確的使用者權限 `hdbuserstore`

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. 將快照集工具解壓縮至/home/azacsnap/bin/。

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. 讓命令可執行檔

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. 確定已在使用者的主目錄上設定正確的擁有權許可權

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>完成快照集工具的設定

安裝程式會在完成快照集工具的安裝之後，提供完成的步驟。
請遵循下列步驟來設定及測試快照集工具。  成功測試之後，請執行第一個資料庫一致的儲存體快照集。

下列輸出顯示使用預設安裝選項執行安裝程式之後要完成的步驟：

1. 變更為快照集使用者帳戶
    1. `su - azacsnap`
1. 設定 HANA 安全使用者存放區
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. 切換至命令的位置
   1. `cd /home/azacsnap/bin/`
1. 設定客戶詳細資料檔案
   1. `azacsnap -c configure –-configuration new`
1. 測試與儲存體的連接 ...。
   1. `azacsnap -c test –-test storage`
1. 測試與 HANA 的連接 ...。
    1. 沒有 SSL
       1. `azacsnap -c test –-test hana`
    1. 使用 SSL 時，您將需要選擇正確的 SSL 選項
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. 執行您的第一個快照集備份
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

如果在安裝之前未完成「[啟用與 SAP Hana 的通訊](#enable-communication-with-sap-hana)」，則需要步驟2。

> [!NOTE]
> 測試命令應該會正確執行。 否則，命令可能會失敗。

## <a name="configuring-the-database"></a>設定資料庫

本節說明如何設定資料基底。

### <a name="sap-hana-configuration"></a>SAP Hana 設定

有一些建議的變更要套用至 SAP Hana，以確保記錄備份和目錄的保護。 根據預設， `basepath_logbackup` 和 `basepath_catalogbackup` 會將它們的檔案輸出到 `$(DIR_INSTANCE)/backup/log` 目錄，而且這個路徑不太可能在設定為快照集的磁片區上，並 `azacsnap` 不會受到儲存體快照集的保護。

下列 `hdbsql` 命令範例旨在示範如何將記錄檔和目錄路徑設定為可由快照集之存放磁片區上的位置 `azacsnap` 。 請務必檢查命令列上的值是否符合本機 SAP Hana 設定。

### <a name="configure-log-backup-location"></a>設定記錄備份位置

在此範例中，會對參數進行變更 `basepath_logbackup` 。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>設定目錄備份位置

在此範例中，會對參數進行變更 `basepath_catalogbackup` 。 首先，請確認 `basepath_catalogbackup` 路徑是否存在於檔案系統上，如果沒有建立與目錄擁有相同擁有權的路徑。

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

如果需要建立路徑，下列範例會建立路徑，並設定正確的擁有權和許可權。 這些命令將需要以 root 的形式執行。

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

下列範例會變更 SAP Hana 設定。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>檢查記錄和目錄備份位置

進行上述變更之後，請使用下列命令確認設定是否正確。
在此範例中，依照上述指引設定的設定會顯示為 [系統設定]。

> 此查詢也會傳回預設設定以進行比較。

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>設定記錄備份超時

SAP Hana 用來執行記錄備份的預設設定為900秒 (15 分鐘) 。 建議您將此值縮減為300秒 (也就是5分鐘的) 。  然後，您可以執行定期備份 (例如，每隔10分鐘) 將 log_backups 磁片區新增至設定檔的 [其他磁片區] 區段。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>檢查記錄備份超時

變更記錄備份超時之後，請檢查以確定這已設定如下。
在此範例中，已設定的設定會顯示為系統設定，但此查詢也會傳回預設設定以進行比較。

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>下一步

- [設定 Azure 應用程式一致的快照集工具](azacsnap-cmd-ref-configure.md)
