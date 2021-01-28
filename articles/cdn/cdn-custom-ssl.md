---
title: 教學課程：在 Azure CDN 自訂網域上設定 HTTPS
description: 在本教學課程中，您將了解如何在 Azure CDN 端點自訂網域上啟用和停用 HTTPS。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c4ad270b989e0e212c1d362ae4bfafc91fe07f3e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943562"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>教學課程：在 Azure CDN 自訂網域上設定 HTTPS

本教學課程示範如何針對與 Azure CDN 端點相關聯的自訂網域，啟用 HTTPS 通訊協定。 

您自訂網域上的 HTTPS 通訊協定 (例如，HTTPs： \/ /www.contoso.com) ，可確保您的機密資料會透過 TLS/SSL 安全地傳遞。 當您的網頁瀏覽器透過 HTTPS 連線時，瀏覽器會驗證網站的憑證。 瀏覽器會確認它是由合法的憑證授權單位單位所發行。 此程序可提供安全性，並讓 Web 應用程式免於遭受攻擊。

Azure CDN 預設支援 CDN 端點主機名稱上的 HTTPS。 舉例來說，當您建立 CDN 端點時 (例如 https:\//contoso.azureedge.net) ，會自動啟用 HTTPS。  

自訂 HTTPS 功能的一些重要特色如下：

- 無額外費用：憑證取得或更新不會產生任何費用，且 HTTPS 流量不會產生額外費用。 您只需支付 CDN 的 GB 輸出。

- 容易啟用：從 [Azure 入口網站](https://portal.azure.com)按一下就能佈建。 您也可以使用 REST API 或其他開發工具來啟用此功能。

- 可使用完整憑證管理： 
    * 為您處理所有憑證採購及管理。 
    * 憑證會在到期前自動布建及更新。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 在您的自訂網域上啟用 HTTPS 通訊協定。
> - 使用 CDN 管理的憑證 
> - 使用您自己的憑證
> - 驗證網域
> - 在您的自訂網域上停用 HTTPS 通訊協定。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

您必須建立 CDN 設定檔和至少一個 CDN 端點，才能完成本教學課程中的步驟。 如需詳細資訊，請參閱[快速入門：建立 Azure CDN 設定檔和端點](cdn-create-new-endpoint.md)。

在您的 CDN 端點上建立 Azure CDN 自訂網域的關聯。 如需詳細資訊，請參閱[教學課程：將自訂網域新增至 Azure CDN 端點](cdn-map-content-to-custom-domain.md)。

> [!IMPORTANT]
> CDN 受控憑證不適用於根或頂點網域。 如果您的 Azure CDN 自訂網域是根或頂點網域，則您必須使用「使用您自己的憑證」功能。 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL 憑證

若要在 Azure CDN 自訂網域上啟用 HTTPS，請使用 TLS/SSL 憑證。 您可以選擇使用 Azure CDN 所管理的憑證，或使用您的憑證。

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[選項 1 (預設值)：使用 CDN 管理的憑證啟用 HTTPS](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN 會處理憑證管理工作，例如採購和續約。 啟用此功能之後，程序就會立即啟動。 

如果自訂網域已對應至 CDN 端點，則不需要採取任何進一步的動作。 Azure CDN 會處理相關步驟並自動完成您的要求。

如果您的自訂網域已對應到其他位置，請使用電子郵件來驗證您的網域擁有權。

依照下列步驟啟用自訂網域的 HTTPS︰

1. 請移至 [Azure 入口網站](https://portal.azure.com)，尋找您的 Azure CDN 所管理的憑證。 搜尋並選取 [CDN 設定檔]  。 

2. 選擇您的設定檔：
    * **來自 Microsoft 的 Azure CDN 標準**
    * **來自 Akamai 的 Azure CDN 標準**
    * **來自 Verizon 的 Azure CDN 標準**
    * **來自 Verizon 的 Azure CDN Premium**

3. 在 CDN 端點清單中，選取包含自訂網域的端點。

    ![端點清單](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    [端點]  頁面隨即出現。

4. 在自訂網域清單中，選取您要啟用 HTTPS 的自訂網域。

    ![顯示 [自訂網域] 頁面的螢幕擷取畫面，此頁面包含 [使用我自己的憑證] 的選項。](./media/cdn-custom-ssl/cdn-custom-domain.png)

    [自訂網域]  頁面隨即出現。

5. 在憑證管理類型底下，選取 [CDN 管理]  。

6. 選取 [開啟]  以啟用 HTTPS。

    ![自訂網域 HTTPS 狀態](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. 繼續 [驗證網域](#validate-the-domain)。


# <a name="option-2-enable-https-with-your-own-certificate"></a>[選項 2：使用您自己的憑證啟用 HTTPS](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> 此選項僅適用於 **來自 Microsoft 的 Azure CDN** 和 **來自 Verizon 的 Azure CDN** 設定檔。 
>
 
您可以使用自己的憑證啟用 HTTPS 功能。 此程序會透過與 Azure Key Vault 的整合來進行，而讓您可以安全地儲存您的憑證。 Azure CDN 會使用此安全機制來取得您的憑證，而且需要一些額外的步驟。 當您建立 TLS/SSL 憑證時，您必須使用允許的憑證授權單位 (CA) 來加以建立。 否則，如果您使用非允許的 CA，系統會拒絕您的要求。 如需允許 CA 的清單，請參閱[可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位](cdn-troubleshoot-allowed-ca.md)。 針對 **來自 Verizon 的 AZURE CDN**，將會接受任何有效的 CA。 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>準備您的 Azure Key Vault 帳戶和憑證
 
1. Azure Key Vault：在與您想要啟用自訂 HTTPS 的 Azure CDN 設定檔和 CDN 端點相同的訂用帳戶底下，您必須有執行中的 Azure Key Vault 帳戶。 建立 Azure Key Vault 帳戶 (如果您還沒有的話)。
 
2. Azure Key Vault 憑證：如果您有憑證，請將它直接上傳至您的 Azure Key Vault 帳戶。 如果您沒有憑證，請透過 Azure Key Vault 直接建立新憑證。

### <a name="register-azure-cdn"></a>註冊 Azure CDN

透過 PowerShell，將 Azure CDN 註冊為 Azure Active Directory 中的應用程式。

1. 如有需要，請在本機電腦上安裝 [Azure PowerShell](/powershell/azure/install-az-ps)。

2. 在 PowerShell 中執行下列命令：

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** 是 **AzureFrontDoor-Cdn** 的服務主體。

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>為 Azure CDN 授與金鑰保存庫的存取權
 
授與 Azure CDN 權限，以存取您 Azure Key Vault 帳戶中的憑證 (祕密)。

1. 在您的金鑰保存庫中，選取 [ **設定** ] 區段中的 [ **存取原則**]。 在右窗格中，選取 [ **+ 新增存取原則**：

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="建立 CDN 的 keyvault 存取原則" border="true":::

2. 在 [**新增存取原則**] 頁面中，選取 [**選取主體**] 旁的 [**無** 選取]。 在 [ **主體** ] 頁面中，輸入 **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**。 選取 [ **AzureFrontdoor]-[Cdn**]。  選擇 [ **選取**]：

2. 在 [ **選取主體**] 中，搜尋 **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**，選擇 [ **AzureFrontDoor-Cdn**]。 選擇 [選取]  。

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="選取 Azure CDN 的服務主體" border="true":::
    
3. 選取 [ **憑證許可權**]。 選取 [ **取得** ] 和 [ **清單** ] 的核取方塊，以允許 CDN 取得和列出憑證的許可權。

4. 選取 [ **秘密許可權**]。 選取 [ **取得** ] 和 [ **清單** ] 的核取方塊，以允許 CDN 取得和列出秘密的許可權：

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="選取 CDN 至 keyvault 的許可權" border="true":::

5. 選取 [新增]。 

    Azure CDN 現在可以存取此金鑰保存庫和此金鑰保存庫中儲存的憑證 (祕密)。
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>選取讓 Azure CDN 進行部署的憑證
 
1. 返回 Azure CDN 入口網站，然後選取您要啟用自訂 HTTPS 的設定檔和 CDN 端點。 

2. 在自訂網域清單中，選取您要啟用 HTTPS 的自訂網域。

    [自訂網域]  頁面隨即出現。

3. 在憑證管理類型底下，選取 [使用我自己的憑證]  。 

    ![設定您的憑證](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. 選取金鑰保存庫、憑證 (祕密) 和憑證版本。

    Azure CDN 列出下列資訊： 
    - 您的訂用帳戶識別碼的金鑰保存庫帳戶。 
    - 所選金鑰保存庫底下的憑證 (密碼)。 
    - 可用的憑證版本。 
 
5. 選取 [開啟]  以啟用 HTTPS。
  
6. 當您使用憑證時，不需要進行網域驗證。 繼續 [等待傳播](#wait-for-propagation)。

---

## <a name="validate-the-domain"></a>驗證網域

如果您使用的自訂網域對應至具有 CNAME 記錄的自訂端點，或您使用自己的憑證，請繼續進行 [自訂網域對應至您的 CDN 端點](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)。 

否則，如果您端點的 CNAME 記錄專案已不存在或包含 cdnverify 子域，請繼續前往 [未對應至 CDN 端點的自訂網域](#custom-domain-isnt-mapped-to-your-cdn-endpoint)。

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>自訂網域已經由 CNAME 記錄對應至您的 CDN 端點

當您將自訂網域新增至端點時，會在對應至 CDN 端點主機名稱的 DNS 網域註冊機構中建立 CNAME 記錄。 

如果該 CNAME 記錄仍然存在且不包含 cdnverify 子域，則 DigiCert CA 會使用它來自動驗證您自訂網域的擁有權。 

如果您要使用自己的憑證，則不需要進行網域驗證。

您的 CNAME 記錄應該採用下列格式：

* *名稱* 是您的自訂功能變數名稱。
* *值* 是您的 CDN 端點主機名稱。

| 名稱            | 類型  | 值                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

如需有關 CNAME 記錄的詳細資訊，請參閱[建立 CNAME DNS 記錄](./cdn-map-content-to-custom-domain.md)。

如果您的 CNAME 記錄格式正確，DigiCert 就會自動驗證您的自訂功能變數名稱，並建立您網域的憑證。 DigitCert 不會傳送驗證電子郵件給您，因此您不需要核准您的要求。 憑證有效期限為一年，並且會在到期之前自動更新。 繼續 [等待傳播](#wait-for-propagation)。 

自動驗證通常需要幾小時。 如果您沒有看到您的網域在 24 小時內進行驗證，請開啟支援票證。

>[!NOTE]
>如果您具有 DNS 提供者的憑證授權單位授權 (CAA) 記錄，它必須包括 DigiCert 作為有效的 CA。 CAA 記錄可讓網域擁有者透過其 DNS 提供者，指定哪些 CA 有權為其網域發行憑證。 如果 CA 收到具有 CAA 記錄之網域的憑證訂單，而且該 CA 未列出為授權簽發者，則禁止該 CA 將憑證發行給該網域或子網域。 如需管理 CAA 記錄的相關資訊，請參閱[管理 CAA 記錄](https://support.dnsimple.com/articles/manage-caa-record/)。 有關 CAA 記錄工具，請參閱 [CAA 記錄協助程式](https://sslmate.com/caa/)。

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>自訂網域未對應至您的 CDN 端點

>[!NOTE]
>如果您使用 **Akamai 中的 Azure CDN**，則應該設定下列 CNAME 以啟用自動網域驗證。 "_acme-challenge.&lt;自訂網域主機名稱&gt; -> CNAME -> &lt;自訂網域主機名稱&gt;.ak-acme-challenge.azureedge.net"

如果 CNAME 記錄項目包含 cdnverify 子網域，請依照此步驟中的其餘指示進行操作。

DigiCert 會將驗證電子郵件傳送至下列電子郵件地址。 請確認您可以直接從下列其中一個地址核准：

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

您應該會在幾分鐘內收到電子郵件，讓您核准要求。 如果您使用垃圾郵件篩選器，請將 verification@digicert.com 其新增至其允許清單。 如果您未在 24 小時內收到驗證電子郵件，請連絡 Microsoft 支援服務。
    
![網域驗證電子郵件](./media/cdn-custom-ssl/domain-validation-email.png)

當您選取核准連結時，系統會將您導向下列線上核准表單： 
    
![網域驗證表單](./media/cdn-custom-ssl/domain-validation-form.png)

遵循表單上的指示；您有兩個驗證選項：

- 您可以核准未來所有經相同帳戶針對同一個根網域 (如 contoso.com) 的所有訂購。 如果您打算為相同的根域新增其他自訂網域，則建議使用此方法。

- 您可以只核准這次要求使用的特定主機名稱。 需要其他核准才能進行後續要求。

核准之後，DigiCert 會針對您的自訂網域名稱完成憑證建立。 憑證有效期限為一年，並且會在到期之前自動更新。

## <a name="wait-for-propagation"></a>等待傳播

自訂網域 HTTPS 功能要在網域名稱通過驗證之後 6-8 小時才會啟用。 當進程完成時，Azure 入口網站中的自訂 HTTPS 狀態會變更為 [ **已啟用**]。 [自訂網域] 對話方塊中的四個作業步驟會標示為完成。 您的自訂網域現在已準備好使用 HTTPS。

![[啟用 HTTPS] 對話方塊](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>作業進度

下表列出停用 HTTPS 時的作業進度。 啟用 HTTPS 之後，四個作業步驟會出現在 [自訂網域] 對話方塊中。 當每個步驟變成作用中時，其他子步驟的詳細資料會在步驟進行時顯示。 並非所有這些子步驟都會發生。 各個步驟完成時，旁邊都會出現一個綠色的核取記號。 

| 作業步驟 | 作業子步驟詳細資料 | 
| --- | --- |
| 1 提交要求 | 提交要求 |
| | 正在提交您的 HTTPS 要求。 |
| | 已成功提交您的 HTTPS 要求。 |
| 2 網域驗證 | 如果網域是對應至 CDN 端點的 CNAME，則會自動驗證網域。 否則，系統會將驗證要求傳送到您的網域註冊記錄 (WHOIS 登記者) 中所列的電子郵件。|
| | 已成功驗證您的網域擁有權。 |
| | 網域擁有權驗證要求已過期 (客戶可能未在 6 天內回應)。 您的網域將不會啟用 HTTPS。 * |
| | 客戶拒絕網域擁有權驗證要求。 您的網域將不會啟用 HTTPS。 * |
| 3 憑證佈建 | 憑證授權單位正在發行在網域上啟用 HTTPS 所需的憑證。 |
| | 憑證已發行，目前正在部署到 CDN 網路。 這可能需要 6 小時的時間。 |
| | 已成功將憑證部署到 CDN 網路。 |
| 4 完成 | 已成功在您的網域上啟用 HTTPS。 |

\* 除非發生錯誤，否則不會出現這則訊息。 

如果錯誤發生於提交要求之前，則會顯示下列錯誤訊息：

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>清除資源 - 停用 HTTPS

在本節中，您會瞭解如何針對您的自訂網域停用 HTTPS。

### <a name="disable-the-https-feature"></a>停用 HTTPS 功能 

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋並選取 [CDN 設定檔]  。 

2. 選擇 **來自 Microsoft 的標準 Azure CDN**、**來自 Verizon 的標準 Azure CDN** 或 **來自 Verizon 的進階 Azure CDN** 設定檔。

3. 在端點清單中，挑選包含自訂網域的端點。

4. 選擇您要停用 HTTPS 的自訂網域。

    ![自訂網域清單](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. 選擇 [關閉]  以停用 HTTPS，然後選取 [套用]  。

    ![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>等待傳播

自訂網域 HTTPS 功能停用之後，可能需要 6-8 小時才會生效。 當程式完成時，Azure 入口網站中的自訂 HTTPS 狀態會變更為 [ **已停用**]。 [自訂網域] 對話方塊中的三個作業步驟會標示為完成。 您的自訂網域無法再使用 HTTPS。

![[停用 HTTPS] 對話方塊](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>作業進度

下表指出停用 HTTPS 時的作業進度。 停用 HTTPS 之後，三個作業步驟會出現在 [自訂網域] 對話方塊中。 當步驟變成作用中時，詳細資料會出現在步驟下方。 各個步驟完成時，旁邊都會出現一個綠色的核取記號。 

| 作業進度 | 作業詳細資料 | 
| --- | --- |
| 1 提交要求 | 正在提交您的要求 |
| 2 憑證取消佈建 | 刪除憑證 |
| 3 完成 | 已憑證刪除 |

## <a name="frequently-asked-questions"></a>常見問題集

1. *憑證提供者是誰？使用的是哪一種憑證？*

    Digicert 提供的專用憑證適用于您的自訂網域，用於：
    
    * **Azure CDN from Verizon**
    * **來自 Microsoft 的 Azure CDN**

2. *您使用 IP 型或 SNI TLS/SSL？*

    **來自 Verizon 的 Azure CDN** 與 **來自 Microsoft 的標準 Azure CDN** 都會使用 SNI TLS/SSL。

3. *如果沒有收到 DigiCert 的驗證電子郵件該怎麼辦？*

    如果您未使用 *cdnverify* 子域，而您的「您的」您的「CNAME」專案適用于您的端點主機名稱，您就不會收到網域驗證
     
    驗證會自動進行。 否則，如果您沒有 CNAME 項目且未在 24 小時內收到電子郵件，請連絡 Microsoft 支援服務。

4. *SAN 憑證的安全性是否比專用憑證來得低？*
    
    SAN 憑證遵循和專用憑證相同的加密與安全性標準。 所有發行的 TLS/SSL 憑證都使用 SHA-256 來加強伺服器安全性。

5. *是否需要我的 DNS 提供者的憑證授權單位授權記錄？*

    目前不需要憑證授權單位單位授權記錄。 不過，如果您的確有一個授權記錄，它必須包含 DigiCert 作為有效的 CA。

6. *在 2018 年 6 月 20 日，來自 Verizon 的 Azure CDN 預設開始使用專用憑證搭配 SNI TLS/SSL。如果使用主體別名 (SAN) 憑證和以 IP 為基礎的 TLS/SSL ，我的現有自訂網域會發生什麼狀況？*

    如果 Microsoft 分析只有對您的應用程式進行 SNI 用戶端要求，則您現有的網域將在未來幾個月中逐漸移轉至單一憑證。 
    
    如果偵測到非 SNI 用戶端，您的網域會保留在具有以 IP 為基礎的 TLS/SSL 的 SAN 憑證中。 對您的服務或非 SNI 用戶端的要求不會受到影響。

7. 若使用您自己的憑證，該如何更新憑證？ 

    若要確保將較新的憑證部署到 PoP 基礎結構，請將新的憑證上傳至 Azure KeyVault。 在 Azure CDN 的 TLS 設定中，選擇最新的憑證版本，然後選取 [儲存]。 Azure CDN 接著會傳播已更新的憑證。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> - 在您的自訂網域上啟用 HTTPS 通訊協定。
> - 使用 CDN 管理的憑證 
> - 使用您自己的憑證
> - 驗證網域。
> - 在您的自訂網域上停用 HTTPS 通訊協定。

請前往下一個教學課程，以了解如何在 CDN 端點上設定快取。

> [!div class="nextstepaction"]
> [教學課程：設定 Azure CDN 快取規則](cdn-caching-rules-tutorial.md)