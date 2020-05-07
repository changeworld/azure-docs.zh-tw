---
title: 升級叢集的 Azure Service Fabric 版本
description: 升級執行 Service Fabric 叢集的 Service Fabric 程式碼和/或組態，包括設定叢集更新模式、升級憑證、新增應用程式連接埠、修補作業系統等等。 執行升級時，您可以期待些什麼？
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 802e7402f60370b7151c5e373c8a4921a5af7c80
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789595"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>升級叢集的 Service Fabric 版本

對於任何現代系統，可升級性的設計是產品達到長期成功的關鍵。 Azure Service Fabric 叢集是您所擁有，但部分由 Microsoft 管理的資源。 本文說明如何將在 Azure 叢集中執行的 Service Fabric 版本升級。

您可以將叢集設定為在 Microsoft 釋出網狀架構升級時自動接收該升級，或者，您也可以選取您想讓叢集執行的受支援網狀架構版本。

作法是在入口網站上設定 “upgrademode” 叢集組態，或是建立時或稍後在即時叢集上使用 Resource Manager 來設定。 

> [!NOTE]
> 請務必保持您的叢集一律執行支援的網狀架構版本。 當我們宣布發行新版本的 Service Fabric 時，從當日起至少 60 天後，舊版就會標示為結束支援。 新的版本會於 [Service Fabric 小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/)發佈。 那時就有新的版本可選擇。 
> 
> 

叢集執行的版本在到期前 14 天會產生健全狀況事件，使您的叢集進入警告健全狀況狀態。 在您升級至支援的網狀架構版本之前，叢集會停留在警告狀態。

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>在 Azure 入口網站中設定升級模式
當您建立叢集時，您可以將叢集設為自動或手動。

![Create_Manualmode][Create_Manualmode]

在即時叢集上，您可以利用管理體驗將叢集設為自動或手動。 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>在設定為手動模式的叢集上，透過入口網站升級至新的版本。
若要升級至新的版本，只需要從下拉式清單中選取可用的版本並儲存即可。 Fabric 升級會自動開始進行。 升級期間會遵守叢集健康狀態原則 (綜合節點健康狀態和所有在叢集中執行之應用程式的健康狀態)。

如果不符合叢集健康狀態原則，則會回復升級。 請往下捲動本文，以深入了解如何設定這些自訂的健康狀態原則。 

在解決導致復原的問題後，您需要依照之前的相同步驟再次起始升級。

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>使用 Resource Manager 範本設定升級模式
將 “upgradeMode" 組態新增至 Microsoft.ServiceFabric/clusters 資源定義，並將 “clusterCodeVersion" 設為其中一個支援的網狀架構版本，如下所示，然後部署範本。 “upgradeMode" 的有效值為 “Manual” 或 “Automatic”。

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>在設定為手動模式的叢集上，透過 Resource Manager 範本升級至新的版本。
當叢集處於手動模式時，若要升級至新的版本，請將 “clusterCodeVersion" 變更為支援的版本並部署。 部署範本時會自動展開 Fabric 升級。 升級期間會遵守叢集健康狀態原則 (綜合節點健康狀態和所有在叢集中執行之應用程式的健康狀態)。

如果不符合叢集健康狀態原則，則會回復升級。  

在解決導致復原的問題後，您需要依照之前的相同步驟再次起始升級。

## <a name="set-custom-health-polices-for-upgrades"></a>設定升級的自訂健康情況原則
您可以為網狀架構升級指定自訂的健全狀況原則。 如果您已將叢集設定為自動網狀架構升級，則這些原則會套用至[自動網狀架構升級的階段 1](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)。
如果已將您的叢集設定為手動網狀架構升級，則每當您選取新版本而觸發系統開始在叢集中展開網狀架構升級時，就會套用這些原則。 如果您不覆寫原則，則會使用預設值。

在 [網狀架構升級] 刀鋒視窗下，您可以選取進階升級設定來指定自訂的健康狀態原則，或檢閱目前的設定。 檢閱下列作法圖片。 

![管理自訂的健康狀態原則][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>針對指定的訂用帳戶列出所有環境的所有可用版本
執行下列命令，應該會得到類似如下的輸出。

“supportExpiryUtc” 會告訴您給定的版本即將到期或已過期。 最新版本並無有效日期 - 它的值為 "9999-12-31T23:59:59.9999999"，這只是表示到期日還沒有設定。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>後續步驟
* 瞭解如何自訂一些[service fabric 叢集網狀架構設定](service-fabric-cluster-fabric-settings.md)
* 了解如何 [相應放大和相應縮小叢集](service-fabric-cluster-scale-in-out.md)
* 瞭解[應用程式升級](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
