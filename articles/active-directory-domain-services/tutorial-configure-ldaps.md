---
title: 教學課程 - 為 Azure Active Directory Domain Services 設定 LDAPS | Microsoft Docs
description: 在本教學課程中，您會了解如何為 Azure Active Directory Domain Services 受控網域設定安全的輕量型目錄存取通訊協定 (LDAPS)。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 1164d838a45496a075d356995a60beb967cdfcca
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054335"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>教學課程：為 Azure Active Directory Domain Services 受控網域設定安全 LDAP

輕量型目錄存取通訊協定 (LDAP) 會用來與您的 Azure Active Directory Domain Services (Azure AD DS) 受控網域進行通訊。 根據預設，LDAP 流量不會加密，這讓許多環境有安全上的顧慮。

您可以使用 Azure AD DS 將受控網域設定為使用安全的輕量型目錄存取通訊協定 (LDAPS)。 使用安全 LDAP 時，流量就會加密。 安全 LDAP 也稱為「透過安全通訊端層 (SSL)/傳輸層安全性 (TLS) 的 LDAP」。

本教學課程將說明如何為 Azure AD DS 受控網域設定 LDAPS。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立用於 Azure AD DS 的數位憑證
> * 為 Azure AD DS 啟用安全 LDAP
> * 設定安全 LDAP 以透過公開的網際網路使用
> * 繫結並測試受控網域的安全 LDAP

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 已在您的電腦上安裝 LDP.exe 工具。
    * 如有需要，請為 Active Directory Domain Services 和 LDAP [安裝遠端伺服器管理工具 (RSAT)][rsat]。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教學課程中，您將使用 Azure 入口網站為受控網域設定安全 LDAP。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-certificate-for-secure-ldap"></a>建立安全 LDAP 的憑證

若使用安全 LDAP，您的通訊就會使用數位憑證來加密。 此數位憑證會套用至您的受控網域，並讓 LDP.exe 之類的工具在查詢資料時使用安全的加密通訊。 有兩種方式可為受控網域的安全 LDAP 存取建立憑證：

* 來自公用憑證授權單位 CA 或企業 CA 的憑證。
    * 如果您的組織從公用 CA 取得憑證，請從該公用 CA 取得安全 LDAP 的憑證。 如果您在組織中使用企業 CA，請從企業 CA 取得安全 LDAP 的憑證。
    * 只有當您將自訂 DNS 名稱與您的受控網域搭配使用時，才適用公用 CA。 如果受控網域的 DNS 網域名稱結尾是 .onmicrosoft.com，您就無法建立數位憑證來保護與此預設網域之間的連線。 .onmicrosoft.com 網域屬於 Microsoft，因此公用 CA 不會發行憑證。 在此情況下，請建立自我簽署的憑證，然後使用該憑證來設定安全 LDAP。
* 您自行建立的自我簽署憑證。
    * 此方法適用於測試目的，也就是本教學課程所示範的內容。

您要求或建立的憑證必須符合下列要求。 如果您使用無效憑證來啟用安全 LDAP，您的受控網域就會遇到問題：

* **信任的簽發者** - 憑證必須由使用安全 LDAP 連線到網域的電腦，所信任的授權單位加以發行。 此授權單位可能是受這些電腦信任的公用 CA 或企業 CA。
* **存留期** - 憑證必須至少在接下來的 3 至 6 個月內都要保持有效。 當憑證過期時，受控網域的安全 LDAP 存取會中斷。
* **主體名稱** - 憑證的主體名稱必須是您受控網域。 例如，如果您的網域名稱為 aaddscontoso.com，則憑證的主體名稱必須是 *.aaddscontoso.com。
    * 憑證的 DNS 名稱或主體替代名稱必須是萬用字元憑證，才能確保安全 LDAP 可以順利地與 Azure AD Domain Services 搭配運作。 網域控制站會使用隨機名稱，而且可以加以移除或新增，以確保服務可供使用。
* **金鑰使用方法** - 必須將憑證設定為「數位簽章」與「金鑰編密」。
* **憑證目的** - 憑證必須可有效用於 TLS 伺服器驗證。

有數個工具可用來建立自我簽署憑證，例如 OpenSSL、Keytool、MakeCert、[New-SelfSignedCertificate][New-SelfSignedCertificate] Cmdlet 等等。

在本教學課程中，會使用 [New-SelfSignedCertificate][New-SelfSignedCertificate] Cmdlet 建立安全 LDAP 的自我簽署憑證。

以**系統管理員**身分開啟 PowerShell 視窗並執行下列命令。 將 $dnsName 變數取代為您本身的受控網域所使用的 DNS 名稱，例如 aaddscontoso.com：

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

下列輸出範例顯示已成功產生憑證，並將其儲存在本機憑證存放區 (LocalMachine\MY)：

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>了解並匯出所需的憑證

若要使用安全 LDAP，網路流量會使用公開金鑰基礎結構 (PKI) 來加密。

* **私密**金鑰會套用至受控網域。
    * 我們會用此私密金鑰來「解密」安全 LDAP 的流量。 私密金鑰只能套用至受控網域，而不是廣泛散發到用戶端電腦。
    * 包含私密金鑰的憑證會使用 .PFX 檔案格式。
    * 憑證的加密演算法必須是 TripleDES-SHA1。
* **公開**金鑰會套用至用戶端電腦。
    * 我們會使用此公開金鑰來「加密」安全 LDAP 的流量。 公開金鑰可以散發到用戶端電腦。
    * 沒有私密金鑰的憑證會使用 .CER 檔案格式。

這兩個金鑰 (「私密」和「公開」金鑰) 可確保只有適當的電腦能夠成功地彼此通訊。 如果您使用公用 CA 或企業 CA，您收到的憑證會包含私密金鑰，並可套用至受控網域。 公開金鑰應該是用戶端電腦已經知道且信任的金鑰。

在本教學課程中，您已建立具有私密金鑰的自我簽署憑證，因此您需要匯出適當的私密和公開元件。

### <a name="export-a-certificate-for-azure-ad-ds"></a>為 Azure AD DS 匯出憑證

若要搭配受控網域使用上一個步驟中建立的數位憑證，您必須先將憑證匯出至包含私密金鑰的 .PFX 憑證檔案。

1. 若要開啟 [執行] 對話方塊，請選取 [Windows] + **R** 鍵。
1. 在 [執行] 對話方塊中輸入 **mmc**並選取 [確定]，以開啟 Microsoft Management Console (MMC)。
1. 在 [使用者帳戶控制] 提示字元選取 [是]，以系統管理員身分啟動 MMC。
1. 從 [檔案] 功能表中，選取 [新增/移除嵌入式...]
1. 在 [憑證嵌入式管理單元] 精靈中，選擇 [電腦帳戶] 並選取 [下一步]。
1. 在 [選取電腦] 頁面上，選擇 [本機電腦: (執行這個主控台的電腦)] 並選取 [完成]。
1. 在 [新增或移除嵌入式管理單元] 對話方塊中，選取 [確定] 以在 MMC 中新增憑證嵌入式管理單元。
1. 在 MMC 視窗中，展開 [主控台根目錄]。 選取 [憑證 (本機電腦)]，然後按依序展開 [個人] 節點和 [憑證] 節點。

    ![在 Microsoft Management Console 中開啟個人憑證存放區](./media/tutorial-configure-ldaps/open-personal-store.png)

1. 此時會顯示先前的步驟中建立的自我簽署憑證，例如 aaddscontoso.com。 在此憑證上按一下滑鼠右鍵，然後選擇 [所有工作] > [匯出...]

    ![在 Microsoft Management Console 中匯出憑證](./media/tutorial-configure-ldaps/export-cert.png)

1. 在 [憑證匯出精靈] 中選取 [下一步]。
1. 您必須匯出憑證的私密金鑰。 如果私密金鑰未包含在匯出的憑證中，為您受控網域啟用安全 LDAP 的動作將會失敗。

    在 [匯出私密金鑰] 頁面上，選擇 [是，匯出私密金鑰] 並選取 [下一步]。
1. 受控網域僅支援包含私密金鑰的 .PFX 憑證檔案格式。 請勿將憑證匯出為不含私密金鑰的 .CER 憑證檔案格式。

    在 [匯出檔案格式] 頁面上，選取 [個人資訊交換 - PKCS #12 (.PFX)] 作為匯出憑證的檔案格式。 核取 [如果可能的話，包含憑證路徑中的所有憑證] 方塊：

    ![選擇以 PKCS 12 (.PFX) 檔案格式匯出憑證的選項](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. 當此憑證用於解密資料時，您應謹慎地控制存取。 密碼可以用來保護憑證的使用。 如果沒有正確的密碼，憑證就無法套用至服務。

    在 [安全性] 頁面上，選擇 [密碼] 選項來保護 .PFX 憑證檔案。 加密演算法必須是 TripleDES-SHA1。 輸入並確認密碼，然後選取 [下一步]。 在下一節中，此密碼會用來為您的受控網域啟用安全 LDAP。
1. 在 [要匯出的檔案]  頁面上，指定檔案名稱及接收匯出憑證的位置，例如 C:\Users\accountname\azure-ad-ds.pfx。 記下 *.PFX* 檔案的密碼和位置，因為後續步驟將需要這項資訊。
1. 在檢閱頁面上選取 [完成]，以將憑證匯出至 .PFX 憑證檔案。 成功匯出憑證後，系統會顯示確認對話方塊。
1. 讓 MMC 保持開啟，以供下一節使用。

### <a name="export-a-certificate-for-client-computers"></a>為用戶端電腦匯出憑證

用戶端電腦必須信任安全 LDAP 憑證的簽發者，才能成功使用 LDAPS 連線到受控網域。 用戶端電腦需要憑證，才能成功加密由 Azure AD DS 解密的資料。 如果您使用公用 CA，電腦應該會自動信任這些憑證簽發者並擁有對應的憑證。

在本教學課程中，您使用了自我簽署憑證，並已在先前步驟中產生包含私密金鑰的憑證。 現在，我們要匯出自我簽署憑證，並將其安裝在用戶端電腦的受信任憑證存放區：

1. 返回 MMC 的 [憑證 (本機電腦)] > [個人] > [憑證] 存放區。 此時會顯示先前的步驟中建立的自我簽署憑證，例如 aaddscontoso.com。 在此憑證上按一下滑鼠右鍵，然後選擇 [所有工作] > [匯出...]
1. 在 [憑證匯出精靈] 中選取 [下一步]。
1. 由於您不需要對用戶端使用私密金鑰，因此請在 [匯出私密金鑰] 頁面上選擇 [否，不要匯出私密金鑰]，然後選取 [下一步]。
1. 在 [匯出檔案格式] 頁面上，選取 [Base-64 編碼的 X.509 (.CER)] 作為匯出憑證的檔案格式：

    ![選擇以 Base-64 編碼 X.509 (.CER) 檔案格式匯出憑證的選項](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. 在 [要匯出的檔案]  頁面上，指定檔案名稱及接收匯出憑證的位置，例如 C:\Users\accountname\azure-ad-ds-client.cer。
1. 在檢閱頁面上選取 [完成]，以將憑證匯出至 .CER 憑證檔案。 成功匯出憑證後，系統會顯示確認對話方塊。

CER 憑證檔案現在可以散發到用戶端電腦，因為這些電腦必須信任以受控網域為目標的安全 LDAP 連線。 讓我們來將憑證安裝在本機電腦上。

1. 開啟 [檔案總管]，然後瀏覽至您儲存 .CER 憑證檔案的位置，例如 C:\Users\accountname\azure-ad-ds-client.cer。
1. 以滑鼠右鍵選取 .CER 憑證檔案，然後選擇 [安裝憑證]。
1. 在 [憑證匯入精靈] 中，選擇將憑證儲存在 [本機電腦] 中，然後選取 [下一步]：

    ![選擇將憑證匯入至本機電腦存放區的選項](./media/tutorial-configure-ldaps/import-cer-file.png)

1. 出現提示時，選擇 [是] 以允許電腦進行變更。
1. 選擇 [根據憑證類型自動選取憑證存放區]，然後選取 [下一步]。
1. 在檢閱頁面上選取 [完成]，以匯入 .CER 憑證。 成功匯入憑證後，系統會顯示確認對話方塊。

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>為 Azure AD DS 啟用安全 LDAP

建立及匯出包含私密金鑰的數位憑證，並將用戶端電腦設定為信任連線之後，您現在可以在受控網域上啟用安全 LDAP。 若要在受控網域上啟用安全 LDAP，請執行下列設定步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的 [搜尋資源] 方塊中輸入「網域服務」。 從搜尋結果選取 [Azure AD Domain Services]。
1. 選擇您的受控網域，例如 aaddscontoso.com。
1. 在 [Azure AD DS] 視窗的左側，選擇 [安全 LDAP]。
1. 根據預設，已停用受控網域的安全 LDAP 存取。 將 [安全 LDAP] 切換至 [啟用]。
1. 透過網際網路存取受控網域的安全 LDAP 存取會預設為停用。 當您啟用公開的安全 LDAP 存取時，您的網域將容易遭受網際網路上的暴力密碼破解攻擊。 在下一個步驟中，我們會設定網路安全性群組，將存取鎖定為僅限所需的來源 IP 位址範圍。

    將 [在網際網路上允許安全 LDAP 存取] 切換為 [啟用]。

1. 選取 [具備安全 LDAP 憑證的 .PFX 檔案] 旁的資料夾圖示。 流覽至 .PFX 檔案的路徑，然後選取先前步驟中所建立且包含私密金鑰的憑證。

    > [!IMPORTANT]
    > 如前一節所述的憑證需求，您無法從具有 .onmicrosoft.com 預設網域的公用 CA 使用憑證。 .onmicrosoft.com 網域屬於 Microsoft，因此公用 CA 不會發行憑證。
    >
    > 請確定您的憑證採用適當的格式。 如果不是適當格式，當您啟用安全 LDAP 時，Azure 平台會產生憑證驗證錯誤。

1. 輸入將憑證匯出至 .PFX 檔案時，在先前步驟中設定的 [用以解密 .PFX 檔案的密碼]。
1. 選取 [儲存] 以啟用安全 LDAP。

    ![在 Azure 入口網站中為受控網域啟用安全 LDAP](./media/tutorial-configure-ldaps/enable-ldaps.png)

安全 LDAP 已針對受控網域進行設定的通知會隨即顯示。 此作業完成之前，您無法修改受控網域的其他設定。

為受控網域啟用安全 LDAP 需要約幾分鐘的時間。 如果您提供的安全 LDAP 憑證不符合所需的準則，為受控網域啟用安全 LDAP 的動作將會失敗。

失敗的一些常見原因為網域名稱不正確、憑證的加密演算法不是 TripleDES-SHA1 或憑證即將過期或已過期。 您可以利用有效的參數重新建立憑證，然後使用此更新憑證來啟用安全 LDAP。

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>鎖定透過網際網路進行的安全 LDAP 存取

當您允許透過網際網路使用安全 LDAP 存取受控網域時，就會產生安全性威脅。 您可以透過 TCP 通訊埠 636 從網際網路連線到受控網域。 建議您將受控網域的存取限制為您環境已知的特定 IP 位址。 您可以使用 Azure 網路安全性群組規則來限制安全 LDAP 的存取。

讓我們來建立一個規則，以允許安全 LDAP 存取可透過 TCP 連接埠 636 從一組指定 IP 位址輸入。 優先順序較低的預設 DenyAll 規則會套用至來自網際網路的所有其他輸入流量，因此只有指定的位址可以使用安全 LDAP 連線到您的受控網域。

1. 在 Azure 入口網站的左側導覽中，選取 [資源群組]。
1. 選擇您的資源群組 (例如 *myResourceGroup*)，然後選取您的網路安全性群組 (例如 *aaads-nsg*)。
1. 此時會顯示現有輸入和輸出安全性規則的清單。 在 [網路安全性群組] 視窗的左側，選擇 [設定] > [輸入安全性規則]。
1. 選取 [新增]，然後建立規則來允許 TCP 連接埠 636。 為了提升安全性，請選擇 [IP 位址] 作為來源，然後為組織指定您自己的有效 IP 位址或範圍。

    | 設定                           | 值        |
    |-----------------------------------|--------------|
    | 來源                            | IP 位址 |
    | 來源 IP 位址 / CIDR 範圍 | 適用於您環境的有效 IP 位址或範圍 |
    | 來源連接埠範圍                | *            |
    | Destination                       | 任意          |
    | 目的地連接埠範圍           | 636          |
    | 通訊協定                          | TCP          |
    | 動作                            | Allow        |
    | 優先順序                          | 401          |
    | 名稱                              | AllowLDAPS   |

1. 準備好時，請選取 [新增] 以儲存並套用規則。

    ![建立網路安全性群組規則，以保護透過網際網路進行的 LDAPS 存取](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>設定用於外部存取的 DNS 區域

啟用透過網際網路進行安全 LDAP 存取後，請更新 DNS 區域，以便用戶端電腦可以找到此受控網域。 「安全 LDAP 的外部 IP 位址」 會列在受控網域的 [屬性] 索引標籤上：

![在 Azure 入口網站中檢視您受控網域的安全 LDAP 外部 IP 位址](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

設定外部 DNS 提供者，以建立主機記錄 (例如 ldaps) 來解析為此外部 IP 位址。 若要先在機器上進行本機測試，您可以在 Windows 的 hosts 檔案中建立項目。 若要成功編輯本機電腦上的 hosts 檔案，請以系統管理員身分開啟 [記事本]，然後開啟 C:\Windows\System32\drivers\etc\hosts 檔案

下列 DNS 項目範例 (搭配外部 DNS 提供者或在本機 hosts 檔案中) 會將 ldaps.aaddscontoso.com 的流量解析為 168.62.205.103 的外部 IP 位址：

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>測試受控網域的查詢

若要連線及繫結至您的受控網域，並透過 LDAP 進行搜尋，您可以使用 LDP.exe 工具。 這項工具組含在遠端伺服器管理工具 (RSAT) 套件中。 如需詳細資訊，請參閱[安裝遠端伺服器管理工具][rsat]。

1. 開啟 LDP.exe 並連線到受控網域。 選取 [連線]，然後選擇 [連線...]。
1. 輸入您在先前的步驟中為受控網域建立的安全 LDAP DNS 網域名稱，例如 ldaps.aaddscontoso.com。 若要使用安全 LDAP，請將 [連接埠] 設定為 [636]，然後核取 [SSL] 的方塊。
1. 選取 [確定] 以連線至受控網域。

接著，繫結至您的受控網域。 如果您已在受控網域上停用 NTLM 密碼雜湊同步，使用者 (和服務帳戶) 就無法執行 LDAP 簡單繫結。 如需有關停用 NTLM 密碼雜湊同步的詳細資訊，請參閱[保護您的受控網域][secure-domain]。

1. 選取 [連線] 功能表選項，然後選擇 [繫結...]。
1. 提供屬於受控網域的使用者帳號認證。 輸入使用者帳戶的密碼，然後輸入您的網域，例如 aaddscontoso.com。
1. 針對 [繫結類型]，選擇 [與認證繫結] 的選項。
1. 選取 [確認] 以繫結至您的受控網域。

若要查看儲存在受控網域中的物件：

1. 選取 [檢視] 功能表選項，然後選擇 [樹狀]。
1. 將 [BaseDN] 欄位保留空白，然後選取 [確定]。
1. 選擇容器，例如 AADDC Users，然後以滑鼠右鍵選取容器並選擇 [搜尋]。
1. 保留已預先填入的欄位集，然後選取 [執行]。 查詢的結果會顯示在右側視窗中，如下列範例輸出所示：

    ![使用 LDP.exe 搜尋受控網域中的物件](./media/tutorial-configure-ldaps/ldp-query.png)

若要直接查詢特定容器，您可以從 [檢視] > [樹狀] 功能表中指定 **BaseDN**，例如 OU=AADDC Users,DC=AADDSCONTOSO,DC=COM 或 OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM。 如需如何格式化和建立查詢的詳細資訊，請參閱 [LDAP 查詢的基本概念][ldap-query-basics]。

## <a name="clean-up-resources"></a>清除資源

如果您已針對本教學課程，將 DNS 項目新增至電腦的本機 hosts 檔案來測試連線能力，請移除此項目，並在您的 DNS 區域中新增正式記錄。 若要移除本機 hosts 檔案中的項目，請完成下列步驟：

1. 在您的本機電腦上，以系統管理員身分開啟 [記事本]
1. 流覽至 *C:\Windows\System32\drivers\etc\hosts*並開啟檔案
1. 刪除您所新增的記錄行，例如 `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立用於 Azure AD DS 的數位憑證
> * 為 Azure AD DS 啟用安全 LDAP
> * 設定安全 LDAP 以透過公開的網際網路使用
> * 繫結並測試受控網域的安全 LDAP

> [!div class="nextstepaction"]
> [設定混合式 Azure AD 環境的密碼雜湊同步](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
