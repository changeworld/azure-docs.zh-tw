---
title: 將實驗室設定為 Azure 開發人員測試實驗室中使用遠端桌面閘道
description: 瞭解如何使用遠端桌面閘道在 Azure DevTest 實驗室中配置實驗室,以確保安全訪問實驗室 VM,而無需公開 RDP 埠。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: eac195babebf300aa9770d35b7b98eba29c234cf
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460982"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>在 Azure 開發人員測試實驗室中設定實驗室以使用遠端桌面閘道
在 Azure DevTest 實驗室中,可以為實驗室配置遠端桌面閘道,以確保安全訪問實驗室虛擬機器 (VM),而無需公開 RDP 埠。 該實驗室為您的實驗室使用者提供了一個中心位置,以便查看和連接到他們有權訪問的所有虛擬機。 **"虛擬機**"頁上的 **"連接**"按鈕將建立一個特定於電腦的 RDP 檔案,您可以打開該檔以連接到電腦。 通過將實驗室連接到遠端桌面閘道,您可以進一步自訂 RDP 連接並保護 RDP 連接。 

此方法更安全,因為實驗室使用者直接對閘道電腦進行身份驗證,或者可以使用域加入的閘道電腦上的公司憑據連接到其電腦。 該實驗室還支援對閘道電腦使用權杖身份驗證,該閘道電腦允許使用者連接到其實驗室虛擬機器,而無需將 RDP 連接埠暴露給 Internet。 本文介紹了如何設置使用權杖身份驗證連接到實驗室計算機的實驗室的範例。

## <a name="architecture-of-the-solution"></a>解決方案的結構結構

![解決方案的結構結構](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. 當您選擇「**連接**」按鈕時,將呼叫[「獲取 RDP 檔內容」](/rest/api/dtl/virtualmachines/getrdpfilecontents)操作。 
1. 取得 RDP 檔內容操作`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`呼叫 以請求身份驗證權杖。
    1. `{gateway-hostname}`是在 Azure 門戶中的**實驗室的「實驗室設置」** 頁上指定的閘道主機名。 
    1. `{lab-machine-name}`是您嘗試連接的電腦的名稱。
    1. `{port-number}`是需要進行連接的埠。 通常這個埠是3389。 如果實驗室 VM 在 DevTest 實驗室中使用[共用 IP](devtest-lab-shared-ip.md)功能,則埠將不同。
1. 遠端桌面閘道將調用從`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`Azure 函數延遲到 Azure 函數以生成身份驗證權杖。 DevTest Labs 服務會自動在請求標頭中包含功能密鑰。 功能鍵將保存在實驗室的密鑰保管庫中。 這個機密的名稱在實驗室的 **「實驗室設定」** 頁上顯示為**閘道金鑰**。
1. Azure 函數應返回權杖,用於對閘道電腦進行基於證書的權杖身份驗證。  
1. 然後,獲取 RDP 檔內容操作傳回完整的 RDP 檔,包括身份驗證資訊。
1. 使用首選 RDP 連接程式開啟 RDP 檔案。 請記住,並非所有 RDP 連接程式都支援權杖身份驗證。 身份驗證權杖確實具有由函數應用設置的到期日期。 在權杖過期之前與實驗室 VM 建立連接。
1. 遠端桌面閘道電腦對 RDP 檔中的權杖進行身份驗證後,連接將轉發到實驗室電腦。

### <a name="solution-requirements"></a>解決方案需求
要使用 DevTest Labs 權杖身份驗證功能,閘道電腦、網域名稱服務 (DNS) 和功能有一些配置要求。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>遠端桌面閘道電腦的要求
- 必須在閘道電腦上安裝 TLS/SSL 憑證才能處理 HTTPS 流量。 如果只有一台計算機,證書必須匹配網關伺服器場的負載均衡器的完全限定功能變數名稱 (FQDN) 或電腦本身的 FQDN。 通配符 TLS/SSL 證書不起作用。  
- 安裝在閘道電腦上的簽名證書。 使用[Create-簽署憑證.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)文稿建立簽署證書。
- 安裝支援遠端桌面閘道令牌身份驗證[的可插拔身份驗證](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)模組。 這種模組的一個範例是`RDGatewayFedAuth.msi`[系統中心虛擬機器管理員 (VMM) 映射](/system-center/vmm/install-console?view=sc-vmm-1807)附帶的一個範例。 有關系統中心的詳細資訊,請參閱[系統中心文件](https://docs.microsoft.com/system-center/)和[定價詳細資訊](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 閘道伺服器可以處理對`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`發出的請求。

    閘道主機名是閘道伺服器場負載均衡器的 FQDN,或者只有一台電腦時電腦本身的 FQDN。 您`{lab-machine-name}`試著連接的實驗室電腦的名稱,`{port-number}`也是將在其上進行連接的連接埠。  默認情況下,此埠為 3389。  但是,如果虛擬機在 DevTest 實驗室中使用[共用 IP](devtest-lab-shared-ip.md)功能,則埠將不同。
- Internet 資訊伺服器 (IIS)[的應用程式路由請求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模組可用於將`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`請求重定向 到 azure 函數,該函數處理獲取權杖進行身份驗證的請求。


## <a name="requirements-for-azure-function"></a>Azure 函數的要求
Azure 函數處理具有`https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}`的 格式的請求,並基於閘道電腦上安裝的相同簽名證書返回身份驗證權杖。 是`{function-app-uri}`用於造訪函數的uri。 函數鍵將自動在請求的標頭中傳遞。 有關範例函數,請參閱[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>網路要求

- 與閘道電腦上安裝的 TLS/SSL 憑證關聯的 FQDN 的 DNS 必須將流量定向到閘道電腦或閘道機場的負載均衡器。
- 如果實驗室電腦使用專用 IP,則必須有一個網路路徑,從閘道電腦到實驗室電腦,透過共用同一虛擬網路或使用對等虛擬網路。

## <a name="configure-the-lab-to-use-token-authentication"></a>將實驗室設定為使用權杖認證 
本節演示如何將實驗室配置為使用支援權杖身份驗證的遠端桌面閘道。 本節不介紹如何設置遠端桌面閘道伺服器場本身。 有關此資訊,請參閱本文末尾的範例[以創建遠端桌面閘道](#sample-to-create-a-remote-desktop-gateway)部分。 

在更新實驗設置之前,請存儲成功執行函數以在實驗室密鑰保管庫中返回身份驗證令牌所需的密鑰。 您可以在 Azure 門戶中的函數**的「管理**」頁中獲取函數鍵值。 有關如何在金鑰保管庫中儲存機密的詳細資訊,請參閱[向金鑰保管庫添加機密](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)。 保存機密的名稱以供以後使用。

要查找實驗室金鑰保管庫的 ID,執行以下 Azure CLI 命令: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

使用以下步驟將實驗室配置為使用權杖身份驗證:

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中,選擇您的**實驗室**。
1. 在實驗室的頁面上,選擇 **「配置」和「策略**」。
1. 在左側功能表中,在 **「設置」** 部分中,選擇 **「實驗室設置**」 。。
1. 在 **「遠端桌面**」部分中,輸入遠端桌面服務閘道電腦或伺服器場的**閘道主機**欄位的完全限定功能變數名稱 (FQDN) 或 IP 位址。 此值必須與閘道電腦上使用的 TLS/SSL 憑證的 FQDN 匹配。

    ![實驗室設定中的遠端桌面選項](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在 **"遠端桌面**"部分中,對於**閘道杖權杖**金鑰,請輸入前面創建的秘密的名稱。 此值不是函數鍵本身,而是實驗室密鑰保管庫中保存函數鍵的機密的名稱。

    ![實驗室設定中的閘道權杖金鑰](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **儲存**變化。

    > [!NOTE] 
    > 以按下 **「儲存**」,即表示您同意[遠端桌面閘道的授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 有關遠端閘道的詳細資訊,請參閱[歡迎使用遠端桌面服務](https://aka.ms/rds)並[部署遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果首選透過自動化配置實驗室,請參閱[Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)瞭解用於設置**閘道主機名稱**和**閘道令牌金鑰**設置的示例 PowerShell 腳本。 [Azure 開發人員測試實驗室 GitHub 儲存庫](https://github.com/Azure/azure-devtestlab)還提供 Azure 資源管理器範本,該範本使用**閘道主機名稱**和**閘道令牌金鑰**設置創建或更新實驗室。

## <a name="configure-network-security-group"></a>設定網路安全群組
為了進一步保護實驗室,可以將網路安全組 (NSG) 添加到實驗室虛擬機器使用的虛擬網路中。 有關如何設定 NSG 的說明,請參閱[建立、更改或刪除網路安全群組](../virtual-network/manage-network-security-group.md)。

下面是一個僅允許首先通過閘道的流量到達實驗室電腦的 NSG 範例。 此規則中的來源是單一閘道電腦的 IP 位址,或閘道電腦前面的負載均衡器的 IP 位址。

![網路安全群組 ─ 規則](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>建立遠端桌面閘道的範例

> [!NOTE] 
> 使用範例樣本,您同意[遠端桌面閘道的授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 有關遠端閘道的詳細資訊,請參閱[歡迎使用遠端桌面服務](https://aka.ms/rds)並[部署遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure 開發人員測試實驗室 GitHub 儲存庫](https://github.com/Azure/azure-devtestlab)提供一些範例,以説明使用 DevTest Labs 設定使用權杖身份驗證和遠端桌面閘道所需的資源。 這些範例包括用於閘道電腦、實驗室設置和功能應用的 Azure 資源管理器範本。

按照以下步驟為遠端桌面閘道伺服器場設置範例解決方案。

1. 建立簽名證書。  執行[簽章憑證.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 保存所創建證書的指紋、密碼和 Base64 編碼。
2. 獲取 TLS/SSL 憑證。 與 TLS/SSL 憑證關聯的 FQDN 必須針對您控制的域。 保存此證書的指紋、密碼和 Base64 編碼。 要使用 PowerShell 獲取指紋,請使用以下命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    要使用 PowerShell 獲取 Base64 編碼,請使用以下命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 從[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)下載檔。

    該範本需要存取同一基礎URI中的一些其他資源管理器範本和相關資源。 將所有檔案從[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)和 RDGatewayFedAuth.msi 複製到存儲帳戶中的 blob 容器。  
4. 從[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)部署**azuredeploy.json。** 這個樣本採用以下參數:
    - 管理員使用者名 = 必需。  閘道電腦的管理員使用者名。
    - 管理員密碼 = 必需。 閘道電腦管理員帳戶的密碼。
    - 實體計數 = 要建立的閘道電腦數。  
    - 始終打開 = 指示是否將創建的 Azure 函數應用保持在溫暖狀態。 保留 Azure 函數應用將避免使用者首次嘗試連接到其實驗室 VM 時的延遲,但它確實會產生成本問題。  
    - 令牌生存期 = 創建權杖將有效的時間長度。 格式為 HH:MM:SS。
    - ssl 憑證 – 閘道電腦 TLS/SSL 憑證的 Base64 編碼。
    - ssl 憑證密碼 – 閘道電腦的 TLS/SSL 憑證的密碼。
    - ssl 憑證指紋 - 用於 TLS/SSL 憑證本地憑證儲存中的識別憑證指紋。
    - 簽署憑證 – 用於閘道電腦簽署憑證的 Base64 編碼。
    - 簽署憑證密碼 – 閘道電腦簽署憑證的密碼。
    - 簽署憑證指紋 - 簽署憑證的本地憑證儲存中的識別證書指紋。
    - _artifactsLocation = URI 位置,可在其中找到所有支持資源。 此值必須是完全限定的 UIR,而不是相對路徑。
    - _artifactsLocationSasToken = 共用存取簽名 (SAS) 權杖用於存取支援資源(如果位置是 Azure 儲存帳戶)。

    可以使用以下指令使用 Azure CLI 部署樣本:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    以下是參數的說明:

    - 可以通過`az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`運行 獲取 [存儲帳戶終結點]。  [存儲-acct 名稱] 是存儲帳戶的名稱,該帳戶保存您上載的檔。  
    - [容器名稱] 是儲存您上傳的檔案的 [儲存-acct 名稱] 中的容器的名稱。  
    - 可以通過`az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`運行 獲得 [sas-token]。 
        - [存儲-acct 名稱] 是存儲帳戶的名稱,該帳戶保存您上載的檔。  
        - [容器名稱] 是儲存您上傳的檔案的 [儲存-acct 名稱] 中的容器的名稱。  
        - [utc 過期日期] 是 SAS 令牌將過期的日期,SAS 令牌不能再用於存取儲存帳戶。

    從範本部署輸出中記錄閘道FQDN和閘道IP的值。 您還需要為新建立的函數保存函數鍵的值,可在[「功能應用設定」](../azure-functions/functions-how-to-use-azure-function-app-settings.md)選項卡中找到。
5. 設定 DNS,以便 TLS/SSL 憑證的 FQDN 從上一步直接到閘道IP的 IP 位址。

    創建遠端桌面閘道伺服器場並進行適當的 DNS 更新後,開發人員測試實驗室中的實驗室就可以使用它了。 閘道**主機名稱**和**閘道權杖金鑰**設置必須設定為使用已部署的閘道電腦。 

    > [!NOTE]
    > 如果實驗室電腦使用專用 IP,則必須有從閘道電腦到實驗室電腦的網路路徑,無論是透過共用同一虛擬網路還是使用對等虛擬網路。

    配置閘道和實驗室後,實驗室使用者按一**下 Connect**時建立的連接檔將自動包含使用權杖身份驗證進行連接所需的資訊。     

## <a name="next-steps"></a>後續步驟
請參考以下文章,瞭解此遠端桌面服務:[遠端桌面服務文件](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


