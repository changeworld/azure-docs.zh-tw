---
title: 設定實驗室以使用 Azure DevTest Labs 中的遠端桌面閘道
description: 瞭解如何使用遠端桌面閘道在 Azure DevTest Labs 中設定實驗室，以確保能夠安全地存取實驗室 Vm，而不需要公開 RDP 埠。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 68cb830c765a71b06f9732c4062be23d9e7f67d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483834"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>在 Azure DevTest Labs 中設定您的實驗室以使用遠端桌面閘道
在 Azure DevTest Labs 中，您可以為您的實驗室設定遠端桌面閘道，以確保能夠安全地存取實驗室虛擬機器（Vm），而不需要公開 RDP 埠。 實驗室提供一個集中的位置，讓您的實驗室使用者能夠查看並聯機到他們可以存取的所有虛擬機器。 [**虛擬機器**] 頁面上的 [連線 **]** 按鈕會建立電腦特定的 RDP 檔案，您可以開啟該檔案來連接到電腦。 您可以藉由將您的實驗室連接至遠端桌面閘道，進一步自訂和保護 RDP 連線。 

這種方法比較安全，因為實驗室使用者會直接向閘道電腦進行驗證，或在已加入網域的閘道電腦上使用公司認證來連線到其電腦。 實驗室也支援對閘道機器使用權杖驗證，讓使用者可以連線到其實驗室虛擬機器，而不需要將 RDP 埠公開至網際網路。 本文會逐步解說如何設定實驗室，以使用權杖驗證來連線至實驗室電腦的範例。

## <a name="architecture-of-the-solution"></a>解決方案的架構

![解決方案的架構](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. 當您選取 [連線] 按鈕時，會呼叫 [[取得 RDP 檔案內容](/rest/api/dtl/virtualmachines/getrdpfilecontents) **]** 動作。1。 
1. [取得 RDP 檔案內容] 動作會叫用 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 以要求驗證權杖。
    1. `{gateway-hostname}`是在 Azure 入口網站的實驗室 [**實驗室設定**] 頁面上指定的閘道主機名稱。 
    1. `{lab-machine-name}`這是您嘗試連線的電腦名稱稱。
    1. `{port-number}`這是需要進行連接的埠。 此埠通常是3389。 如果實驗室 VM 使用 DevTest Labs 中的[共用 IP](devtest-lab-shared-ip.md)功能，埠會不同。
1. 遠端桌面閘道會將來自的呼叫延遲 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 到 Azure 函式，以產生驗證權杖。 DevTest Labs 服務會自動在要求標頭中包含函數金鑰。 函式金鑰會儲存在實驗室的金鑰保存庫中。 在實驗室的 [**實驗室設定**] 頁面上，該密碼要顯示為 [**閘道權杖密碼**] 的名稱。
1. Azure 函式預期會針對閘道機器傳回以憑證為基礎的權杖驗證權杖。  
1. [取得 RDP 檔案內容] 動作接著會傳回完整的 RDP 檔案，包括驗證資訊。
1. 您可以使用慣用的 RDP 連線程式來開啟 RDP 檔案。 請記住，並非所有的 RDP 連接程式都支援權杖驗證。 驗證權杖的到期日是由函式應用程式所設定。 在權杖到期之前，建立與實驗室 VM 的連線。
1. 當遠端桌面閘道機器在 RDP 檔案中驗證權杖之後，連線就會轉送到您的實驗室電腦。

### <a name="solution-requirements"></a>解決方案需求
若要使用 DevTest Labs 權杖驗證功能，閘道機器、功能變數名稱服務（DNS）和功能有幾個設定需求。

### <a name="requirements-for-remote-desktop-gateway-machines"></a>遠端桌面閘道電腦的需求
- 必須在閘道電腦上安裝 TLS/SSL 憑證，才能處理 HTTPS 流量。 憑證必須符合閘道伺服器陣列負載平衡器的完整功能變數名稱（FQDN），或電腦本身的 FQDN （如果只有一部電腦）。 萬用字元 TLS/SSL 憑證無法運作。  
- 已安裝在閘道電腦上的簽署憑證。 使用[Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)腳本來建立簽署憑證。
- 安裝支援遠端桌面閘道權杖驗證的[插入式驗證](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273)模組。 這類別模組的其中一個範例就是 `RDGatewayFedAuth.msi` [SYSTEM CENTER VIRTUAL MACHINE MANAGER （VMM）映射](/system-center/vmm/install-console?view=sc-vmm-1807)隨附。 如需 System Center 的詳細資訊，請參閱[System center 檔](https://docs.microsoft.com/system-center/)和[定價詳細資料](https://www.microsoft.com/cloud-platform/system-center-pricing)。  
- 閘道伺服器可以處理對提出的要求 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 。

    如果只有一部電腦，則閘道主機名稱為閘道伺服器陣列的負載平衡器 FQDN，或電腦本身的 FQDN。 `{lab-machine-name}`是您嘗試連線之實驗室電腦的名稱，而 `{port-number}` 是將在其上建立連接的埠。  根據預設，此埠是3389。  不過，如果虛擬機器使用 DevTest Labs 中的[共用 IP](devtest-lab-shared-ip.md)功能，埠會不同。
- Internet Information Server （IIS）的[應用程式路由要求](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)模組可用來將要求重新導向至 azure 函式，此函式會 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 處理取得權杖以進行驗證的要求。


## <a name="requirements-for-azure-function"></a>Azure function 的需求
Azure 函式會處理格式為的要求 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` ，並根據閘道機器上所安裝的相同簽署憑證傳回驗證權杖。 `{function-app-uri}`是用來存取函數的 uri。 函式金鑰會自動在要求的標頭中傳遞。 如需範例函式，請參閱 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) 。 


## <a name="requirements-for-network"></a>網路的需求

- 與安裝在閘道電腦上的 TLS/SSL 憑證相關聯之 FQDN 的 DNS，必須將流量導向閘道電腦伺服器陣列的閘道機器或負載平衡器。
- 如果實驗室電腦使用私人 Ip，則必須透過共用相同的虛擬網路或使用對等互連的虛擬網路，從閘道機器到實驗室電腦的網路路徑。

## <a name="configure-the-lab-to-use-token-authentication"></a>將實驗室設定為使用權杖驗證 
本節說明如何設定實驗室，以使用支援權杖驗證的遠端桌面閘道電腦。 本節並未涵蓋如何設定遠端桌面閘道伺服器陣列本身。 如需相關資訊，請參閱本文結尾處的[建立遠端桌面閘道的範例](#sample-to-create-a-remote-desktop-gateway)一節。 

在您更新實驗室設定之前，請先儲存成功執行函式所需的金鑰，以傳回實驗室金鑰保存庫中的驗證權杖。 您可以在 Azure 入口網站的函式的 [**管理**] 頁面中取得函數金鑰值。 如需有關如何將秘密儲存在金鑰保存庫中的詳細資訊，請參閱[將密碼新增至 Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)。 儲存密碼的名稱以供稍後使用。

若要尋找實驗室金鑰保存庫的識別碼，請執行下列 Azure CLI 命令： 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

使用下列步驟，將實驗室設定為使用權杖驗證：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選取您的**實驗室**。
1. 在實驗室的頁面上，選取 [設定**和原則**]。
1. 在左側功能表的 [**設定**] 區段中，選取 [**實驗室設定**]。
1. 在 [**遠端桌面**] 區段中，為 [**閘道主機名稱**] 欄位輸入遠端桌面服務閘道電腦或伺服器陣列的完整功能變數名稱（FQDN）或 IP 位址。 此值必須符合閘道電腦上使用的 TLS/SSL 憑證的 FQDN。

    ![實驗室設定中的遠端桌面選項](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. 在 [**遠端桌面**] 區段的 [**閘道權杖**秘密] 中，輸入稍早建立的密碼名稱。 這個值不是函式索引鍵本身，而是實驗室的金鑰保存庫中的秘密名稱，其中保留了函數金鑰。

    ![實驗室設定中的閘道權杖秘密](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **儲存**變更.

    > [!NOTE] 
    > 按一下 [**儲存**]，即表示您同意[遠端桌面閘道的授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 如需遠端閘道的詳細資訊，請參閱[歡迎使用遠端桌面服務](https://aka.ms/rds)並[部署您的遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。


如果偏好透過「自動化」設定實驗室，請參閱[Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) ，以取得範例 PowerShell 腳本來設定**閘道主機名稱**和**閘道權杖秘密**設定。 [Azure DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-devtestlab)也提供 Azure Resource Manager 範本，可使用**閘道主機名稱**和**閘道權杖秘密**設定來建立或更新實驗室。

## <a name="configure-network-security-group"></a>設定網路安全性群組
若要進一步保護實驗室，可以將網路安全性群組（NSG）新增至實驗室虛擬機器所使用的虛擬網路。 如需如何設定 NSG 的指示，請參閱[建立、變更或刪除網路安全性群組](../virtual-network/manage-network-security-group.md)。

以下範例 NSG 僅允許第一次通過閘道的流量到達實驗室機器。 此規則中的來源是單一閘道電腦的 IP 位址，或閘道機器前方的負載平衡器 IP 位址。

![網路安全性群組-規則](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>建立遠端桌面閘道的範例

> [!NOTE] 
> 藉由使用範例範本，即表示您同意[遠端桌面閘道的授權條款](https://www.microsoft.com/licensing/product-licensing/products)。 如需遠端閘道的詳細資訊，請參閱[歡迎使用遠端桌面服務](https://aka.ms/rds)並[部署您的遠端桌面環境](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

[Azure DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-devtestlab)提供一些範例，可協助您設定使用權杖驗證和遠端桌面閘道搭配 DevTest Labs 所需的資源。 這些範例包括閘道機器、實驗室設定和函數應用程式的 Azure Resource Manager 範本。

請遵循下列步驟來設定遠端桌面閘道伺服器陣列的範例解決方案。

1. 建立簽署憑證。  執行[Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)。 儲存所建立憑證的指紋、密碼和 Base64 編碼。
2. 取得 TLS/SSL 憑證。 與 TLS/SSL 憑證相關聯的 FQDN 必須適用于您所控制的網域。 儲存此憑證的指紋、密碼和 Base64 編碼。 若要使用 PowerShell 取得憑證指紋，請使用下列命令。

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    若要使用 PowerShell 取得 Base64 編碼，請使用下列命令。

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. 從下載檔案 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) 。

    此範本需要在相同的基底 URI 存取一些其他 Resource Manager 範本和相關資源。 將所有檔案從 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) 和 RDGatewayFedAuth.msi 複製到儲存體帳戶中的 blob 容器。  
4. 從部署**azuredeploy.js** [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) 。 此範本會採用下列參數：
    - adminUsername –必要。  閘道機器的系統管理員使用者名稱。
    - adminPassword –必要。 閘道電腦之系統管理員帳戶的密碼。
    - instanceCount –要建立的閘道機器數目。  
    - alwaysOn-指出是否要將建立的 Azure Functions 應用程式保持在暖狀態。 保留 Azure Functions 應用程式可避免使用者第一次嘗試連線到其實驗室 VM 時延遲，但會有成本上的影響。  
    - tokenLifetime –建立的權杖生效的時間長度。 格式為 HH： MM： SS。
    - sslCertificate –閘道電腦的 TLS/SSL 憑證的 Base64 編碼。
    - sslCertificatePassword –閘道電腦的 TLS/SSL 憑證的密碼。
    - sslCertificateThumbprint-TLS/SSL 憑證的本機憑證存放區中識別的憑證指紋。
    - signCertificate –閘道機器簽署憑證的 Base64 編碼方式。
    - signCertificatePassword –閘道電腦簽署憑證的密碼。
    - signCertificateThumbprint-簽署憑證的本機憑證存放區中識別的憑證指紋。
    - _artifactsLocation –可以找到所有支援資源的 URI 位置。 此值必須是完整的 UIR，而不是相對路徑。
    - _artifactsLocationSasToken –如果位置是 Azure 儲存體帳戶，用來存取支援資源的共用存取簽章（SAS）權杖。

    您可以使用下列命令，使用 Azure CLI 來部署範本：

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    以下是參數的描述：

    - 您可以藉由執行來取得 {storage-account-endpoint} `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` 。  {儲存體-帳戶名稱} 是存放您上傳之檔案的儲存體帳戶名稱。  
    - {Container-name} 是 {storage-「帳戶名稱}」中的容器名稱，其中包含您上傳的檔案。  
    - 您可以藉由執行來取得 {sas token} `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` 。 
        - {儲存體-帳戶名稱} 是存放您上傳之檔案的儲存體帳戶名稱。  
        - {Container-name} 是 {storage-「帳戶名稱}」中的容器名稱，其中包含您上傳的檔案。  
        - {Utc-逾期-date} 是 SAS 權杖到期的日期，而 SAS 權杖無法再用來存取儲存體帳戶（UTC）。

    從範本部署輸出中記錄 gatewayFQDN 和 gatewayIP 的值。 您也需要儲存新建立之函式的函式金鑰值，這可以在 [[函數應用程式設定](../azure-functions/functions-how-to-use-azure-function-app-settings.md)] 索引標籤中找到。
5. 設定 DNS，讓 TLS/SSL 憑證的 FQDN 從上一個步驟導向至 gatewayIP 的 IP 位址。

    建立遠端桌面閘道伺服器陣列並建立適當的 DNS 更新之後，DevTest Labs 中的實驗室就可以使用它。 **閘道主機名稱**和**閘道權杖秘密**設定必須設定為使用您所部署的閘道電腦。 

    > [!NOTE]
    > 如果實驗室電腦使用私人 Ip，則必須透過共用相同的虛擬網路或使用對等互連的虛擬網路，從閘道機器到實驗室電腦的網路路徑。

    一旦設定閘道和實驗室，當實驗室使用者按一下連線時所建立的連線檔案，**就會**自動包含使用權杖驗證進行連接所需的資訊。     

## <a name="next-steps"></a>後續步驟
請參閱下列文章，以深入瞭解遠端桌面服務：[遠端桌面服務檔](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


