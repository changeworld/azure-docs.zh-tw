---
title: 將叢集更新為使用憑證一般名稱
description: 瞭解如何將 Service Fabric 叢集憑證從以指紋為基礎的宣告轉換成一般名稱。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368055"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>將叢集憑證從以指紋為基礎的宣告轉換為一般名稱
憑證 (堆疊的簽章稱為「指紋」 ) 是唯一的，這表示指紋所宣告的叢集憑證會參考特定的憑證實例。 接著，這會讓憑證變換和管理更為普遍且明確：每項變更都需要協調叢集和基礎運算主機的升級。 將 Service Fabric 叢集的憑證宣告從以指紋為基礎的憑證宣告轉換成以憑證的主體一般名稱為基礎的宣告，就能大幅簡化管理，尤其是在憑證上輪流，不再需要叢集升級。 本文說明如何將現有的叢集轉換為一般以名稱為基礎的宣告，而不需要停機。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>移至憑證授權單位單位 (CA) 簽署的憑證
如果叢集的安全性是由指紋宣告，則在不可能的情況下，或在不可行與另一個簽章相同的憑證的情況下進行計算。 在此情況下，憑證的來源比較不重要，因此自我簽署的憑證已足夠。 相反地，具有一般名稱所宣告之憑證的叢集安全性，會從公開金鑰基礎結構服務 (PKI) 發出該憑證，並包含其認證實務等層面，包括其運作安全性是否經過審核和其他許多方面。 基於這個理由，PKI 的選擇很重要、對簽發者 (憑證授權單位單位或 CA) 有深入的瞭解，而且自我簽署的憑證基本上是毫無價值。 一般名稱 (CN) 所宣告的憑證通常會被視為有效，如果它的鏈可以成功建立、主體具有預期的 CN 元素，而且其簽發者 (在) 鏈中的「立即」或「更高」，則會由執行驗證的代理程式信任。 Service Fabric 支援透過具有 ' 隱含 ' 簽發者的 CN 宣告憑證 (鏈必須以信任錨點結尾) ，或使用指紋宣告的簽發者 ( 「簽發者釘選」 ) ;如需詳細資訊，請參閱這  [篇文章](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) 。 若要使用指紋所宣告的自我簽署憑證將叢集轉換為一般名稱，則必須先將目標、CA 簽署的憑證依指紋導入到叢集中;只有這樣才可以從 TP 轉換成 CN。

基於測試目的，可透過 CN 宣告自我簽署憑證，將簽發者釘選到自己的指紋;從安全性的觀點來看，這幾乎等同于以 TP 宣告相同的憑證。 不過請注意，這種類型的成功轉換並不保證成功地從 TP 轉換為具有 CA 簽署憑證的 CN。 因此，建議使用適當的 CA 簽署憑證來測試轉換 () 中有可用的免費選項。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>上傳憑證並安裝在擴展集
在 Azure 中，取得和布建憑證的建議機制包含 Azure Key Vault 服務和其工具。 符合叢集憑證宣告的憑證必須布建到組成您叢集的虛擬機器擴展集的每個節點;如需進一步的詳細資料，請參閱 [虛擬機器擴展集上的密碼](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) 。 在叢集的憑證宣告進行變更之前，必須先在叢集的每個節點類型的 Vm 上安裝目前的和目標叢集憑證。 從憑證發行到布建至 Service fabric 節點的旅程將在 [這裡](cluster-security-certificate-management.md#the-journey-of-a-certificate)深入討論。

## <a name="bring-cluster-to-an-optimal-starting-state"></a>將叢集帶入最佳的啟動狀態
將憑證宣告從指紋式轉換成以一般名稱為基礎的影響兩者：

- 叢集中的每個節點如何找到並將其認證提供給其他節點
- 當每個節點建立安全連線時，如何驗證其對應的認證  

請檢查 [這兩個設定的簡報和驗證規則](cluster-security-certificates.md#certificate-configuration-rules) ，再繼續進行。 執行指紋轉換一般名稱轉換時，最重要的考慮是升級和尚未升級的節點 (也就是屬於不同升級網域的節點) 必須能夠在升級期間隨時執行成功的相互驗證。 若要達到此目的，建議您在初始升級中依指紋宣告目標/目標憑證，並在後續的程式中完成轉換為一般名稱。 如果叢集已處於建議的啟動狀態，則可以略過此區段。

轉換有多個有效的啟動狀態;非變異是叢集已經使用目標憑證 (在升級至一般名稱開始時由指紋) 宣告。 我們考慮 `GoalCert` `OldCert1` `OldCert2` ：

#### <a name="valid-starting-states"></a>有效的啟動狀態
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `GoalCert` 日期晚 `NotAfter` 于 `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `GoalCert` 日期晚 `NotAfter` 于 `OldCert1`

如果您的叢集不是上述的其中一個有效狀態，請參閱本文結尾的 < 關於如何達成該狀態的附錄。

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>選取所需的一般名稱型憑證驗證配置
如先前所述，Service Fabric 支援透過具有隱含信賴起點的 CN 宣告憑證，或是明確地釘選簽發者指紋。 請參閱 [這篇文章](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) 以取得詳細資訊，並確定您已充分瞭解差異，以及選擇任一種機制的含意。 在語法上，此差異/選擇取決於參數的值 `certificateIssuerThumbprintList` ：空白表示依賴信任的根 CA (信賴起點) ，而一組指紋會限制允許的叢集憑證直接簽發者。

   > [!NOTE]
   > ' CertificateIssuerThumbprint ' 欄位允許指定主體一般名稱所宣告之憑證的預期直接簽發者。 可接受的值為一或多個逗點分隔的 SHA1 指紋。 請注意，這是憑證驗證的增強-如果未指定簽發者/清單是空的，則會接受憑證進行驗證（如果可以建立憑證鏈），並在驗證程式信任的根目錄中結束。 如果指定了一或多個簽發者指紋，當其直接簽發者的指紋（從鏈中解壓縮）符合此欄位中指定的任何值時，就會接受憑證，不論根是否受信任。 請注意，PKI 可能會使用不同的憑證授權單位單位 ( 「簽發者」 ) 來簽署具有特定主旨的憑證，因此請務必為該主體指定所有預期的簽發者指紋。 換句話說，憑證的更新並不保證是由與要更新之憑證相同的簽發者所簽署。
   >
   > 指定簽發者是最佳的做法；雖然省略它仍然可以運作 (針對鏈結至受信任根目錄的憑證)，此行為仍具有限制，並可能會在不久的將來被移除。 此外，請注意到在 Azure 中部署、受到由私人 PKI 簽發的 X509 憑證所保護，並由主體所宣告的叢集，在該 PKI 的憑證原則不可探索、不可用且不可存取的情況下，該叢集可能會無法由 Azure Service Fabric 服務進行驗證 (針對叢集對服務通訊)。 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>更新叢集的 Azure 資源管理 (ARM) 範本和部署
建議您使用 ARM 範本來管理 Azure Service Fabric 叢集;另一種方法是使用 json 成品，也就是 [Azure 資源總管 (preview) ](https://resources.azure.com)。 Azure 入口網站目前無法使用相同的體驗。 如果無法使用對應至現有叢集的原始範本，則可以在 Azure 入口網站中取得對等的範本，方法是流覽至包含該叢集的資源群組，選取 [**自動化**] 左側功能表中的 [**匯出範本**]，然後再選取所需的資源;至少應該匯出虛擬機器擴展集和叢集資源。 也可以下載產生的範本。 請注意，此範本可能需要在完整部署之前變更，而且可能不符合原始的變更-它會反映叢集資源的目前狀態。

必要的變更如下所示：
    - 更新虛擬機器資源) 下 (Service Fabric 節點擴充功能的定義;如果叢集定義了多個節點類型，您將需要更新每個對應虛擬機器擴展集的定義
    - 更新叢集資源定義

以下提供詳細的範例。

### <a name="updating-the-virtual-machine-scale-set-resources"></a>正在更新虛擬機器擴展集資源 (s) 
寄件者
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
收件者
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="updating-the-cluster-resource"></a>正在更新叢集資源
在**ServiceFabric/** 叢集資源中，新增具有**CommonNames**設定的 **>certificatecommonnames**屬性，並將 [**憑證**] 屬性全部移除 (其所有設定) ：

寄件者
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
收件者
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

如需詳細資訊，請參閱 [部署使用憑證一般名稱而非指紋的 Service Fabric 叢集。](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>部署更新的範本
完成變更之後，重新部署更新的範本。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>附錄：達成將叢集轉換為以 CN 為基礎之憑證宣告的有效啟動狀態

| 開始狀態 | 升級 1 | 升級 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 而且 `GoalCert` 的日期晚 `NotAfter` 于 `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 而且 `OldCert1` 的日期晚 `NotAfter` 于 `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `OldCert1` 日期晚 `NotAfter` 于 `GoalCert` | 升級至 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `OldCert1` 日期晚 `NotAfter` 于 `GoalCert` | 升級至 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | 移除其中一個 `OldCert1` 或 `OldCert2` 以進入狀態 `Thumbprint: OldCertx, ThumbprintSecondary: None` | 從新的啟動狀態繼續 |

如需有關如何執行這些升級的指示，請參閱 [這份檔](service-fabric-cluster-security-update-certs-azure.md)。


## <a name="next-steps"></a>後續步驟
* 瞭解叢集 [安全性](service-fabric-cluster-security.md)。
* 瞭解如何依照 [一般名稱來變換叢集憑證](service-fabric-cluster-rollover-cert-cn.md)
* 瞭解如何[設定 touchless autorollover 的](cluster-security-certificate-management.md)叢集

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
