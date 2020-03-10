---
title: Azure Service Fabric 安全性最佳做法
description: 維護 Azure Service Fabric 叢集和應用程式安全的最佳作法和設計考慮。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365056"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric 安全性 

如需 [Azure 安全性最佳做法](https://docs.microsoft.com/azure/security/)的相關詳細資訊，請參閱 [Azure Service Fabric 安全性最佳做法](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) 是建議採用的 Azure Service Fabric 應用程式和叢集的秘密管理服務。
> [!NOTE]
> 如果 Key Vault 的憑證/秘密已部署至虛擬機器擴展集作為虛擬機器擴展集祕密，Key Vault 和虛擬機器擴展集必須位於相同位置。

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>建立發出 Service Fabric 憑證的憑證授權單位

您可以建立 Azure Key Vault 憑證，或將它匯入至 Key Vault。 當建立 Key Vault 憑證時，會在 Key Vault 內部建立私密金鑰，並且絕不會向憑證擁有者公開。 以下是在 Key Vault 中建立憑證的方式：

- 建立自我簽署憑證以建立公開-私密金鑰組，並與憑證建立關聯。 憑證會由自己擁有的金鑰簽署。 
- 手動建立新憑證以建立公開-私密金鑰組，並產生 X.509 憑證簽署要求。 簽署要求可以由您的登錄授權單位或憑證授權單位簽署。 經過簽署的 x509 憑證能與擱置中的金鑰組合併，在 Key Vault 中完成 KV 憑證。 這個方式的步驟比較繁瑣，不過能確實提高安全性，因為私密金鑰僅限在 Key Vault 中建立。 下圖提供相關說明。 

檢閱[Azure 金鑰保存庫憑證建立方法](https://docs.microsoft.com/azure/key-vault/create-certificate)了解其他詳細資訊。

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>將 Key Vault 憑證部署至 Service Fabric 叢集虛擬機器擴展集

若要將憑證從位於相同位置的金鑰保存庫部署至虛擬機器擴展集，請使用虛擬機器擴展集 [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)。 下列是 Resource Manager 範本屬性：

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> 必須啟用保存庫才能進行 Resource Manager 範本部署。

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>將存取控制清單 (ACL) 套用至您 Service Fabric 叢集的憑證

[虛擬機器擴展集延伸模組](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) 發行者 Microsoft.Azure.ServiceFabric 是用來設定您的節點安全性的。
若要針對您的 Service Fabric 叢集程序將 ACL 套用至憑證，請使用下列 Resource Manager 範本屬性：

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>透過一般名稱保護 Service Fabric 叢集憑證

若要透過憑證 `Common Name` Service Fabric 叢集，請使用 Resource Manager 範本屬性 [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)，如下所示：

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric 叢集將會使用在主機的憑證存放區中找到的第一個有效憑證。 在 Windows 中，這將會是到期日期最慢、並且與一般名稱和簽發者指紋相符的憑證。

Azure 網域，例如 *\<YOUR SUBDOMAIN\>.cloudapp.azure.com 或 \<YOUR SUBDOMAIN\>.trafficmanager.net，由 Microsoft 擁有。 憑證授權單位不會發出網域憑證給未經授權的使用者。 大部分的使用者必須向註冊機構購買網域，或本身為授權的網域系統管理員，憑證授權單位才會發出具備該一般名稱的憑證給您。

如需有關如何設定 DNS 服務以將網域解析為 Microsoft IP 位址的其他詳細資訊，請檢閱如何設定 [Azure DNS 來裝載您的網域](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。

> [!NOTE]
> 將網域名稱伺服器委派給您的 Azure DNS 區域名稱伺服器之後，請將下列兩項記錄新增至您的 DNS 區域：
> - 不是您的自訂網域將解析之所有 IP 位址 `Alias record set` 的網域 APEX 'A' 記錄。
> - 不是 `Alias record set` 之您所佈建 Microsoft 子網域的 'C' 記錄。 例如，可以使用您的流量管理員或負載平衡器的 DNS 名稱。

若要更新入口網站以顯示您 Service Fabric 叢集 `"managementEndpoint"` 的自訂 DNS 名稱，請更新下列 Service Fabric 叢集 Resource Manager 範本屬性：

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>加密 Service Fabric 封裝秘密值

Service Fabric 封裝中會經過加密的常見值包括 Azure Container Registry (ACR) 認證、環境變數、設定和 Azure 磁碟區外掛程式儲存體帳戶金鑰。

若要[設定加密憑證，並在 Windows 叢集上將祕密加密](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)：

產生自我簽署的憑證以加密您的秘密：

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

使用[將 Key Vault 憑證部署至 Service Fabric 叢集虛擬機器擴展集](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets)中的指示，將 Key Vault 憑證部署至您 Service Fabric 叢集的虛擬機器擴展集。

使用以下 PowerShell 命令將您的秘密加密，然後使用加密的值更新您的 Service Fabric 應用程式資訊清單：

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

若要[設定加密憑證，並在 Linux 叢集上將祕密加密](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)：

產生自我簽署的憑證以加密您的秘密：

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

使用[將 Key Vault 憑證部署至 Service Fabric 叢集虛擬機器擴展集](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets)中的指示，將 Key Vault 憑證部署至您 Service Fabric 叢集的虛擬機器擴展集。

使用以下命令將您的秘密加密，然後使用加密的值更新您的 Service Fabric 應用程式資訊清單：

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

將受保護的值加密之後，[在 Service Fabric 應用程式中指定加密的祕密](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)，然後[從服務程式碼將加密的秘密解密](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code)。

## <a name="include-certificate-in-service-fabric-applications"></a>在 Service Fabric 應用程式中包含憑證

若要讓您的應用程式存取秘密，請將**SecretsCertificate**元素新增至應用程式資訊清單，以包含憑證。

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>使用受控服務識別 (MSI) 向 Azure 資源驗證 Service Fabric 應用程式

若要了解適用於 Azure 資源的受控識別，請參閱[什麼是適用於 Azure 資源的受控識別？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。
Azure Service Fabric 叢集裝載在虛擬機器擴展集中，支援[受控服務識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)。
若要取得 MSI 可用來進行驗證的服務清單，請參閱[支援 Azure Active Directory 驗證的 Azure 服務](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication)。


若要在虛擬機器擴展集建立期間，或在現有的虛擬機器擴展集上，啟用系統指派的受控識別，請宣告以下 `"Microsoft.Compute/virtualMachinesScaleSets"` 屬性：

```json
"identity": { 
    "type": "SystemAssigned"
}
```
如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)。

如果建立[使用者指派的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)，請在您的範本中宣告以下資源，以將它指派給您的虛擬機器擴展集。 請將 `\<USERASSIGNEDIDENTITYNAME\>` 取代為您建立的使用者指派受控識別名稱：

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

在您的 Service Fabric 應用程式可以使用受控識別之前，必須先將權限授與需要向它驗證的 Azure 資源。
下列命令會授與存取權給 Azure 資源：

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

在您的 Service Fabric 應用程式代碼中，藉由建立 Azure Resource Manager 的[存取權杖](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http)，其方式與下列內容相同：

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

然後您的 Service Fabric 應用程式就可以使用存取權杖，向支援 Active Directory 的 Azure 資源進行驗證。
以下範例示範如何針對 Cosmos DB 資源執行此作業：

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows 安全性基準
[我們建議您執行廣為知名且經過妥善測試的業界標準設定，例如 Microsoft 安全性基準，而不是自行建立基準](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines);在您的虛擬機器擴展集上布建這些功能的選項是使用 Azure Desired State Configuration （DSC）延伸模組處理常式，在 Vm 上線時進行設定，使其執行生產環境軟體。

## <a name="azure-firewall"></a>Azure 防火牆
[Azure 防火牆是受控的雲端式網路安全性服務，可保護您的 Azure 虛擬網路資源。它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端擴充性。](https://docs.microsoft.com/azure/firewall/overview)這讓您能夠將輸出 HTTP/S 流量限制為指定的完整功能變數名稱（FQDN）清單，包括萬用字元。 這項功能不需要 SSL 終止。 建議您利用適用于 Windows Update 的[Azure 防火牆 FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)標籤，並讓 Microsoft Windows Update 端點的網路流量可以流經您的防火牆。 [使用範本部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/deploy-template)提供了適用于 Microsoft 網路/azureFirewalls 資源範本定義的範例。 Service Fabric 應用程式通用的防火牆規則，是針對您的叢集虛擬網路允許下列各項：

- \* download.microsoft.com
- \* servicefabric.azure.com
- *.core.windows.net

這些防火牆規則可讓您的允許輸出網路安全性群組（包括 ServiceFabric 和儲存體）成為虛擬網路中允許的目的地。

## <a name="tls-12"></a>TLS 1.2
[技術](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

根據預設，Windows Server 2016 上已安裝 Windows Defender 防毒軟體。 如需詳細資訊，請參閱 [Windows Server 2016 上的 Windows Defender 防毒軟體](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)。 某些 SKU 上會預設安裝使用者介面，但並不需要。 若要降低因 Windows Defender 造成的任何效能影響和資源過度耗用，以及如果您的安全性原則可讓您排除開放原始碼軟體的處理程序和路徑，請宣告下列虛擬機器擴展集延伸模組 Resource Manager 範本屬性，以將 Service Fabric 叢集從掃描作業中排除：


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> 如果您沒有使用 Windows Defender，請參閱您的反惡意程式碼文件，以了解設定規則。 Linux 不支援 Windows Defender。

## <a name="platform-isolation"></a>平臺隔離
根據預設，Service Fabric 應用程式會被授與 Service Fabric 執行時間本身的存取權，這會以不同的形式來列出本身：[環境變數](service-fabric-environment-variables-reference.md)會指向對應至應用程式和網狀架構檔案之主機上的檔案路徑、可接受應用程式特定要求的處理序間通訊端點，以及網狀架構預期應用程式用來驗證本身的用戶端憑證。 在服務裝載本身不受信任程式碼的可能性中，建議您停用此 SF 執行時間的存取權，除非明確需要。 在應用程式資訊清單的 [原則] 區段中，會使用下列宣告來移除執行時間的存取： 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 Vm 或電腦上建立叢集：[針對 Windows server 建立 Service Fabric](service-fabric-cluster-creation-for-windows-server.md)叢集。
* 在執行 Linux 的 Vm 或電腦上建立叢集：[建立 linux](service-fabric-cluster-creation-via-portal.md)叢集。
* 了解 [Service Fabric 支援選項](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
