---
title: 節點類型和虛擬機器擴展集
description: 瞭解 Azure 服務結構節點類型如何與虛擬機器縮放集相關，以及如何遠端連線到規模集實例或叢集節點。
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199711"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 節點類型與虛擬機器擴展集

[虛擬機器擴展集](/azure/virtual-machine-scale-sets)是 Azure 計算資源。 您可以使用擴展集來將虛擬機器集合以一組的方式加以部署和管理。 在 Azure Service Fabric 群集中定義的每個節點類型都完全設置一個比例集：多個節點類型不能由同一比例集備份，並且一個節點類型不應（在大多數情況下）由多個比例集備份。 例外情況是[，在節點類型垂直縮放](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)的罕見情況下，當您暫時具有兩個具有相同`nodeTypeRef`值的比例集，而副本從原始比例集遷移到升級的比例集時。

服務交換矩陣運行時安裝在*Microsoft.Azure.ServiceFabric*虛擬機器擴展設置的規模中的每台虛擬機器上。 您可以分開相應增加或減少每個節點類型、變更每個叢集節點上執行的 OS SKU、開啟不同組的連接埠，並使用不同的容量計量。

下圖顯示了具有兩個節點類型的群集，稱為*FrontEnd*和*BackEnd*。 每個節點類型各有五個節點。

![有兩個節點類型的叢集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>將虛擬機器擴展集執行個體對應至節點

如上圖所示，擴展集執行個體是由執行個體 0 開始編號，然後再逐個增加 1。 編號會反映在節點名稱中。 例如，BackEnd_0 節點是 BackEnd 擴展集的執行個體 0。 這個特定的擴展集有五個執行個體，名稱分別是 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

當您相應增加擴展集，系統就會建立一個新的執行個體。 新擴展集執行個體的名稱通常是擴展集名稱 + 下一個執行個體編號。 在我們的範例中是 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>將擴展集負載平衡器對應至節點類型和擴展集

如果您已 Azure 入口網站中部署叢集，或已使用範例 Azure Resource Manager 範本部署叢集，系統會列出資源群組下的所有資源都。 可以看到每個擴展集或節點類型的負載平衡器。 負載平衡器的名稱使用下列格式：**LB-&lt;節點類型名稱&gt;**。 例如下圖中顯示的 LB-sfcluster4doc-0：

![資源][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>服務結構虛擬機器擴展

服務結構虛擬機器擴展用於將服務結構引導到 Azure 虛擬機器，並配置節點安全性。

以下是服務結構虛擬機器擴展段：

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

以下是屬性說明：

| **名稱** | **允許的值** | **指導或簡短描述** |
| --- | --- | --- | --- |
| NAME | 字串 | 擴展的唯一名稱 |
| type | "服務結構Linux節點"或"服務結構視窗節點" | 標識 OS 服務交換矩陣正在引導到 |
| 自動升級小版本 | true 或 false | 啟用 SF 運行時次要版本的自動升級 |
| publisher | 微軟.Azure.服務結構 | 服務結構擴展發行者的名稱 |
| 群集 Endpont | 字串 | URI：從點到管理終結點 |
| nodeTypeRef | 字串 | 節點類型的名稱 |
| 耐久性級別 | 青銅， 銀， 金， 鉑金 | 允許暫停不可變 Azure 基礎結構的時間 |
| 啟用並行作業 | true 或 false | 啟用計算並行作業，如並行使用相同的比例設置中刪除 VM 並重新啟動 VM |
| nicprefixOverride | 字串 | 子網首碼，如"10.0.0.0/24" |
| 通用名稱 | string[] | 已安裝群集證書的常見名稱 |
| x509商店名稱 | 字串 | 已安裝群集證書所在的存儲名稱 |
| typeHandlerVersion | 1.1 | 擴展版本。 建議將 1.0 經典版擴展升級到 1.1 |
| 資料路徑 | 字串 | 用於為 Service Fabric 系統服務和應用程式資料保存狀態的磁碟機的路徑。

## <a name="next-steps"></a>後續步驟

* 請參閱[「到處部署」功能和與 Azure 受控叢集比較的概觀](service-fabric-deploy-anywhere.md)。
* 瞭解[群集安全性](service-fabric-cluster-security.md)。
* [遠端連線](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)到特定擴展集執行個體
* 部署後在叢集 VM 上[更新 RDP 連接埠範圍值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 變更叢集 VM 的[管理員使用者名稱和密碼](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
