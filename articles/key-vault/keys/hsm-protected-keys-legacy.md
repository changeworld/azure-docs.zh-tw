---
title: 如何為 Azure Key Vault 產生並傳輸受 HSM 保護的金鑰 - Azure Key Vault | Microsoft Docs
description: 使用這份文件協助您規劃、產生，並傳輸受 HSM 保護的金鑰，以搭配 Azure 金鑰保存庫使用。 也稱為 BYOK 或「攜帶您自己的金鑰」。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 28f066668d580f16d831371f2d02a5abcc0e84b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429729"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>匯入金鑰保存庫(舊版)的受 HSM 保護的金鑰

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

為了加強保證，當您使用 Azure 金鑰保存庫時，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 此案例通常稱為 *自備金鑰*或 BYOK。 Azure 密鑰保管庫使用 nCipher nShield 系列 HSM(FIPS 140-2 級別 2 驗證)來保護密鑰。

使用本主題中的資訊，協助您規劃、產生然後傳送自己受 HSM 保護的金鑰，以搭配使用 Azure 金鑰保存庫。

此功能不適用於 Azure China。

> [!NOTE]
> 有關 Azure 金鑰保管庫的詳細資訊,請參閱[什麼是 Azure 密鑰保管庫?](../general/overview.md)  
> 如需入門教學課程 (包括建立受 HSM 保護之金鑰的金鑰保存庫)，請參閱[什麼是 Azure Key Vault？](../general/overview.md)。

有關產生及透過網際網路傳輸受 HSM 保護之金鑰的詳細資訊：

* 您可以從離線工作站產生金鑰，可減少受攻擊面。
* 此金鑰利用金鑰交換金鑰 (KEK) 加密，且加密狀態會維持到金鑰傳輸至 Azure 金鑰保存庫 HSM 為止。 只有加密版本的金鑰會離開原始工作站。
* 工具組會在將您的金鑰繫結至 Azure 金鑰保存庫安全世界的租用戶金鑰上設定屬性。 因此，在 Azure 金鑰保存庫 HSM 接收和解密您的金鑰之後，只有這些 HSM 可使用它。 無法匯出您的金鑰。 此繫結由 nCipher HSM 強制執行。
* 用來解密金鑰的金鑰互換金鑰 (KEK) 產生於 Azure 金鑰保存庫 HSM 內且不可匯出。 HSM 會強制執行使 HSM 外部沒有明確版本的 KEK。 此外,該工具集還包括來自 nCipher 的證明,證明 KEK 不可匯出,並且生成於由 nCipher 製造的正版 HSM 中。
* 該工具集包括來自 nCipher 的證明,即 Azure 密鑰保管庫安全世界也生成在 nCipher 製造的正版 HSM 上。 這個證書向您證明 Microsoft 正在使用正版硬體。
* Microsoft 會在每個地理區域使用不同的 KEK 和不同的「安全世界」。 這種區隔可確保您的金鑰只能用在您加密它時所在區域中的資料中心。 例如，來自歐洲客戶的金鑰不能在北美或亞洲的資料中心使用。

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>有關 nCipher HSM 和 Microsoft 服務的詳細資訊

nCipher Security 是一家委託 Datacard 公司,是通用 HSM 市場的領導者,通過為其關鍵業務資訊和應用程式提供信任、完整性和控制,為世界領先的組織提供支援。 nCipher的加密解決方案保護新興技術(雲、物聯網、區塊鏈、數位支付),並幫助滿足新的合規性要求,使用全球組織目前所依賴的相同成熟技術來抵禦其敏感數據、網路通信和企業基礎架構的威脅。 nCipher 為關鍵業務應用程式提供信任,確保數據的完整性,並始終讓客戶完全控制。

微軟與 NCipher 安全公司合作,以增強 HSM 的最先進的狀態。 這些增強內容可讓您取得裝載服務的典型優勢，而且不用放棄金鑰的控制權。 具體而言，這些增強內容可讓 Microsoft 管理 HSM，如此您就不必費心管理。 作為雲服務,Azure 密鑰保管庫會在短時間內擴展,以滿足組織的使用高峰。 同時,金鑰在 Microsoft 的 HSM 中受到保護:您保留對密鑰生命週期的控制,因為您生成密鑰並將其傳輸到 Microsoft 的 HSM。

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>實作 Azure 金鑰保存庫的自備金鑰 (BYOK)

如果您將產生您自己受 HSM 保護的金鑰，然後將它傳輸到 Azure 金鑰保存庫，請使用下列資訊和程序—自備金鑰 (BYOK) 案例。

## <a name="prerequisites-for-byok"></a>BYOK 的必要條件

請參閱下表的必要條件清單以取得 Azure 金鑰保存庫的自備金鑰 (BYOK)。

| 需求 | 詳細資訊 |
| --- | --- |
| Azure 訂用帳戶 |若要建立 Azure 金鑰保存庫，您需要 Azure 訂用帳戶： [註冊免費試用](https://azure.microsoft.com/pricing/free-trial/) |
| 可支援受 HSM 保護之金鑰的 Azure 金鑰保存庫進階服務層級 |如需 Azure 金鑰保存庫的服務層級和功能的詳細資訊，請參閱 [Azure 金鑰保存庫價格](https://azure.microsoft.com/pricing/details/key-vault/) 網站。 |
| nCipher nShield HSM、智慧卡和支援軟體 |您必須有權訪問 nCipher 硬體安全模組和 nCipher nShield HSM 的基本操作知識。 有關相容型號的清單,請參閱[nCipher nShield 硬體安全模組](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy),或者如果您沒有 HSM,則購買 HSM。 |
| 下列的硬體和軟體︰<ol><li>離線 x64 工作站,具有至少 Windows 7 和 nCipher nShield 軟體的 Windows 作業系統,至少是版本 11.50。<br/><br/>如果此工作站執行 Windows 7，您必須[安裝 Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)。</li><li>連線至網際網路且 Windows 作業系統至少為 Windows 7 的工作站，並已安裝至少為 [1.1.0 版的 Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) ****。</li><li>至少有 16 MB 可用空間的 USB 磁碟機或其他可攜式儲存裝置。</li></ol> |基於安全性理由，建議第一個工作站不要連線到網路。 不過，在程式設計方面並不強迫採取這項建議。<br/><br/>在接下來的指示中，此工作站稱為中斷連線的工作站。</p></blockquote><br/>此外，如果您的租用戶金鑰適用於生產網路，建議您另外使用第二個工作站來下載工具組，並上傳租用戶金鑰。 但如果只是測試，您可以直接使用第一個工作站。<br/><br/>在接下來的指示中，此第二個工作站稱為網際網路連線的工作站。</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>產生金鑰並將其傳輸至 Azure 金鑰保存庫 HSM

您將使用下列五個步驟產生金鑰並將其傳輸至 Azure 金鑰保存庫 HSM：

* [步驟 1：準備網際網路連線的工作站](#step-1-prepare-your-internet-connected-workstation)
* [步驟 2：準備中斷連線的工作站](#step-2-prepare-your-disconnected-workstation)
* [步驟 3：產生您的金鑰](#step-3-generate-your-key)
* [步驟 4：準備要傳輸的金鑰](#step-4-prepare-your-key-for-transfer)
* [步驟 5：將金鑰傳輸至 Azure 金鑰保存庫](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>步驟 1：準備網際網路連線的工作站

在第一個步驟中，請在連線到網際網路的工作站上執行下列程序。

### <a name="step-11-install-azure-powershell"></a>步驟 1.1：安裝 Azure PowerShell

從網際網路連線的工作站，下載並安裝 Azure PowerShell 模組，其包含 cmdlet 以管理 Azure 金鑰保存庫。 如需安裝指示，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

### <a name="step-12-get-your-azure-subscription-id"></a>步驟 1.2：取得您的 Azure 訂用帳戶識別碼

使用下列命令開始 Azure PowerShell 工作階段，並登入您的 Azure 帳戶：

```Powershell
   Connect-AzAccount
```
在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。 然後，使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) 命令：

```powershell
   Get-AzSubscription
```
從輸出中，找出您將用於 Azure 金鑰保存庫的訂用帳戶識別碼。 您稍後將需要此訂用帳戶識別碼。

請勿關閉 Azure PowerShell 視窗。

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>步驟 1.3：下載 Azure 金鑰保存庫的 BYOK 工具組

移至 Microsoft 下載中心並為您的地理區域或 Azure 執行個體 [下載 Azure 金鑰保存庫 BYOK 工具組](https://www.microsoft.com/download/details.aspx?id=45345) 。 使用下列資訊來識別要下載封裝雜湊與其對應的 SHA-256 封裝雜湊︰

---
**美國:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**歐洲:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**亞洲:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**拉丁美洲:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**日本:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**南韓：**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**南非:**

鑰匙庫-比約克-工具-南非.zip

C41060C5C0170AAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**阿聯酋:**

鑰匙庫-比約克-工具-阿聯酋.zip

FADE80210B06962AA0913EA411DAB9779248C65F365FD953BB9F241D5FC0D3

---
**澳大利亞:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure 政府:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**US Gov 國防部：**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**加拿大:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**德國:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**德國 公共:**

鑰匙庫-比約克-工具-德國-公共.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B788865364A29

---
**印度:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**法國:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**英國：**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**瑞士:**

鑰匙庫-比約克-工具-瑞士.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


若要驗證您已下載之 BYOK 工具組的完整性，請從您的 Azure PowerShell 工作階段，使用 [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) Cmdlet。

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

此工具組包含：

* 具有以 **BYOK-KEK-pkg-**
* 具有以 **BYOK-SecurityWorld-pkg-**
* 名為 **verifykeypackage.py** 的 python 指令碼。
* 名為 **KeyTransferRemote.exe** 的命令列可執行檔及關聯的 DLL。
* Visual C++ 可轉散發套件，名為 **vcredist_x64.exe**。

將封裝複製到 USB 磁碟機或其他可攜式儲存裝置。

## <a name="step-2-prepare-your-disconnected-workstation"></a>步驟 2：準備中斷連線的工作站

在第二個步驟中，請在未連線到網路 (網際網路或內部網路) 的工作站上執行下列程序。

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>步驟 2.1:使用 nCipher nShield HSM 準備斷連線的工作站

在 Windows 電腦上安裝 nCipher 支援軟體,然後將 nCipher nShield HSM 附加到該電腦。

確保 nCipher 工具位於您的路徑中 **(%nfast_home %nfast_home%\bin**)。 例如，輸入下列內容：

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

有關詳細資訊,請參閱 nShield HSM 中包含的使用者指南。

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>步驟 2.2：在中斷連線的工作站上安裝 BYOK 工具組

從 USB 磁碟機或其他可攜式儲存裝置複製 BYOK 工具組封裝，然後執行下列動作：

1. 將檔案從下載的封裝解壓縮至任何資料夾。
2. 從該資料夾執行 vcredist_x64.exe。
3. 遵循指示以安裝 Visual Studio 2013 的 Visual C++ 執行階段元件。

## <a name="step-3-generate-your-key"></a>步驟 3：產生您的金鑰

在第三個步驟中，請在中斷連線的工作站上執行下列程序。 若要完成此步驟，您的 HSM 必須是初始化模式。 

### <a name="step-31-change-the-hsm-mode-to-i"></a>步驟 3.1︰將 HSM 模式變更為 I

如果使用 nCipher nShield 邊緣,則更改模式:1。 使用 [Mode (模式)] 按鈕來反白顯示必要的模式。 2. 在幾秒鐘之內，按住 [Clear (清除)] 按鈕幾秒鐘。 如果模式發生更改,新模式的 LED 將停止閃爍並保持亮起。 狀態 LED 可能會不規則閃爍幾秒鐘的時間，當裝置就緒後則規則地閃爍。 否則，裝置會維持目前的模式，適當的模式 LED 會亮起。

### <a name="step-32-create-a-security-world"></a>步驟 3.2：建立安全世界

啟動命令提示符並運行 nCipher 新世界程式。

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

此程式會在 %NFAST_KMDATA%\local\world 中建立與 C:\ProgramData\nCipher\Key Management Data\local 資料夾對應的**安全園地**檔案。 您可以為仲裁使用不同的值,但在我們的示例中,系統會提示您為每個值輸入三張空白卡和引腳。 然後，任兩張卡片可提供安全世界的完整存取權。 這些卡片將成為新安全園地的**系統管理員卡組**。

> [!NOTE]
> 如果您的 HSM 不支援較新的加密套件 DLf3072s256mRijndael，您可以使用 --cipher-suite=DLf1024s160mRijndael 來取代 --cipher-suite= DLf3072s256mRijndael
> 
> 使用 nCipher 軟體版本 12.50 附帶的 nCipher 軟體版本創建的安全世界與此 BYOK 過程不相容。 其中提供兩個選項：
> 1) 將 nCipher 軟體版本降級到 12.40.2,以創建一個新的安全世界。
> 2) 聯繫 nCipher 支援,並要求他們提供 12.50 軟體版本的修補程式,允許您使用 12.40.2 版本的新世界.exe,該版本與本 BYOK 過程相容。

然後執行以下動作：

* 備份世界檔案。 保障和保護世界檔案、系統管理員卡及其 pin，並確定沒有一個人可存取多張卡。

### <a name="step-33-change-the-hsm-mode-to-o"></a>步驟 3.3︰將 HSM 模式變更為 O

如果使用 nCipher nShield 邊緣,則更改模式:1。 使用 [Mode (模式)] 按鈕來反白顯示必要的模式。 2. 在幾秒鐘之內，按住 [Clear (清除)] 按鈕幾秒鐘。 如果模式發生更改,新模式的 LED 將停止閃爍並保持亮起。 狀態 LED 可能會不規則閃爍幾秒鐘的時間，當裝置就緒後則規則地閃爍。 否則，裝置會維持目前的模式，適當的模式 LED 會亮起。

### <a name="step-34-validate-the-downloaded-package"></a>步驟 3.4：驗證下載的封裝

此步驟為選擇性但建議使用，以便您可以驗證下列項目：

* 工具集中中包含的金鑰交換金鑰是從正版 nCipher nShield HSM 生成的。
* 工具集中中包含的安全世界哈希已在正版 nCipher nShield HSM 中生成。
* 金鑰交換金鑰不可匯出。

> [!NOTE]
> 要驗證下載的包,HSM 必須連接、通電,並且必須有一個安全世界(例如您剛剛創建的包)。

驗證下載的封裝：

1. 根據您的地理區域或 Azure 的執行個體輸入下列其中一個區域，以執行 verifykeypackage.py 指令碼：

   * 北美洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * 歐洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * 亞洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * 拉丁美洲：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * 日本：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * 南韓︰

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * 南非:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * 阿聯酋:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * 澳大利亞：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * 對於 [Azure Government](https://azure.microsoft.com/features/gov/)，它會使用 US Gov 的 Azure 執行個體：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * 美國政府國防部：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * 針對加拿大：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * 針對德國：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * 對於德國公眾:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * 針對印度︰

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * 針對法國：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * 針對英國：

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * 對於瑞士:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > nCipher nShield 套件包括在 %NFAST_HOME%\python_bin 的 python
     >
     >
2. 確認您看到下列訊息，表示驗證成功： **Result: SUCCESS**

此腳本驗證簽名者鏈到 nShield 根鍵。 此根金鑰的雜湊內嵌於指令碼中，且其值必須為 **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**。 您也可以通過訪問[nCipher 網站](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation)單獨確認此值。

現在,您可以創建新密鑰。

### <a name="step-35-create-a-new-key"></a>步驟 3.5：建立新的金鑰

使用 nCipher nShield**產生金鑰程式產生金鑰**。

執行下列命令來產生金鑰：

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

當您執行此命令時，請使用下列指示：

* 參數 *protect* 必須如所示般設定為值 **module**。 這會建立受模組保護的金鑰。 BYOK 工具組不支援受 OCS 保護的金鑰。
* 以任何字串值取代 **ident** 和 **plainname** 的 *contosokey* 值。 若要將系統管理負擔降至最低並減少錯誤的風險，建議您同時對兩者使用相同的值。 **ident** 值只能包含數字、破折號和小寫字母。
* 在這個範例中，Pubexp 保留空白 (預設值)，但是您可以指定特定值。 有關詳細資訊,請參閱[nCipher 文檔。](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

此命令會在您的 %NFAST_KMDATA%\local 資料夾建立名稱開頭為 **key_simple_** 的語彙基元化金鑰檔案，後面接著在命令中指定的 **ident**。 例如：**key_simple_contosokey**。 此檔案包含已加密的金鑰。

在安全的位置備份此語彙基元化金鑰檔案。

> [!IMPORTANT]
> 當您稍後將您的金鑰傳輸至 Azure 金鑰保存庫時，Microsoft 就無法將此金鑰匯出給您，因此，請務必安全地備份您的金鑰和安全世界。 請與[nCipher](https://www.ncipher.com/about-us/contact-us)聯繫,瞭解備份密鑰的指南和最佳實踐。
>


您現在已準備好將金鑰傳輸至 Azure 金鑰保存庫。

## <a name="step-4-prepare-your-key-for-transfer"></a>步驟 4：準備要傳輸的金鑰

在第四個步驟中，請在中斷連線的工作站上執行下列程序。

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>步驟 4.1：使用降低權限建立金鑰的複本

開啟新的命令提示字元，並將目前的目錄變更為解壓縮 BYOK ZIP 檔案的位置。 若要減少金鑰的權限，請從命令提示字元，根據您的地理區域或 Azure 執行個體，執行下列其中一個區域：

* 北美洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* 歐洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* 亞洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* 拉丁美洲：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* 日本：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* 南韓︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* 南非:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* 阿聯酋:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* 澳大利亞：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* 對於 [Azure Government](https://azure.microsoft.com/features/gov/)，它會使用 US Gov 的 Azure 執行個體：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* 美國政府國防部：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* 針對加拿大：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* 針對德國：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* 對於德國公眾:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* 針對印度︰

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* 針對法國：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* 針對英國：

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* 對於瑞士:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

當您執行此命令時，請以您從[產生您的金鑰](#step-3-generate-your-key)步驟的**步驟 3.5：建立新的金鑰**中指定的相同值取代 *contosokey*。

系統會要求您插入您的安全世界系統管理員卡。

命令完成後,您將看到**結果:成功**,並且具有縮小許可權的密鑰副本位於\<名為 key_xferacId_ contosokey>的文件中。

您可以使用 nCipher nShield 實用程式使用以下指令檢查 ACLS:

* aclprint.py：

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe：

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  當您執行這些命令時，請以您從[產生您的金鑰](#step-3-generate-your-key)步驟的**步驟 3.5：建立新的金鑰**指定的相同值取代 contosokey。

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>步驟 4.2:使用微軟金鑰交換金鑰加密金鑰

根據您的地理區域或 Azure 執行個體，執行下列其中一個命令：

* 北美洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 歐洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 亞洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 拉丁美洲：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 日本：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 南韓︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 南非:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 阿聯酋:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 澳大利亞：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 對於 [Azure Government](https://azure.microsoft.com/features/gov/)，它會使用 US Gov 的 Azure 執行個體：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 美國政府國防部：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 針對加拿大：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 針對德國：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 對於德國公眾:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 針對印度︰

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 針對法國：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 針對英國：

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 對於瑞士:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


當您執行此命令時，請使用下列指示：

* 請以您在*產生您的金鑰*步驟的**步驟 3.5：建立新的金鑰**中用來產生金鑰的識別碼取代 [contosokey](#step-3-generate-your-key) 。
* 以包含金鑰保存庫的 Azure 訂用帳戶識別碼取代 *SubscriptionID* 。 您先前已在 **步驟 1.2：取得 Azure 訂用帳戶識別碼** 中從 [準備網際網路連線的工作站](#step-1-prepare-your-internet-connected-workstation) 步驟擷取過這個值。
* 以用於輸出檔案名稱的標籤取代 *ContosoFirstHSMKey*。

當此動作成功完成時，會顯示 **Result: SUCCESS** ，而且目前的資料夾中會有新的檔案，其名稱如下：KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>步驟 4.3：將金鑰傳輸封裝複製到網際網路連線的工作站

使用 USB 磁碟機或其他可攜式儲存裝置，將上一個步驟的輸出檔案 (KeyTransferPackage-ContosoFirstHSMkey.byok) 複製到網際網路連線的工作站。

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>步驟 5：將金鑰傳輸至 Azure 金鑰保存庫

針對這最後一個步驟，在連線到網際網路的工作站上，使用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) Cmdlet，將您從已中斷連線之工作站複製的金鑰傳輸套件上傳到 Azure Key Vault HSM：

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

如果上傳成功，就會顯示您剛才加入之金鑰的屬性。

## <a name="next-steps"></a>後續步驟

您現在可以在您的金鑰保存庫中使用這個受 HSM 保護的金鑰。 有關詳細資訊,請參閱此價格和功能[比較](https://azure.microsoft.com/pricing/details/key-vault/)。
