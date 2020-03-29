---
title: 將實驗室配置為在 Azure 開發人員測試實驗室中使用遠端桌面閘道
description: 瞭解如何使用遠端桌面閘道在 Azure DevTest 實驗室中配置實驗室，以確保安全訪問實驗室 VM，而無需公開 RDP 埠。
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
ms.openlocfilehash: 88daecdf4490ffd4eef45e6cd664a16f86bad113
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170291"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>在 Azure 開發人員測試實驗室中配置實驗室以使用遠端桌面閘道
在 Azure DevTest 實驗室中，可以為實驗室配置遠端桌面閘道，以確保安全訪問實驗室虛擬機器 （VM），而無需公開 RDP 埠。 該實驗室為您的實驗室使用者提供了一個中心位置，以便查看和連接到他們有權訪問的所有虛擬機器。 **"虛擬機器**"頁上的 **"連接**"按鈕將創建一個特定于電腦的 RDP 檔，您可以打開該檔以連接到電腦。 通過將實驗室連接到遠端桌面閘道，您可以進一步自訂 RDP 連接並保護 RDP 連接。 

此方法更安全，因為實驗室使用者直接對閘道電腦進行身份驗證，或者可以使用域加入的閘道電腦上的公司憑據連接到其電腦。 該實驗室還支援對閘道電腦使用權杖身份驗證，該閘道電腦允許使用者連接到其實驗室虛擬機器，而無需將 RDP 埠暴露給 Internet。 本文介紹了如何設置使用權杖身份驗證連接到實驗室電腦的實驗室的示例。

## <a name="architecture-of-the-solution"></a>解決方案的體系結構

![解決方案的體系結構](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. 當您選擇"**連接**"按鈕時，將調用["獲取 RDP 檔內容"](/rest/api/dtl/virtualmachines/getrdpfilecontents)操作。 
1. 獲取 RDP 檔內容操作調用`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`以請求身份驗證權杖。
    1. `{gateway-hostname}`是在 Azure 門戶中的**實驗室的"實驗室設置"** 頁上指定的閘道主機名稱。 
    1. `{lab-machine-name}`是您嘗試連接的電腦的名稱。
    1. `{port-number}`是需要進行連接的埠。 通常這個埠是3389。 如果實驗室 VM 在 DevTest 實驗室中使用[共用 IP](devtest-lab-shared-ip.md)功能，則埠將不同。
1. 遠端桌面閘道將調用從`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`Azure 函數延遲到 Azure 函數以生成身份驗證權杖。 DevTest Labs 服務會自動在請求標頭中包含功能金鑰。 功能鍵將保存在實驗室的金鑰保存庫中。 該機密的名稱在實驗室的 **"實驗室設置"** 頁上顯示為**閘道權杖金鑰**。
1. Azure 函數應返回權杖，用於對閘道電腦進行基於證書的權杖身份驗證。  
1. 然後，獲取 RDP 檔內容操作返回完整的 RDP 檔，包括身份驗證資訊。
1. 使用首選 RDP 連接程式打開 RDP 檔。 請記住，並非所有 RDP 連接程式都支援權杖身份驗證。 身份驗證權杖確實具有由函數應用設置的到期日期。 在權杖過期之前與實驗室 VM 建立連接。
1. 遠端桌面閘道電腦對 RDP 檔中的權杖進行身份驗證後，連接將轉發到實驗室電腦。

### <a name="solution-requirements"></a>解決方案需求
要使用 DevTest Labs 權杖身份驗證功能，閘道電腦、功能變數名稱服務 （DNS） 和功能有一些配置要求。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>遠端桌面閘道電腦的要求
- 必須在閘道電腦上安裝 SSL 憑證才能處理 HTTPS 流量。 如果只有一台電腦，證書必須匹配閘道伺服器場的負載等化器的完全限定功能變數名稱 （FQDN） 或電腦本身的 FQDN。 萬用字元 SSL 憑證不起作用。  
- 安裝在閘道電腦上的簽章憑證。 使用[Create-簽章憑證.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)腳本創建簽章憑證。
- 安裝支援遠端桌面閘道權杖身份驗證[的可插拔身份驗證](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)模組。 這種模組的一個示例是`RDGatewayFedAuth.msi`[系統中心虛擬機器管理器 （VMM） 映射](/system-center/vmm/install-console?view=sc-vmm-1807)附帶的一個示例。 有關系統中心的詳細資訊，請參閱[系統中心文檔](https://docs.microsoft.com/system-center/)和[定價詳細資訊](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 閘道伺服器可以處理對`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`發出的請求。

    閘道主機名稱是閘道伺服器場負載等化器的 FQDN，或者只有一台電腦時電腦本身的 FQDN。 是`{lab-machine-name}`您嘗試連接的實驗室電腦的名稱，`{port-number}`也是將在其上進行連接的埠。  預設情況下，此埠為 3389。  但是，如果虛擬機器在 DevTest 實驗室中使用[共用 IP](devtest-lab-shared-ip.md)功能，則埠將不同。
- Internet 資訊伺服器 （IIS）[的應用程式路由請求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模組可用於將請求重定向`https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`到 azure 函數，該函數處理獲取權杖進行身份驗證的請求。


## <a name="requirements-for-azure-function"></a>Azure 函數的要求
Azure 函數處理具有 的`https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}`格式的請求，並基於閘道電腦上安裝的相同簽章憑證返回身份驗證權杖。 是`{function-app-uri}`用於訪問函數的 uri。 函數鍵將自動在請求的標頭中傳遞。 有關示例函數，請參閱[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)。 


## <a name="requirements-for-network"></a>網路要求

- 與閘道電腦上安裝的 SSL 憑證關聯的 FQDN 的 DNS 必須將流量定向到閘道電腦或閘道電腦伺服器場的負載等化器。
- 如果實驗室電腦使用專用 IP，則必須有一個網路路徑，從閘道電腦到實驗室電腦，通過共用同一虛擬網路或使用對等虛擬網路。

## <a name="configure-the-lab-to-use-token-authentication"></a>將實驗室配置為使用權杖身份驗證 
本節演示如何將實驗室配置為使用支援權杖身份驗證的遠端桌面閘道電腦。 本節不介紹如何設置遠端桌面閘道伺服器場本身。 有關此資訊，請參閱本文末尾的示例[以創建遠端桌面閘道](#sample-to-create-a-remote-desktop-gateway)部分。 

在更新實驗設置之前，請存儲成功執行函數以在實驗室金鑰保存庫中返回身份驗證權杖所需的金鑰。 您可以在 Azure 門戶中的函數**的"管理**"頁中獲取函數鍵值。 有關如何在金鑰保存庫中保存機密的詳細資訊，請參閱[向金鑰保存庫添加機密](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)。 保存機密的名稱以供以後使用。

要查找實驗室金鑰保存庫的 ID，運行以下 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

使用以下步驟將實驗室配置為使用權杖身份驗證：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選擇您的**實驗室**。
1. 在實驗室的頁面上，選擇 **"配置"和"策略**"。
1. 在左側功能表中，在 **"設置"** 部分中，選擇 **"實驗室設置**"。
1. 在 **"遠端桌面**"部分中，輸入遠端桌面服務閘道電腦或伺服器場的**閘道主機**欄位的完全限定功能變數名稱 （FQDN） 或 IP 位址。 此值必須與閘道電腦上使用的 SSL 憑證的 FQDN 匹配。

    ![實驗室設置中的遠端桌面選項](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在 **"遠端桌面**"部分中，對於**閘道權杖**金鑰，請輸入前面創建的秘密的名稱。 此值不是函數鍵本身，而是實驗室金鑰保存庫中保存函數鍵的機密的名稱。

    ![實驗室設置中的閘道權杖金鑰](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **保存**變化。

    > [!NOTE] 
    > 通過按一下 **"保存**"，即表示您同意[遠端桌面閘道的許可證條款](https://www.microsoft.com/licensing/product-licensing/products)。 有關遠端閘道的詳細資訊，請參閱[歡迎使用遠端桌面服務](https://aka.ms/rds)並[部署遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果首選通過自動化配置實驗室，請參閱[Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)瞭解用於設置**閘道主機名稱**和**閘道權杖金鑰**設置的示例 PowerShell 腳本。 [Azure 開發人員測試實驗室 GitHub 存儲庫](https://github.com/Azure/azure-devtestlab)還提供 Azure 資源管理器範本，該範本使用**閘道主機名稱**和**閘道權杖金鑰**設置創建或更新實驗室。

## <a name="configure-network-security-group"></a>配置網路安全性群組
為了進一步保護實驗室，可以將網路安全性群組 （NSG） 添加到實驗室虛擬機器使用的虛擬網路中。 有關如何設置 NSG 的說明，請參閱[創建、更改或刪除網路安全性群組](../virtual-network/manage-network-security-group.md)。

下面是一個僅允許首先通過閘道的流量到達實驗室電腦的 NSG 示例。 此規則中的源是單個閘道電腦的 IP 位址，或閘道電腦前面的負載等化器的 IP 位址。

![網路安全性群組 - 規則](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>創建遠端桌面閘道的示例

> [!NOTE] 
> 通過使用示例範本，您同意[遠端桌面閘道的許可證條款](https://www.microsoft.com/licensing/product-licensing/products)。 有關遠端閘道的詳細資訊，請參閱[歡迎使用遠端桌面服務](https://aka.ms/rds)並[部署遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure 開發人員測試實驗室 GitHub 存儲庫](https://github.com/Azure/azure-devtestlab)提供了一些示例，以説明使用 DevTest Labs 設置使用權杖身份驗證和遠端桌面閘道所需的資源。 這些示例包括用於閘道電腦、實驗室設置和功能應用的 Azure 資源管理器範本。

按照以下步驟為遠端桌面閘道伺服器場設置示例解決方案。

1. 創建簽章憑證。  運行[創建簽章憑證.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 保存所創建證書的指紋、密碼和 Base64 編碼。
2. 取得 SSL 憑證。 與 SSL 憑證關聯的 FQDN 必須針對您控制的域。 保存此證書的指紋、密碼和 Base64 編碼。 要使用 PowerShell 獲取指紋，請使用以下命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    要使用 PowerShell 獲取 Base64 編碼，請使用以下命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 從[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)下載檔案。

    該範本需要訪問同一基礎 URI 中的一些其他資源管理器範本和相關資源。 將所有檔從[https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway)和 RDGatewayFedAuth.msi 複製到存儲帳戶中的 blob 容器。  
4. 從[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)部署**azuredeploy.json。** 該範本採用以下參數：
    - 管理員使用者名 = 必需。  閘道電腦的管理員使用者名。
    - 管理員密碼 = 必需。 閘道電腦管理員帳戶的密碼。
    - 實例計數 = 要創建的閘道電腦數。  
    - 始終打開 = 指示是否將創建的 Azure 函數應用保持在溫暖狀態。 保留 Azure 函數應用將避免使用者首次嘗試連接到其實驗室 VM 時的延遲，但它確實會產生成本問題。  
    - 權杖存留期 = 創建權杖將有效的時間長度。 格式為 HH：MM：SS。
    - ssl 證書 – 閘道電腦 SSL 憑證的 Base64 編碼。
    - ssl 證書密碼 – 閘道電腦的 SSL 憑證的密碼。
    - ssl 證書指紋 - SSL 憑證的本地憑證存放區中的標識證書指紋。
    - 簽章憑證 – 用於閘道電腦簽章憑證的 Base64 編碼。
    - 簽章憑證密碼 – 閘道電腦簽章憑證的密碼。
    - 簽章憑證指紋 - 簽章憑證的本地憑證存放區中的標識證書指紋。
    - _artifactsLocation = URI 位置，可在其中找到所有支援資源。 此值必須是完全限定的 UIR，而不是相對路徑。
    - _artifactsLocationSasToken = 共用訪問簽名 （SAS） 權杖用於訪問支援資源（如果位置是 Azure 存儲帳戶）。

    可以使用以下命令使用 Azure CLI 部署範本：

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    以下是參數的說明：

    - 可以通過運行`az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`獲取 [存儲帳戶終結點]。  [存儲-acct 名稱] 是存儲帳戶的名稱，該帳戶保存您上載的檔。  
    - [容器名稱] 是保存您上載的檔的 [存儲-acct 名稱] 中的容器的名稱。  
    - 可以通過運行`az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`獲得 [sas-token]。 
        - [存儲-acct 名稱] 是存儲帳戶的名稱，該帳戶保存您上載的檔。  
        - [容器名稱] 是保存您上載的檔的 [存儲-acct 名稱] 中的容器的名稱。  
        - [utc 過期日期] 是 SAS 權杖將過期的日期，SAS 權杖不能再用於訪問存儲帳戶。

    從範本部署輸出中記錄閘道FQDN和閘道IP的值。 您還需要為新創建的函數保存函數鍵的值，可在["功能應用設置"](../azure-functions/functions-how-to-use-azure-function-app-settings.md)選項卡中找到。
5. 配置 DNS，以便 SSL 憑證的 FQDN 從上一步直接到閘道IP的 IP 位址。

    創建遠端桌面閘道伺服器場並進行適當的 DNS 更新後，開發人員測試實驗室中的實驗室就可以使用它了。 閘道**主機名稱**和**閘道權杖金鑰**設置必須配置為使用已部署的閘道電腦。 

    > [!NOTE]
    > 如果實驗室電腦使用專用 IP，則必須有從閘道電腦到實驗室電腦的網路路徑，無論是通過共用同一虛擬網路還是使用對等虛擬網路。

    配置閘道和實驗室後，實驗室使用者按一下**Connect**時創建的連接檔將自動包含使用權杖身份驗證進行連接所需的資訊。     

## <a name="next-steps"></a>後續步驟
請參閱以下文章，瞭解有關遠端桌面服務：[遠端桌面服務文檔](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


