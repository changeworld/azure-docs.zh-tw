---
title: 管理個別感應器
description: 瞭解如何管理個別的感應器，包括管理啟用檔案、執行備份，以及更新獨立感應器。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 16031c3d67b075e962c73fbb38ada36c7efeddad
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621209"
---
# <a name="manage-individual-sensors"></a>管理個別感應器

本文說明如何管理個別的感應器。 工作包括管理啟用檔案、執行備份，以及更新獨立感應器。

您也可以從內部部署管理主控台進行特定的感應器管理工作，其中多個感應器可以同時進行管理。

您可以使用 Azure 入口網站來進行感應器上線和註冊。

## <a name="manage-sensor-activation-files"></a>管理感應器啟用檔案

您的感應器已從 Azure 入口網站使用適用于 IoT 的 Azure Defender 進行上線。 每個感應器都上線為本機連接的感應器或連線到雲端的感應器。

唯一的啟用檔案會上傳至您部署的每個感應器。 如需有關何時及如何使用新檔案的詳細資訊，請參閱 [上傳新的啟用](#upload-new-activation-files)檔案。 如果您無法上傳檔案，請參閱 [疑難排解啟用檔案上傳](#troubleshoot-activation-file-upload)。

### <a name="about-activation-files-for-locally-connected-sensors"></a>關於本機連線感應器的啟用檔案

本機連線的感應器會與 Azure 訂用帳戶相關聯。 本機連線感應器的啟用檔案包含到期日。 在此日期之前的一個月時，感應器主控台頂端會出現一則警告訊息。 在您更新啟用檔案之前，會一直保留警告。

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="系統設定的螢幕擷取畫面。":::

即使啟用檔案已過期，您仍然可以繼續使用 Defender for IoT 功能。 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>關於與雲端連線的感應器的啟用檔案

雲端連線的感應器會與適用于 IoT 中樞的 Defender 產生關聯。 這些感應器不受限於啟用檔案的時間週期。 適用于雲端連線感應器的啟用檔案是用來確保連線至適用于 IoT 中樞的 Defender。

### <a name="upload-new-activation-files"></a>上傳新的啟用檔案

當下列情況時，您可能需要上傳上線感應器的新啟用檔案：

- 啟用檔案會在本機連線的感應器上到期。 

- 您想要使用不同的感應器管理模式。 

- 您想要將新的 Defender for IoT 中樞指派給連線到雲端的感應器。

若要加入新的啟用檔案：

1. 移至 [ **感應器管理** ] 頁面。

2. 選取您要上傳新啟用檔案的感應器。

3. 刪除它。

4. 在新模式中，或使用新的 Defender for IoT **hub，從上線頁面再次** 讓感應器上線。

5. 從 **下載啟用** 檔案頁面下載啟動檔案。

6. 儲存檔案。

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="從 Defender for IoT 中樞下載啟用檔案。":::

7. 登入適用于 IoT 的 Defender 感應器主控台。

8. 在感應器主控台中，選取 [**系統設定** 重新開機]  >  ****。

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="[系統設定] 畫面上的 [重新開機] 選項。":::

9. 選取 [ **上傳** ]，然後選取您儲存的檔案。

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="上傳您儲存的檔案。":::

10. 選取 [啟用]  。

### <a name="troubleshoot-activation-file-upload"></a>針對啟用檔案上傳進行疑難排解

如果無法上傳啟用檔案，您將會收到錯誤訊息。 可能發生下列事件：

- **針對本機連線的感應器**：啟用檔案無效。 如果檔案無效，請移至適用于 IoT 的 Defender 入口網站。 在 [ **感應器管理** ] 頁面上，選取具有無效檔案的感應器，並下載新的啟用檔案。

- **針對已連線到雲端的感應器**：感應器無法連線到網際網路。 檢查感應器的網路設定。 如果您的感應器需要透過 web proxy 連線來存取網際網路，請確認已在 **感應器網路** 設定畫面上正確設定您的 proxy 伺服器。 確認 \* 防火牆和/或 proxy 中允許 azure-devices.net:443。 如果不支援萬用字元，或您想要更多控制，您應在防火牆和/或 proxy 中開啟特定 Defender for IoT 中樞的 FQDN。 如需詳細資訊，請參閱 [參考-IoT 中樞端點](../iot-hub/iot-hub-devguide-endpoints.md)。  

- **針對已連線到雲端的感應器**：啟用檔案有效，但 Defender for IoT 拒絕它。 如果您無法解決此問題，您可以從適用于 IoT 的 Defender 入口網站的 [ **感應器管理** ] 頁面下載另一個啟用。 如果無法運作，請聯絡 Microsoft 支援服務。

## <a name="manage-certificates"></a>管理憑證

在感應器安裝之後，會產生本機自我簽署憑證，並使用此憑證來存取感應器 web 應用程式。 當第一次登入感應器時，系統管理員使用者會收到提供 SSL/TLS 憑證的提示。  如需首次安裝的詳細資訊，請參閱登 [入並啟動感應器](how-to-activate-and-set-up-your-sensor.md)。

本文提供有關更新憑證、使用憑證 CLI 命令，以及支援的憑證和憑證參數的資訊。

### <a name="about-certificates"></a>關於憑證

適用于 IoT 的 Azure Defender 使用 SSL/TLS 憑證：

- 上傳 CA 簽署的憑證，以符合您組織所要求的特定憑證和加密需求。

- 允許在管理主控台和連線的感應器之間，以及在管理主控台與高可用性管理主控台之間進行驗證。 驗證會針對憑證撤銷清單和憑證到期日進行評估。 *如果驗證失敗，管理主控台和感應器之間的通訊會停止，並在主控台中顯示驗證錯誤*。 安裝之後，預設會啟用此選項。

 協力廠商轉送規則，例如傳送到 SYSLOG、Splunk 或 ServiceNow 的警示資訊;或與 Active Directory 的通訊不會經過驗證。

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

設備可以使用唯一的憑證檔案。 如果您需要取代已上傳的憑證，

- 從10.0 版開始，可以從 [系統設定] 功能表取代憑證。

- 針對10.0 之前的版本，可以使用命令列工具來取代 SSL 憑證。

### <a name="update-certificates"></a>更新憑證

感應器系統管理員使用者可以更新憑證。

若要更新憑證：  

1. 選取 [ **系統設定**]。

1. 選取 [ **SSL/TLS 憑證]。**
1. 刪除或編輯憑證，並新增一個新憑證。

    - 新增憑證名稱。
    
    - 上傳 CRT 檔案和金鑰檔，然後輸入複雜密碼。
    - 視需要上傳 PEM 檔案。

若要變更驗證設定：

1. 啟用或停用 [ **啟用憑證驗證** ] 切換。

1. 選取 [儲存]。

如果啟用此選項且驗證失敗，管理主控台和感應器之間的通訊會停止，並在主控台中顯示驗證錯誤。

### <a name="certificate-support"></a>憑證支援

以下是支援的憑證：

- 私用和企業金鑰基礎結構 (私用 PKI) 

- 公開金鑰基礎結構 (公開 PKI)  

- 在本機產生的設備 (本機自我簽署) 。 

> [!IMPORTANT]
> 我們不建議使用自我簽署憑證。 這種類型的連線不安全，而且只能用於測試環境。 因為無法驗證憑證的擁有者，而且無法維護系統的安全性，所以不應該將自我簽署憑證用於生產網路。

### <a name="supported-ssl-certificates"></a>支援的 SSL 憑證 

支援下列參數。 

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
- 主體 (OU) 組織單位 = 已定義，例如 Contoso Labs
- Subject (O) rganization = 已定義，例如 Contoso Inc.。

**金鑰檔**

- 建立 CSR 時產生的金鑰檔。

- RSA 2048 位 (最低) 或4096位。

 > [!Note]
 > 使用金鑰長度4096bits：
 > - 每個連線開始時的 SSL 交握將會變慢。  
 > - 在交握期間，CPU 使用率也會增加。 

**憑證鏈**

- 如果您的 CA 所提供的任何) ，中繼憑證檔案 (

- 發行伺服器憑證的 CA 憑證應該是檔案中的第一個，後面接著任何其他專案，跟根目錄一樣。 
- 可以包含包屬性。

**密碼**

- 支援一個金鑰。

- 當您匯入憑證時設定。

具有其他參數的憑證可能會運作，但 Microsoft 不支援它們。

#### <a name="encryption-key-artifacts"></a>加密金鑰構件

**pem –憑證容器檔案**

隱私權增強郵件 (PEM) 檔是用來保護電子郵件的一般檔案類型。 現今，PEM 檔案會與憑證搭配使用，並使用 x509 ASN1 金鑰。  

容器檔案定義于 Rfc 1421 至1424，這是一種可能只包含公用憑證的容器格式。 例如，Apache 安裝、CA 憑證、檔案等、SSL 或憑證。 這可能包含整個憑證鏈，包括公開金鑰、私密金鑰和根憑證。  

它也可以將 CSR 編碼為 PKCS10 格式，以轉譯成 PEM。

**.crt – certificate container file**

`.pem` `.der` 具有不同副檔名的或格式化檔案。 Windows 檔案總管將檔案辨識為憑證。 `.pem`   Windows 檔案總管不能辨識檔案。

**. 金鑰-私密金鑰檔案**

金鑰檔的格式與 PEM 檔案的格式相同，但副檔名不同。

#### <a name="additional-commonly-available-key-artifacts"></a>其他常用的主要構件

**。 csr-憑證簽署要求**。  

此檔案會用來提交給憑證授權單位單位。 實際的格式為 PKCS10 （定義于 RFC 2986 中），其中可能包含所要求憑證的部分或所有重要詳細資料。 例如，主體、組織和狀態。 它是由 CA 簽署的憑證公開金鑰，並會接收傳回的憑證。  

傳回的憑證是公開憑證，其中包含公開金鑰，但不包含私密金鑰。 

**pkcs12 .pfx – password 容器**。 

最初是由 RSA 在 Public-Key 加密標準 (PKCS) 中定義，這是 Microsoft 最初增強的12個變數，並在稍後提交為 RFC 7292。  

此容器格式需要同時包含公開和私用憑證組的密碼。 與檔案不同 `.pem`   的是，此容器已完全加密。  

您可以使用 OpenSSL 將它轉換成 `.pem`   具有公開和私密金鑰的檔案： `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**der –二進位編碼的 PEM**。

以二進位格式編碼 asn.1 語法的方法是透過檔案 `.pem`   ，這只是一個 Base64 編碼的檔案 `.der` 。 

OpenSSL 可將這些檔案轉換成 `.pem` ：  `openssl x509 -inform der -in to-convert.der -out converted.pem` 。  

Windows 會將這些檔案辨識為憑證檔案。 根據預設，Windows 會將憑證匯出為 `.der` 具有不同副檔名的格式化檔案。  

**crl-憑證撤銷清單**。  
憑證授權單位單位會產生這些檔案，以在憑證到期之前將憑證解除授權。
 
##### <a name="cli-commands"></a>CLI 命令

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

## <a name="connect-a-sensor-to-the-management-console"></a>將感應器連線到管理主控台

本節說明如何確保感應器和內部部署管理主控台之間的連接。 如果您是在有空調的網路中工作，而且想要從感應器將裝置和警示資訊傳送到管理主控台，就必須這樣做。 此連接也可讓管理主控台將系統設定推送至感應器，並在感應器上執行其他管理工作。

連接：

1. 登入內部部署管理主控台。

2. 選取 [ **系統設定**]。

3. 在 [ **感應器設定-連接字串** ] 區段中，複製自動產生的連接字串。

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="從這個畫面複製連接字串。"::: 

4. 登入感應器主控台。

5. 在左窗格中，選取 [ **系統設定**]。

6. 選取 [ **管理主控台** 連線]。

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="[管理主控台連線] 對話方塊的螢幕擷取畫面。":::

7. 在 [ **連接字串** ] 方塊中貼上連接字串，然後選取 **[連接]**。

8. 在內部部署管理主控台的 [ **網站管理** ] 視窗中，將感應器指派至區域。

## <a name="change-the-name-of-a-sensor"></a>變更感應器的名稱

您可以變更感應器主控台的名稱。 新名稱會出現在主控台網頁瀏覽器、各種主控台視窗和疑難排解記錄檔中。

變更感應器名稱的程式會因本機連線的感應器和連線到雲端的感應器而有所不同。 預設名稱為 **感應器**。

### <a name="change-the-name-of-a-locally-connected-sensor"></a>變更本機連線感應器的名稱

若要變更名稱：

1. 在主控台的左窗格底部，選取目前的感應器標籤。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="顯示感應器標籤的螢幕擷取畫面。":::

1. 在 [ **編輯感應器名稱** ] 對話方塊中，輸入名稱。

1. 選取 [儲存]。 套用新的名稱。

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>變更已連線到雲端的感應器名稱

如果您的感應器註冊為連接雲端的感應器，則感應器名稱是由註冊期間指派的名稱所定義。 當您第一次登入時，該名稱會包含在您上傳的啟用檔中。 若要變更感應器的名稱，您需要上傳新的啟用檔。

若要變更名稱：

1. 在適用于 IoT 的 Azure Defender 入口網站中，移至 [ **感應器管理** ] 頁面。

1. 從 [ **感應器管理** ] 視窗中刪除感應器。

1. 使用新名稱註冊。

1. 下載和新的啟用檔。

1. 登入感應器並上傳新的啟用檔案。

## <a name="update-the-sensor-network-configuration"></a>更新感應器網路設定

感應器網路設定是在感應器安裝期間定義。 您可以變更設定參數。 您也可以設定 proxy 設定。

如果您建立新的 IP 位址，可能需要再次登入。

若要變更設定：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **系統設定** ] 視窗中，選取 [ **網路**]。

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="設定您的網路設定。":::

3. 設定參數，如下所示：

    | 參數 | 描述 |
    |--|--|
    | IP 位址 | 感應器 IP 位址 |
    | 子網路遮罩 | 遮罩位址 |
    | 預設閘道 | 預設閘道位址 |
    | DNS | DNS 伺服器位址 |
    | Hostname (主機名稱) | 感應器主機名稱 |
    | Proxy | Proxy 主機和埠名稱 |

4. 選取 [儲存]。

## <a name="synchronize-time-zones-on-the-sensor"></a>同步處理感應器上的時區

您可以設定感應器的時間和區域，讓所有使用者都能看到相同的時間和區域。

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="設定時間和區域。":::

| 參數 | 描述 |
|--|--|
| 時區 | 時區定義：<br />-警示<br />-趨勢和統計資料小工具<br />-資料採礦報表<br />   -風險評量報告<br />-攻擊媒介 |
| 日期格式 | 選取下列其中一個格式選項：<br />-dd/MM/yyyy HH： MM： ss<br />-MM/dd/yyyy HH： MM： ss<br />-yyyy/MM/dd HH： MM： ss |
| 日期和時間 | 以您選取的格式顯示目前的日期和本地時間。<br />例如，如果您的實際位置是北美洲和紐約，但時區設定為歐洲和柏林，則會根據柏林當地時間顯示時間。 |

若要設定感應器時間：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **系統設定** ] 視窗中，選取 [ **時間 & 地區**]。

3. 設定參數，然後選取 [ **儲存**]。

## <a name="set-up-backup-and-restore-files"></a>設定備份和還原檔案

系統備份會在每天上午3:00 執行。 資料會儲存在感應器的不同磁片上。 預設位置為 `/var/cyberx/backups`。

您可以自動將此檔案傳送至內部網路。

> [!NOTE]
> - 您只能在相同版本之間執行備份和還原程式。
> - 在某些架構中，備份已停用。 您可以在檔案中啟用它 `/var/cyberx/properties/backup.properties` 。

當您使用內部部署管理主控台來控制感應器時，可以使用感應器的備份排程來收集這些備份，然後將它們儲存在管理主控台或外部備份伺服器上。

**備份的內容：** 設定和資料。

**未備份的內容：** PCAP 檔案和記錄檔。 您可以手動備份和還原 PCAPs 和記錄。

感應器備份檔案會自動以下列格式命名： `<sensor name>-backup-version-<version>-<date>.tar` 。 例如 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`。

若要設定備份：

- 登入系統管理帳戶，然後輸入 `$ sudo cyberx-xsense-system-backup` 。

若要還原最新的備份檔案：

- 登入系統管理帳戶，然後輸入 `$ sudo cyberx-xsense-system-restore` 。

若要將備份儲存至外部 SMB 伺服器：

1. 在外部 SMB 伺服器中建立共用資料夾。

    取得存取 SMB 伺服器所需的資料夾路徑、使用者名稱和密碼。

2. 在感應器中，建立備份的目錄：

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. 編輯 `fstab`： 

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. 編輯並建立要與 SMB 伺服器共用的認證：

    `sudo nano /etc/samba/user` 

5. 新增：

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. 掛接目錄：

    `sudo mount -a`

7. 設定備份目錄至適用于 IoT 的 Defender 上的共用資料夾感應器：  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>還原感應器

您可以從感應器主控台和使用 CLI 還原備份。

**若要從主控台還原：**

- 從感應器的 [**系統設定**] 視窗中選取 [**還原映射**]。

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="選取按鈕以還原您的映射。":::

主控台將會顯示還原失敗。

**若要使用 CLI 進行還原：**

- 登入系統管理帳戶，然後輸入 `$ sudo cyberx-management-system-restore` 。

## <a name="update-a-standalone-sensor-version"></a>更新獨立感應器版本

下列程式說明如何使用感應器主控台來更新獨立感應器。 更新程式大約需要30分鐘的時間。

1. 前往 [Azure 入口網站](https://portal.azure.com/)。

2. 前往 Defender for IoT。

3. 移至 [ **更新** ] 頁面。

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="適用于 IoT Defender Defender 更新頁面的螢幕擷取畫面。":::

4. 從 [**感應器**] 區段中選取 [**下載**]，然後儲存檔案。

5. 在感應器主控台的側邊欄中，選取 [ **系統設定**]。

6. 在 [ **版本升級** ] 窗格中，選取 [ **升級**]。

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="[升級] 窗格的螢幕擷取畫面。":::

7. 選取您從 [Defender for IoT **更新** ] 頁面下載的檔案。

8. 更新程式隨即啟動，在這段期間系統會重新開機兩次。 第一次重新開機之後 () 更新程式完成之前，系統會以登入視窗開啟。 登入之後，升級版本會顯示在提要欄位的左下方。

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="登入後出現的升級版本螢幕擷取畫面。":::

## <a name="forward-sensor-failure-alerts"></a>轉寄感應器失敗警示 

您可以將警示轉寄給協力廠商，以提供下列詳細資料：

- 已中斷連線的感應器

- 遠端備份失敗

當您建立系統通知的轉送規則時，就會傳送這項資訊。

> [!NOTE]
> 系統管理員可以傳送系統通知。

若要傳送通知：

1. 登入內部部署管理主控台。
1. 從側邊功能表選取 [ **轉送** ]。
1. 建立轉送規則。
1. 選取 [ **報告系統通知**]。

如需轉送規則的詳細資訊，請參閱 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。

## <a name="adjust-system-properties"></a>調整系統屬性

系統屬性會控制感應器中的各種作業和設定。 編輯或修改它們可能會損毀感應器主控台的操作。

變更您的設定之前，請先參閱 [Microsoft 支援服務](https://support.microsoft.com/) 。

若要存取系統屬性：

1. 登入內部部署管理主控台或感應器。

2. 選取 [ **系統設定**]。

3. 從 [**一般**] 區段中選取 [**系統屬性**]。

## <a name="see-also"></a>另請參閱

[威脅情報研究和套件](how-to-work-with-threat-intelligence-packages.md)

[從管理主控台管理感應器](how-to-manage-sensors-from-the-on-premises-management-console.md)