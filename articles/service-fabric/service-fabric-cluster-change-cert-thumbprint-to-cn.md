---
title: 將叢集更新為使用憑證一般名稱
description: 瞭解如何將 Azure Service Fabric 叢集憑證從以指紋為基礎的宣告轉換成一般名稱。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900783"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>將叢集憑證從以指紋為基礎的宣告轉換為一般名稱

憑證的簽章 (通常稱為指紋) 是唯一的。 指紋所宣告的叢集憑證會參考特定的憑證實例。 這項明確的功能使得憑證變換和一般管理、困難且明確的管理。 每項變更都需要協調叢集和基礎運算主機的升級。

將 Azure Service Fabric 叢集的憑證宣告從以指紋為基礎的憑證宣告轉換成以憑證的主體一般名稱為基礎的宣告 (CN) 可大幅簡化管理。 尤其是，在憑證上輪流，不再需要叢集升級。 本文說明如何將現有的叢集轉換成以 CN 為基礎的宣告，而不需要停機。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>移至憑證授權單位單位簽署的憑證

其憑證由指紋宣告的叢集安全性，會在不可能或計算不可行的情況下，將憑證與另一個簽章偽造。 在此情況下，憑證的來源比較不重要，因此自我簽署的憑證已足夠。

相反地，由 CN 流程宣告其憑證的叢集安全性，會來自叢集擁有者在其憑證提供者中的隱含信任。 提供者是發行憑證 (PKI) 服務的公開金鑰基礎結構。 在 PKI 的憑證實務上，信任是以其他因素為基礎，也就是他們的營運安全性是否由其他受信任的當事人進行審核及核准等等。

叢集擁有者也必須詳細瞭解 (Ca) 發出憑證的憑證授權單位單位，因為這是依主旨驗證憑證的基本層面。 這也表示自我簽署憑證完全不適合此用途。 任何人都可以產生具有指定主旨的憑證。

在下列情況下，CN 所宣告的憑證通常會視為有效：

* 其鏈可以成功建立。
* 主體具有預期的 CN 元素。
* 其簽發者 (在鏈中的直屬或更高，) 由執行驗證的代理程式所信任。

Service Fabric 支援以兩種方式透過 CN 宣告憑證：

* 使用 *隱含* 簽發者，這表示鏈必須以信任錨點結尾。
* 使用指紋宣告的簽發者，這稱為「簽發者釘選」。

如需詳細資訊，請參閱以 [名稱為基礎的一般憑證驗證聲明](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)。

若要使用憑證指紋所宣告的自我簽署憑證來轉換叢集，必須先依指紋將目標、CA 簽署的憑證引進到叢集。 只有這樣才可以從指紋轉換成 CN。

基於測試目的，CN *可以* 宣告自我簽署的憑證，但只有在簽發者已釘選到自己的指紋時才能使用。 從安全性的觀點來看，這個動作幾乎等同于依指紋宣告相同的憑證。 此類型的成功轉換並不保證成功地從指紋轉換為具有 CA 簽署憑證的 CN。 建議您使用適當的 CA 簽署憑證來測試轉換。 這種測試有免費的選項。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>上傳憑證並安裝在擴展集

在 Azure 中，取得和布建憑證的建議機制包含 Azure Key Vault 和其工具。 符合叢集憑證宣告的憑證必須布建至組成叢集的虛擬機器擴展集的每個節點。 如需詳細資訊，請參閱 [虛擬機器擴展集上的密碼](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm)。

在叢集的憑證宣告中進行變更之前，請務必在叢集的每個節點類型的虛擬機器上安裝目前的和目標叢集憑證。 憑證發佈到 Service Fabric 節點的進行布建的旅程，將在 [憑證旅程](cluster-security-certificate-management.md#the-journey-of-a-certificate)中深入討論。

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>讓叢集進入最佳的啟動狀態

將憑證宣告從以指紋為基礎的轉換成以 CN 為基礎的影響：

- 叢集中的每個節點如何尋找並將其認證提供給其他節點。
- 每個節點在建立安全連線時，如何驗證其對應的認證。

在您繼續之前，請先檢查 [這兩項設定的簡報與驗證規則](cluster-security-certificates.md#certificate-configuration-rules) 。 當您執行指紋對 CN 轉換時，最重要的考慮是升級和尚未升級的節點 (也就是屬於不同升級網域的節點) 必須能夠在升級期間隨時執行成功的相互驗證。 若要達到這種行為，建議的方法是在初始升級中依指紋宣告目標或目標憑證。 然後完成轉換至後續的 CN。 如果叢集已處於建議的啟動狀態，您可以略過本節。

轉換有多個有效的啟動狀態。 非變異是叢集已經使用目標憑證 (在升級到 CN 開始時由指紋) 宣告。 我們 `GoalCert` `OldCert1` `OldCert2` 在本文中考慮、和。

#### <a name="valid-starting-states"></a>有效的啟動狀態

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `GoalCert` 日期晚 `NotBefore` 于 `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `GoalCert` 日期晚 `NotBefore` 于 `OldCert1`

> [!NOTE]
> 在版本 7.2.445 (7.2 CU4) 之前，Service Fabric 選取最新的到期憑證 (最遠為 ' NotAfter ' 屬性) 的憑證，因此 7.2 CU4 之前的啟動狀態需要 GoalCert 的 `NotAfter` 日期晚于 `OldCert1`

如果您的叢集不是先前所述的其中一個有效狀態，請參閱本文結尾一節中有關達到該狀態的資訊。

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>選取想要的以 CN 為基礎的憑證驗證配置

如先前所述，Service Fabric 支援透過具有隱含信賴起點的 CN 宣告憑證，或是明確地釘選簽發者指紋。 如需詳細資訊，請參閱以 [名稱為基礎的一般憑證驗證聲明](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)。

確定您已充分瞭解這些差異，以及選擇任一機制的含意。 在語法上，此差異或選擇取決於參數的值 `certificateIssuerThumbprintList` 。 空白表示依賴信任的根 CA (信賴起點) ，而一組指紋會限制叢集憑證允許的直接簽發者。

   > [!NOTE]
   > 此 `certificateIssuerThumbprint` 欄位可讓您指定主旨 CN 所宣告之憑證的預期直接簽發者。 可接受的值為一或多個逗點分隔的 SHA1 指紋。 此動作會增強憑證驗證。
   >
   > 如果未指定簽發者或清單是空的，則會接受憑證以進行驗證（如果可以建立憑證鏈）。 憑證接著會在驗證程式信任的根目錄中結束。 如果指定了一或多個簽發者指紋，當其直接簽發者的指紋（從鏈中解壓縮）符合此欄位中指定的任何值時，就會接受憑證。 無論根是否受信任，都會接受憑證。
   >
   > PKI 可能會使用不同的憑證授權單位單位 (也稱為簽發 *者) 來* 簽署具有指定主旨的憑證。 基於這個理由，請務必為該主體指定所有預期的簽發者指紋。 換句話說，憑證的更新並不保證是由與要更新之憑證相同的簽發者所簽署。
   >
   > 指定簽發者被視為最佳做法。 略過簽發者將可繼續用於連結至受根信任的憑證，但這種行為有一些限制，可能會在不久的將來淘汰。 在 Azure 中部署的叢集，會使用私人 PKI 所發行的 X509 憑證來保護，而且可能無法透過叢集對服務通訊) 的 Service Fabric (進行驗證。 驗證需要 PKI 的憑證原則可以探索、可用且可供存取。

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>更新叢集的 Azure Resource Manager 範本並部署

使用 Azure Resource Manager (ARM) 範本管理您的 Service Fabric 叢集。 另一種方法是使用 JSON 成品，也就是 [Azure 資源總管 (preview) ](https://resources.azure.com)。 Azure 入口網站目前無法使用相同的體驗。

如果無法使用對應至現有叢集的原始範本，則可以在 Azure 入口網站中取得對等的範本。 移至包含叢集的資源群組，然後從左側的 [**自動化**] 功能表選取 [**匯出範本**]。 然後選取您想要的資源。 至少應該匯出虛擬機器擴展集和叢集資源。 也可以下載產生的範本。 此範本可能需要在完整部署之前進行變更。 此範本也可能完全不符合原始範本。 它會反映叢集資源的目前狀態。

必要的變更如下所示：

- 更新虛擬機器資源) 下 (Service Fabric 節點擴充功能的定義。 如果叢集定義了多個節點類型，您必須更新每個對應的虛擬機器擴展集的定義。
- 正在更新叢集資源定義。

這裡包含詳細的範例。

### <a name="update-the-virtual-machine-scale-set-resources"></a>更新虛擬機器擴展集資源
寄件者: 
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
變更為：
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

### <a name="update-the-cluster-resource"></a>更新叢集資源

在 **ServiceFabric/** 叢集資源中，新增具有 **CommonNames** 設定的 **>certificatecommonnames** 屬性，並) 所有的設定中 (完全移除 **憑證** 屬性。

寄件者: 
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
變更為：
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

如需詳細資訊，請參閱 [部署使用憑證一般名稱而非指紋的 Service Fabric](./service-fabric-create-cluster-using-cert-cn.md)叢集。

## <a name="deploy-the-updated-template"></a>部署更新的範本

進行變更之後，請重新部署更新的範本。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>達成將叢集轉換為以 CN 為基礎之憑證宣告的有效啟動狀態

| 開始狀態 | 升級 1 | 升級 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 而且 `GoalCert` 的日期晚 `NotBefore` 于 `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 而且 `OldCert1` 的日期晚 `NotBefore` 于 `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `OldCert1` 日期晚 `NotBefore` 于 `GoalCert` | 升級至 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `OldCert1` 日期晚 `NotBefore` 于 `GoalCert` | 升級至 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | 移除其中一個 `OldCert1` 或 `OldCert2` 以進入狀態 `Thumbprint: OldCertx, ThumbprintSecondary: None` | 從新的啟動狀態繼續 |

> [!NOTE]
> 若為7.2.445 版之前版本的叢集 (7.2 CU4) ，請將取代 `NotBefore` 為 `NotAfter` 上述狀態中的。

如需如何執行任何這些升級的指示，請參閱 [管理 Azure Service Fabric](service-fabric-cluster-security-update-certs-azure.md)叢集中的憑證。

## <a name="next-steps"></a>後續步驟

* 瞭解叢集 [安全性](service-fabric-cluster-security.md)。
* 瞭解如何依照 [一般名稱](service-fabric-cluster-rollover-cert-cn.md)變換叢集憑證。
* 瞭解如何 [設定 touchless autorollover](cluster-security-certificate-management.md)的叢集。

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
