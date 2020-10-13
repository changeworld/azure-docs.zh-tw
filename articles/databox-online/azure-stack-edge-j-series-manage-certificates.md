---
title: 使用憑證搭配 Azure Stack Edge Pro GPU |Microsoft Docs
description: 說明如何使用 Azure Stack Edge Pro GPU 裝置的憑證，包括使用原因、哪些類型，以及如何在您的裝置上傳憑證。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: 5cec5eda7cf398949865bf6d3d3e8be5b2d5e840
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767359"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>使用憑證搭配 Azure Stack Edge Pro GPU 裝置

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明可安裝在 Azure Stack Edge Pro 裝置上的憑證類型。 本文也包含每個憑證類型的詳細資料，以及安裝和識別到期日的程式。 

## <a name="about-certificates"></a>關於憑證

憑證會提供**公開金鑰**和實體 (之間的連結，例如已簽署 (由受信任的協力廠商 (（如**憑證授權單位**單位) ）) **簽署**的功能變數名稱) 。  憑證提供便利的方式來散發受信任的公用加密金鑰。 憑證可確保您的通訊是受信任的，且您要將加密資訊傳送到正確的伺服器。 

最初設定 Azure Stack Edge Pro 裝置時，會自動產生自我簽署憑證。 （選擇性）您可以攜帶自己的憑證。 如果您打算攜帶您自己的憑證，您必須遵循下列指導方針。

## <a name="types-of-certificates"></a>憑證的類型

您 Azure Stack Edge Pro 裝置上使用的各種憑證類型如下所示： 
- 簽署憑證
    - 根 CA
    - 中級

- 端點憑證
    - 節點憑證
    - 本機 UI 憑證
    - Azure Resource Manager 憑證
    - Blob 儲存體憑證
    - IoT 裝置憑證
    <!--- WiFi certificates
    - VPN certificates-->

- 加密憑證
    - 支援會話憑證

下列各節將詳細說明每個憑證。

## <a name="signing-chain-certificates"></a>簽署鏈憑證

這些是簽署憑證或簽署憑證授權單位單位之授權單位的憑證。 

### <a name="types"></a>型別

這些憑證可以是根憑證或中繼憑證。 根憑證一律會自我簽署 (或自行簽署) 。 中繼憑證不是自我簽署的憑證，而且是由簽署授權單位簽署。

### <a name="caveats"></a>警示

- 根憑證應簽署鏈證書。
- 您可以用下列格式將根憑證上傳至您的裝置： 
    - **DER** –以副檔名的形式提供 `.cer` 。
    - 以**64 為基礎的編碼**-這些都是以 `.cer` 副檔名提供。
    - **P7b** –此格式僅用於包含根憑證和中繼憑證的簽署鏈憑證。
- 簽署鏈憑證一律會上傳，然後再上傳任何其他憑證。


## <a name="node-certificates"></a>節點憑證

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> 裝置中的所有節點會不斷地彼此通訊，因此需要有信任關係。 節點憑證提供建立該信任的方式。 當您使用透過 HTTPs 的遠端 PowerShell 會話連線到裝置節點時，節點憑證也會開始播放。

### <a name="caveats"></a>警示

- 應以 `.pfx` 可匯出的私密金鑰格式提供節點憑證。 
- 您可以建立並上傳1個萬用字元節點憑證或4個個別節點憑證。 
- 如果 DNS 網域變更但裝置名稱不會變更，則必須變更節點憑證。 如果您要攜帶自己的節點憑證，則無法變更裝置序號，只能變更功能變數名稱。
- 您可以使用下表來引導您建立節點憑證。
   
    |類型 | (SN 的主體名稱)   | (SAN) 的主體替代名稱  |主體名稱範例 |
    |---------|---------|---------|---------|
    |節點|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>端點憑證

針對裝置所公開的任何端點，信任通訊需要憑證。 端點憑證包含透過 REST Api 存取 Azure Resource Manager 和 blob 儲存體時所需的憑證。 

當您帶入自己的已簽署憑證時，您也需要憑證的對應簽署鏈。 針對簽署鏈、Azure Resource Manager，以及裝置上的 blob 憑證，您必須在用戶端電腦上使用對應的憑證來驗證和與裝置通訊。

### <a name="caveats"></a>警示

- 端點憑證的格式必須是 `.pfx` 私密金鑰。 簽署鏈應為 DER 格式 (`.cer` 副檔名) 。 
- 當您攜帶自己的端點憑證時，這些憑證可以是個別憑證或多網域憑證。 
- 如果您要帶入簽署鏈，必須先上傳簽署鏈憑證，然後才能上傳端點憑證。
- 如果裝置名稱或 DNS 功能變數名稱變更，則必須變更這些憑證。
- 您可以使用萬用字元端點憑證。
- 端點憑證的屬性類似于一般 SSL 憑證的屬性。 
- 建立端點憑證時，請使用下表：

    |類型 | (SN 的主體名稱)   | (SAN) 的主體替代名稱  |主體名稱範例 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 儲存體|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |這兩個端點的多重 SAN 單一憑證|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>本機 UI 憑證

您可以透過瀏覽器存取裝置的本機 web UI。 為了確保這項通訊安全，您可以上傳自己的憑證。 

### <a name="caveats"></a>警示

- 本機 UI 憑證也會以 `.pfx` 具有可匯出之私密金鑰的格式上傳。
- 上傳本機 UI 憑證之後，您必須重新開機瀏覽器，並清除快取。 請參閱瀏覽器的特定指示。

    |類型 | (SN 的主體名稱)   | (SAN) 的主體替代名稱  |主體名稱範例 |
    |---------|---------|---------|---------|
    |本機 UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge 裝置憑證

您的 Azure Stack Edge Pro 裝置也是 IoT 裝置，其中的計算是由與其連接的 IoT Edge 裝置所啟用。 針對此 IoT Edge 裝置與可能連接的下游裝置之間的任何安全通訊，您也可以上傳 IoT Edge 憑證。 

如果您只想要在裝置上使用計算案例，則裝置具有自我簽署憑證可供使用。 但是，如果 Azure Stack Edge Pro 裝置已連線到下游裝置，您就必須攜帶自己的憑證。

您需要安裝三個 IoT Edge 憑證，才能啟用此信任關係：

- **根憑證授權單位或擁有者憑證授權單位單位**
- **裝置憑證授權單位單位** 
- **裝置金鑰憑證**

### <a name="caveats"></a>警示

- IoT Edge 的憑證會以格式上傳 `.pem` 。 


如需 IoT Edge 憑證的詳細資訊，請參閱 [Azure IoT Edge 憑證詳細資料](../iot-edge/iot-edge-certs.md#iot-edge-certificates)。

## <a name="support-session-certificates"></a>支援會話憑證

如果您的 Azure Stack Edge Pro 裝置遇到任何問題，若要針對這些問題進行疑難排解，可能會在裝置上開啟遠端 PowerShell 支援會話。 若要在此支援會話上啟用安全且加密的通訊，您可以上傳憑證。

### <a name="caveats"></a>警示

- 請確定 `.pfx` 使用解密工具在用戶端電腦上安裝具有私密金鑰的對應憑證。
- 確認憑證的 [ **金鑰使用** 方法] 欄位不是 **憑證簽署**。 若要確認這一點，請以滑鼠右鍵按一下憑證，選擇 [ **開啟** ]，然後在 [ **詳細資料** ] 索引標籤中尋找 **金鑰使用**方式 


### <a name="caveats"></a>警示

- 支援會話憑證必須以副檔名提供的 DER 格式提供 `.cer` 。


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a> (選用) 建立憑證

下一節說明建立簽署鏈和端點憑證的程式。

### <a name="certificate-workflow"></a>憑證工作流程

您將有定義的方法，可為環境中運作的裝置建立憑證。 您可以使用 IT 系統管理員提供給您的憑證。 

**若僅供開發或測試之用，您也可以使用 Windows PowerShell 在本機系統上建立憑證。** 建立用戶端的憑證時，請遵循下列指導方針：

1. 您可以建立下列類型的憑證：

    - 建立單一憑證，以用於單一完整功能變數名稱 (FQDN) 。 例如， *mydomain.com*。
    - 建立萬用字元憑證，以保護主要功能變數名稱和多個子網域。 例如，**. mydomain.com*。
    - 建立 (SAN) 憑證的主體別名，此憑證將涵蓋單一憑證中的多個功能變數名稱。 

2. 如果您要攜帶自己的憑證，您將需要簽署鏈的根憑證。 請參閱 [建立簽署鏈憑證](#create-signing-chain-certificate)的步驟。

3. 您接下來可以為設備、blob 和 Azure Resource Manager 的本機 UI 建立端點憑證。 您可以為設備、blob 和 Azure Resource Manager 建立3個不同的憑證，也可以為所有3個端點建立一個憑證。 如需詳細步驟，請參閱 [建立簽署和端點憑證](#create-signed-endpoint-certificates)。

4. 無論您要建立3個不同的憑證或一個憑證，請根據每個憑證類型所提供的指引，指定主體名稱 (SN) 和主體別名 (SAN) 。 

### <a name="create-signing-chain-certificate"></a>建立簽署鏈憑證

透過以系統管理員模式執行的 Windows PowerShell 來建立這些憑證。 **以這種方式建立的憑證應該僅用於開發或測試用途。**

簽署鏈憑證只需要建立一次。 其他端點憑證會參考此憑證進行簽署。
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>建立已簽署的端點憑證

透過以系統管理員模式執行的 Windows PowerShell 來建立這些憑證。

在這些範例中，會使用下列程式為裝置建立端點憑證：
    - 裝置名稱： `DBE-HWDC1T2`
    - DNS 網域： `microsoftdatabox.com`

以您裝置的名稱和 DNS 網域取代，以建立裝置的憑證。
 
**Blob 端點憑證**

在您的個人存放區中建立 Blob 端點的憑證。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager 端點憑證**

在您的個人存放區中建立 Azure Resource Manager 端點的憑證。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**裝置本機 web UI 憑證**

在您的個人存放區中建立裝置本機 web UI 的憑證。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**適用于所有端點的單一多重 SAN 憑證**

為您個人存放區中的所有端點建立單一憑證。

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

建立憑證後，下一個步驟是將憑證上傳到您的 Azure Stack Edge Pro 裝置上


## <a name="upload-certificates"></a>上傳憑證 

您為裝置建立的憑證預設位於用戶端的 **個人存放區** 中。 您必須在用戶端上將這些憑證匯出到適當的格式檔案，然後再上傳至您的裝置。

1. 根憑證必須匯出為副檔名為 DER 的 DER 格式 `.cer` 。 如需詳細步驟，請參閱將 [憑證匯出為 DER 格式](#export-certificates-as-der-format)。
2. 端點憑證必須以具有私密金鑰的 *.pfx* 檔案匯出。 如需詳細步驟，請參閱 [使用私密金鑰將憑證匯出為 *.pfx* ](#export-certificates-as-pfx-format-with-private-key)檔案。 
3. 然後，會使用本機 web UI 中 [憑證] 頁面上的 [ **+ 新增憑證** ] 選項，在裝置上傳根目錄和端點憑證。 

    1. 先上傳根憑證。 在本機 web UI 中，移至 [ **憑證 > + 新增憑證**]。

        ![新增簽署鏈憑證1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. 接下來上傳端點憑證。 

        ![新增簽署鏈憑證2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        選擇 *.pfx* 格式的憑證檔案，然後輸入您在匯出憑證時所提供的密碼。 Azure Resource Manager 憑證可能需要幾分鐘的時間才會套用。

        如果未先更新簽署鏈，而您嘗試上傳端點憑證，您將會收到錯誤訊息。

        ![套用憑證錯誤](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        返回並上傳簽署鏈憑證，然後上傳並套用端點憑證。

> [!IMPORTANT]
> 如果裝置名稱或 DNS 網域已變更，則必須建立新的憑證。 接著，您應該使用新的裝置名稱和 DNS 網域來更新用戶端憑證和裝置憑證。 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>在存取裝置的用戶端上匯入憑證

您建立並上傳至裝置的憑證必須匯入 Windows 用戶端上， (將裝置) 存取適當的憑證存放區。

1. 您匯出為 DER 的根憑證現在應該匯入用戶端系統上 **受信任的根憑證授權** 單位。 如需詳細步驟，請參閱 [將憑證匯入至受信任的根憑證授權單位存放區](#import-certificates-as-der-format)。

2. 您匯出為的端點憑證 `.pfx` 必須匯出為副檔名為 DER 的 DER `.cer` 。 `.cer`然後，這會匯入您系統上的**個人憑證存儲**中。 如需詳細步驟，請參閱 [將憑證匯入至個人憑證存儲](#import-certificates-as-der-format)。

### <a name="import-certificates-as-der-format"></a>將憑證匯入為 DER 格式

若要在 Windows 用戶端上匯入憑證，請執行下列步驟：

1. 以滑鼠右鍵按一下檔案，然後選取 [ **安裝憑證**]。 這個動作會啟動 [憑證匯入精靈]。

    ![匯入憑證 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. 針對 [ **存放區位置**]，選取 [ **本機電腦**]，然後選取 **[下一步]**。

    ![匯入憑證 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. 選取 **[將所有憑證放入以下的存放區**]，然後選取 **[流覽]**。 

    - 若要匯入到個人存放區，請流覽至遠端主機的個人存放區，然後選取 **[下一步]**。

        ![匯入憑證4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - 若要匯入至信任的存放區，請流覽至信任的根憑證授權單位，然後選取 **[下一步]**。

        ![匯入憑證3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. 選取 [完成]。 顯示匯入成功的結果訊息。

### <a name="export-certificates-as-pfx-format-with-private-key"></a>使用私密金鑰將憑證匯出為 .pfx 格式

採取下列步驟，在 Windows 電腦上匯出具有私密金鑰的 SSL 憑證。 

> [!IMPORTANT]
> 在您用來建立憑證的同一部電腦上執行這些步驟。 

1. 執行 *certlm.msc* ，以啟動本機電腦憑證存放區。

1. 按兩下 [ **個人** ] 資料夾，然後按一下 [ **憑證**]。

    ![匯出憑證1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. 以滑鼠右鍵按一下您想要備份的憑證，然後選擇 [ **所有工作] > 匯出 ...**

    ![匯出憑證2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. 遵循憑證匯出嚮導，將憑證備份至 .pfx 檔案。

    ![匯出憑證3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. 選擇 [ **是，匯出私密金鑰]**。

    ![匯出憑證4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. 選擇 [ **包含憑證路徑中的所有憑證] （如果可能的話**）， **匯出所有擴充屬性** 並 **啟用憑證隱私權**。 

    > [!IMPORTANT]
    > **如果匯出成功**，請勿選取 [刪除私密金鑰] 選項。

    ![匯出憑證5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. 輸入您將記得的密碼。 確認密碼。 密碼會保護私密金鑰。

    ![匯出憑證6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. 選擇將檔案儲存在設定的位置。

    ![匯出憑證7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. 選取 [完成]。

    ![匯出憑證8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. 您會收到已成功匯出的訊息。 選取 [確定]  。

    ![匯出憑證9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

.Pfx 檔案備份現在會儲存在您選取的位置，並準備好移動或儲存以供您安全的保留。


### <a name="export-certificates-as-der-format"></a>將憑證匯出為 DER 格式

1. 執行 *certlm.msc* ，以啟動本機電腦憑證存放區。

1. 在 [個人] 憑證存放區中，選取根憑證。 以滑鼠右鍵按一下並選取 [所有工作] **> 匯出 ...**

    ![匯出憑證 DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. 憑證嚮導隨即開啟。 選取格式為 **DER 編碼的二進位 x.509 ( .cer) **。 選取 [下一步]  。

    ![匯出憑證 DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. 流覽並選取您要匯出 .cer 格式檔案的位置。

    ![匯出憑證 DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. 選取 [完成]。

    ![匯出憑證 DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>支援的憑證演算法

 Azure Stack Edge Pro 裝置只支援 Rivest – Shamir – Adleman (RSA) 憑證。 如果使用橢圓曲線數位簽章演算法 (ECDSA) 憑證，則裝置行為不確定。

 包含 RSA 公開金鑰的憑證稱為 RSA 憑證。 包含橢圓曲線密碼編譯 (ECC) 公開金鑰的憑證，稱為 ECDSA (橢圓曲線數位簽章演算法) 憑證。 


## <a name="view-certificate-expiry"></a>查看憑證到期日

如果您攜帶自己的憑證，憑證通常會在1年或6個月內到期。 若要查看憑證的到期日，請移至裝置本機 web UI 中的 [ **憑證** ] 頁面。 如果您選取特定的憑證，您可以查看憑證的到期日。

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates](azure-stack-edge-j-series-manage-certificates.md).-->

## <a name="next-steps"></a>後續步驟

[部署 Azure Stack Edge Pro 裝置](azure-stack-edge-gpu-deploy-prep.md)
