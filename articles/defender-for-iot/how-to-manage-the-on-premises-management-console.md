---
title: 管理內部部署管理主控台
description: 深入瞭解內部部署管理主控台的選項，例如備份和還原、定義主機名稱，以及將 proxy 設定為感應器。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 80dbad919e9446100bdeebb7cde71c147abfc8bc
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539340"
---
# <a name="manage-the-on-premises-management-console"></a>管理內部部署管理主控台

本文涵蓋了內部部署管理主控台選項，例如備份和還原、下載委員會裝置啟用檔案、更新憑證，以及將 proxy 設定為感應器。

您可以從 Azure 入口網站上架內部部署管理主控台。

## <a name="upload-an-activation-file"></a>上傳啟用檔案

當您第一次登入時，會下載內部部署管理主控台的啟用檔。 此檔案包含在上架程式期間定義的匯總認可裝置。 此清單包含與多個訂用帳戶相關聯的感應器。

初始啟用之後，受監視裝置的數目可能會超過上線期間所定義的認可裝置數目。 例如，如果您將更多感應器連線到管理主控台，就可能會發生此事件。 如果受監視的裝置數目與認可的裝置數目之間有不一致的情況，則會在管理主控台中顯示警告。 如果發生此事件，您應該上傳新的啟用檔。

上傳啟用檔案：

1. 移至適用于 IoT 的 Azure Defender **定價** 頁面。
1. 選取 [ **下載管理主控台** ] 索引標籤的啟用檔。啟用檔案已下載。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="下載啟用檔。":::

1. 從管理主控台選取 [ **系統設定** ]。
1. 選取 [ **啟用**]。
1. 選取 **[選擇** 檔案]，然後選取您儲存的檔案。

## <a name="manage-certificates"></a>管理憑證

安裝內部部署管理主控台之後，會產生本機自我簽署憑證，並使用此憑證來存取管理主控台的 web 應用程式。 當系統管理員使用者第一次登入管理主控台時，系統會提示他們提供 SSL/TLS 憑證。 如需第一次安裝的詳細資訊，請參閱 [啟用和設定您的內部部署管理主控台](how-to-activate-and-set-up-your-on-premises-management-console.md)。

下列各節提供有關更新憑證、使用憑證 CLI 命令，以及支援的憑證和憑證參數的資訊。

### <a name="about-certificates"></a>關於憑證

適用于 IoT 的 Azure Defender 使用 SSL 和 TLS 憑證：

- 上傳 CA 簽署的憑證，以符合您組織所要求的特定憑證和加密需求。

- 允許在管理主控台和連線的感應器之間，以及在管理主控台與高可用性管理主控台之間進行驗證。 驗證會針對憑證撤銷清單和憑證到期日進行評估。 *如果驗證失敗，管理主控台和感應器之間的通訊會停止，並在主控台中顯示驗證錯誤*。 安裝之後，預設會啟用此選項。

不會驗證協力廠商轉送規則。 範例包括傳送到 SYSLOG、Splunk 或 ServiceNow 的警示資訊;和 Active Directory 的通訊。

#### <a name="ssl-certificates"></a>SSL 憑證

適用于 IoT 的 Defender 感應器和內部部署管理主控台使用 SSL，以及適用于下列功能的 TLS 憑證： 

 - 保護使用者和設備的 web 主控台之間的通訊安全。 
 
 - 保護感應器和內部部署管理主控台上的 REST API 通訊。
 
 - 在感應器和內部部署管理主控台之間進行安全通訊。 

安裝之後，設備會產生本機自我簽署憑證，以允許初步存取 web 主控台。 您可以使用命令列工具來安裝企業 SSL 和 TLS 憑證 [`cyberx-xsense-certificate-import`](#cli-commands) 。 

 > [!NOTE]
 > 若為整合和轉送規則，其中設備是會話的用戶端和起始端，則會使用特定的憑證，而且與系統憑證無關。  
 >
 >在這些情況下，通常會從伺服器接收憑證，或使用將提供特定憑證來設定整合的非對稱式加密。 

### <a name="update-certificates"></a>更新憑證

內部部署管理主控台的系統管理員使用者可以更新憑證。

若要更新憑證：  

1. 選取 [ **系統設定**]。

1. 選取 [ **SSL/TLS 憑證**]。
1. 刪除或編輯憑證，並新增一個新憑證。
   
   - 新增憑證名稱。
   
   - 上傳 CRT 檔案和金鑰檔，然後輸入複雜密碼。
   - 視需要上傳 PEM 檔案。

若要變更驗證設定：

1. 開啟或關閉 [ **啟用憑證驗證** ] 切換。

1. 選取 [儲存]。

如果啟用此選項且驗證失敗，則管理主控台和感應器之間的通訊會停止，而且主控台中會出現驗證錯誤。

### <a name="certificate-support"></a>憑證支援

以下是支援的憑證：

- 私用和企業金鑰基礎結構 (私用 PKI) 
 
- 公開金鑰基礎結構 (公開 PKI)  

- 在本機產生的設備 (本機自我簽署)  

  > [!IMPORTANT]
  > 我們不建議使用自我簽署憑證。 這種類型的連線不安全，而且只能用於測試環境。 因為無法驗證憑證的擁有者，而且無法維護系統的安全性，所以不應該將自我簽署憑證用於生產網路。

### <a name="supported-ssl-certificates"></a>支援的 SSL 憑證 

支援下列參數： 

**憑證 CRT**

- 您功能變數名稱的主要憑證檔案

- 簽名演算法 = SHA256RSA
- 簽章雜湊演算法 = SHA256
- 有效期自 = 有效的過去日期
- 有效期限 = 有效的未來日期
- 公開金鑰 = RSA 2048 位 (最低) 或4096位
- CRL 發佈點 = crl 檔案的 URL
- 主體 CN = URL，可以是萬用字元憑證;例如，node.js。 <span>com 或 *. contoso。 <span>Com
- Subject (C) ountry = 已定義，例如 US
- Subject (OU) 組織單位 = 已定義;例如，Contoso Labs
- Subject (O) rganization = 已定義;例如，Contoso Inc。

**金鑰檔案**

- 建立 CSR 時產生的金鑰檔

- RSA 2048 位 (最低) 或4096位

 > [!Note]
 > 使用金鑰長度4096bits：
 > - 每個連線開始時的 SSL 交握將會變慢。  
 > - 在交握期間，CPU 使用率也會增加。 

**憑證鏈結**

- 中繼憑證檔案 (您的 CA 所提供的任何) 。

- 發行伺服器憑證的 CA 憑證應該是檔案中的第一個，後面接著任何其他專案，跟根目錄一樣。 
- 連鎖店可以包含包屬性。

**密碼**

- 支援一個金鑰。

- 當您匯入憑證時設定。

具有其他參數的憑證可能會運作，但 Microsoft 不支援它們。

#### <a name="encryption-key-artifacts"></a>加密金鑰構件

**pem： certificate container file**

隱私權增強郵件 (PEM) 檔是用來保護電子郵件的一般檔案類型。 現今，PEM 檔案會與憑證搭配使用，並使用 x509 ASN1 金鑰。  

容器檔案定義于 Rfc 1421 至1424，這是一種可能只包含公用憑證的容器格式。 例如，Apache 安裝、CA 憑證、檔案等、SSL 或憑證。 這可能包含整個憑證鏈，包括公開金鑰、私密金鑰和根憑證。  

它也可以將 CSR 編碼為 PKCS10 格式，以轉譯成 PEM。

**.cer. crt： certificate 容器檔案**

`.pem` `.der` 具有不同副檔名的或格式化檔案。 Windows 檔案總管將檔案辨識為憑證。 `.pem`   Windows 檔案總管不能辨識檔案。

**。金鑰：私密金鑰檔案**

金鑰檔的格式與 PEM 檔案的格式相同，但副檔名不同。 

#### <a name="additional-commonly-available-key-artifacts"></a>其他常用的主要構件

**。 csr-憑證簽署要求**。  

此檔案會用來提交給憑證授權單位單位。 實際的格式為 PKCS10 （定義于 RFC 2986 中），其中可能包含所要求憑證的部分或所有重要詳細資料。 例如，主體、組織和狀態。 它是由 CA 簽署的憑證公開金鑰，並會接收傳回的憑證。  

傳回的憑證是公開憑證，其中包含公開金鑰，但不包含私密金鑰。 

**pkcs12 .pfx – password 容器**。 

最初是由 RSA 在 Public-Key 加密標準 (PKCS) 中定義，這是 Microsoft 最初增強的12個變數，並在稍後提交為 RFC 7292。  

此容器格式需要同時包含公開和私用憑證組的密碼。 與檔案不同 `.pem`   的是，此容器已完全加密。  

您可以使用 OpenSSL 將檔案轉換成 `.pem`   具有公開和私密金鑰的檔案： `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**der –二進位編碼的 PEM**。

以二進位格式編碼 asn.1 語法的方法是透過檔案 `.pem`   ，這只是一個 Base64 編碼的檔案 `.der` 。 

OpenSSL 可將這些檔案轉換成 `.pem` ：  `openssl x509 -inform der -in to-convert.der -out converted.pem` 。  

Windows 會將這些檔案辨識為憑證檔案。 根據預設，Windows 會將憑證匯出為 `.der` 具有不同副檔名的格式化檔案。

**crl-憑證撤銷清單**。  

憑證授權單位單位會產生這些授權單位，以在憑證到期前將憑證解除授權。 

#### <a name="cli-commands"></a>CLI 命令

使用 `cyberx-xsense-certificate-import` CLI 命令匯入憑證。 若要使用此工具，您必須使用 WinSCP 或 Wget 等工具，將憑證檔案上傳至裝置。

此命令支援下列輸入旗標：

- `-h`：顯示命令列説明語法。

- `--crt`：憑證檔案的路徑 ( .crt 副檔名) 。

- `--key`：  \* 金鑰檔。 金鑰長度至少應為2048位。

- `--chain`：憑證鏈檔案的路徑 (選擇性) 。

- `--pass`：用來加密憑證 (選用) 的複雜密碼。

- `--passphrase-set`： Default = `False` 、未使用。 將設定為， `True` 以使用先前憑證提供的先前複雜密碼 (選擇性) 。

當您使用 CLI 命令時：

- 確認憑證檔案在設備上是可讀取的。

- 確認憑證中的功能變數名稱和 IP 符合 IT 部門已規劃的設定。

### <a name="use-openssl-to-manage-certificates"></a>使用 OpenSSL 管理憑證

使用下列命令來管理您的憑證：

| 描述 | CLI 命令 |
|--|--|
| 產生新的私密金鑰和憑證簽署要求 | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 產生自我簽署的憑證 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 針對現有的私密金鑰 (CSR) 產生憑證簽署要求 | `openssl req -out CSR.csr -key privateKey.key -new` |
| 根據現有的憑證產生憑證簽署要求 | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 從私密金鑰移除複雜密碼 | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

如果您需要檢查憑證、CSR 或私密金鑰內的資訊，請使用下列命令：

| 描述 | CLI 命令 |
|--|--|
| 檢查 (CSR) 的憑證簽署要求 | `openssl req -text -noout -verify -in CSR.csr` |
| 檢查私密金鑰 | `openssl rsa -in privateKey.key -check` |
| 檢查憑證 | `openssl x509 -in certificate.crt -text -noout`  |

如果您收到錯誤，指出私密金鑰與憑證不符，或您安裝到網站的憑證不受信任，請使用下列命令來修正錯誤：

| 描述 | CLI 命令 |
|--|--|
| 檢查公開金鑰的 MD5 雜湊，以確保它符合 CSR 或私密金鑰中的內容 | 單. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

若要將憑證和金鑰轉換成不同的格式，以使其與特定類型的伺服器或軟體相容，請使用下列命令：

| 描述 | CLI 命令 |
|--|--|
| 將 DER 檔案 ( .crt) 轉換為 PEM  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| 將 PEM 檔案轉換成 DER | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 將 PKCS # 12 檔案 ( .pfx. p12) 包含私密金鑰和憑證轉換為 PEM | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />您可以新增 `-nocerts` 至只輸出私密金鑰，或新增 `-nokeys` 至只輸出憑證。 |
| 將 PEM 憑證檔案和私密金鑰轉換成 PKCS # 12 ( .pfx. p12)  | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>定義備份和還原設定

內部部署管理主控台系統備份會每日自動執行。 資料會儲存在不同的磁片上。 預設位置為 `/var/cyberx/backups`。 

您可以自動將此檔案傳送至內部網路。 

> [!NOTE]
> 您只能在相同版本上執行備份和還原程式。 

若要備份內部部署管理主控台電腦： 

- 登入系統管理帳戶，然後輸入 `sudo cyberx-management-backup -full` 。

若要還原最新的備份檔案：

- 登入系統管理帳戶，然後輸入 `$ sudo cyberx-management-system-restore` 。

若要將備份儲存至外部 SMB 伺服器：

1. 在外部 SMB 伺服器中建立共用資料夾。  

   取得存取 SMB 伺服器所需的資料夾路徑、使用者名稱和密碼。 

2. 在適用于 IoT 的 Defender 中，建立備份的目錄：

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. 編輯 fstab：  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. 編輯或建立要共用的 SMB 伺服器認證： 

   - `sudo nano /etc/samba/user` 

5. 新增： 

   - `username=<user name>`

   - `password=<password>` 

6. 掛接目錄： 

   - `sudo mount -a` 

7. 針對 IoT 內部部署管理主控台的 Defender 上的共用資料夾設定備份目錄：  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>編輯主機名稱

若要編輯組織 DNS 伺服器中設定的管理主控台主機名稱：

1. 在管理主控台的左窗格中，選取 [ **系統設定**]。  

2. 在主控台的 [網路功能] 區段中，選取 [ **網路**]。 

3. 輸入組織 DNS 伺服器中設定的主機名稱。 

4. 選取 [儲存]。

## <a name="define-vlan-names"></a>定義 VLAN 名稱

在感應器和管理主控台之間，不會同步處理 VLAN 名稱。 您應該在元件上定義相同的名稱。

在 [網路功能] 區域中，選取 [ **vlan** ]，並將名稱新增至探索到的 VLAN id。 接著，選取 [儲存]。

## <a name="define-a-proxy-to-sensors"></a>定義感應器的 proxy

防止使用者直接登入感應器，藉此增強系統安全性。 相反地，請使用 proxy 通道，讓使用者從內部部署管理主控台使用單一防火牆規則來存取感應器。 這項增強功能可降低未經授權存取感應器之外網路環境的可能性。

在無法直接連線至感應器的環境中使用 proxy。

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="使用者。":::

下列程式會將感應器連接至內部部署管理主控台，並在該主控台上啟用通道：

1. 使用系統管理認證登入內部部署管理主控台設備 CLI。

2. 輸入 `sudo cyberx-management-tunnel-enable` 並選取 **Enter**。

4. 輸入 `--port 10000` 並選取 **Enter**。

## <a name="adjust-system-properties"></a>調整系統屬性

系統屬性會控制管理主控台中的各種作業和設定。 編輯或修改它們可能會損毀管理主控台的操作。 變更您的設定之前，請先參閱 [Microsoft 支援服務](https://support.microsoft.com) 。

若要存取系統屬性： 

1. 登入內部部署管理主控台或感應器。

2. 選取 [ **系統設定**]。

3. 從 [**一般**] 區段中選取 [**系統屬性**]。

## <a name="change-the-name-of-the-on-premises-management-console"></a>變更內部部署管理主控台的名稱

您可以變更內部部署管理主控台的名稱。 新的名稱會出現在主控台網頁瀏覽器、各種主控台視窗和疑難排解記錄檔中。 預設名稱為 **管理主控台**。

若要變更名稱：

1. 在左窗格的底部，選取目前的名稱。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="內部部署管理主控台版本的螢幕擷取畫面。":::

2. 在 [ **編輯管理主控台** 設定] 對話方塊中，輸入新的名稱。 名稱長度不得超過25個字元。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="編輯 Defender for IoT 平臺設定的螢幕擷取畫面。":::

3. 選取 [儲存]。 套用新的名稱。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="顯示主控台變更名稱的螢幕擷取畫面。":::

## <a name="password-recovery"></a>密碼復原

內部部署管理主控台的密碼復原會與裝置所連接的訂用帳戶相關聯。 如果您不知道裝置所連接的訂用帳戶，就無法復原密碼。

若要重設您的密碼：

1. 移至內部部署管理主控台的登入頁面。
1. 選取 [ **密碼** 復原]。
1. 複製唯一的識別碼。
1. 移至 [適用于 IoT 的 Defender **網站和感應器** ] 頁面，然後選取 [ **復原我的密碼** ] 索引標籤。
1. 輸入唯一識別碼，然後選取 [ **復原**]。 啟用檔案已下載。
1. 移至 [ **密碼修復** ] 頁面並上傳啟用檔案。
1. 選取 [下一步]  。
 
   您現在已獲得使用者名稱和新的系統產生密碼。

> [!NOTE]
> 感應器會連結到其原先連接的訂用帳戶。 您只能使用其所連接的相同訂用帳戶來復原密碼。

## <a name="update-the-software-version"></a>更新軟體版本

下列程式說明如何更新內部部署管理主控台軟體版本。 更新程式大約需要30分鐘的時間。

1. 移至 [Azure 入口網站](https://portal.azure.com/)。

1. 前往 Defender for IoT。

1. 移至 [ **更新** ] 頁面。

1. 從內部部署管理主控台區段中選取版本。

1. 選取 [下載] 並儲存檔案。

1. 登入內部部署管理主控台，並從側邊功能表選取 [ **系統設定** ]。

1. 在 [ **版本更新** ] 窗格中，選取 [ **更新**]。

1. 選取您從 [Defender for IoT **更新** ] 頁面下載的檔案。

## <a name="see-also"></a>另請參閱

[從管理主控台管理感應器](how-to-manage-sensors-from-the-on-premises-management-console.md)

[管理個別感應器](how-to-manage-individual-sensors.md)
