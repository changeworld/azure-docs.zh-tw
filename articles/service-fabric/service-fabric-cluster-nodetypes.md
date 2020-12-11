---
title: 節點類型和虛擬機器擴展集
description: 瞭解 Azure Service Fabric 節點類型與虛擬機器擴展集的關聯性，以及如何從遠端連線到擴展集實例或叢集節點。
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.openlocfilehash: 9e30c02de54806006a1881448bcb9f788a57310c
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095248"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 節點類型與虛擬機器擴展集

[虛擬機器擴展集](../virtual-machine-scale-sets/index.yml)是 Azure 計算資源。 您可以使用擴展集來將虛擬機器集合以一組的方式加以部署和管理。 您在 Azure Service Fabric 叢集中定義的每個節點類型都只會設定一個擴展集：多個節點類型不能受到相同擴展集的支援，且一個節點類型不應由多個擴展集支援。

*ServiceFabric* 虛擬機器擴充功能會在擴展集中的每部虛擬機器上安裝 Service Fabric 執行時間。 您可以分開相應增加或減少每個節點類型、變更每個叢集節點上執行的 OS SKU、開啟不同組的連接埠，並使用不同的容量計量。

下圖顯示具有兩個節點類型的叢集，名為 *前端* 和 *後端*。 每個節點類型各有五個節點。

![有兩個節點類型的叢集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>將虛擬機器擴展集執行個體對應至節點

如上圖所示，擴展集執行個體是由執行個體 0 開始編號，然後再逐個增加 1。 編號會反映在節點名稱中。 例如，BackEnd_0 節點是 BackEnd 擴展集的執行個體 0。 這個特定的擴展集有五個執行個體，名稱分別是 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

當您相應放大擴展集時，會建立新的實例。 新擴展集執行個體的名稱通常是擴展集名稱 + 下一個執行個體編號。 在我們的範例中是 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>將擴展集負載平衡器對應至節點類型和擴展集

如果您已 Azure 入口網站中部署叢集，或已使用範例 Azure Resource Manager 範本部署叢集，系統會列出資源群組下的所有資源都。 可以看到每個擴展集或節點類型的負載平衡器。 負載平衡器的名稱使用下列格式：**LB-&lt;節點類型名稱&gt;**。 例如下圖中顯示的 LB-sfcluster4doc-0：

![螢幕擷取畫面顯示已反白顯示兩個負載平衡器的資源群組。][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 虛擬機器擴充功能

Service Fabric 的虛擬機器擴充功能可用來啟動 Azure 虛擬機器 Service Fabric，以及設定節點安全性。

以下是 Service Fabric 虛擬機器擴充功能的程式碼片段：

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

以下是屬性描述：

| **名稱** | **允許的值** | **指引或簡短描述** |
| --- | --- | --- | --- |
| NAME | 字串 | 延伸模組的唯一名稱 |
| 類型 | "ServiceFabricLinuxNode" 或 "ServiceFabricWindowsNode" | 識別啟動的作業系統 Service Fabric |
| autoUpgradeMinorVersion | true 或 false | 啟用 SF Runtime 次要版本的自動升級 |
| publisher | ServiceFabric | Service Fabric 副檔名發行者的名稱 |
| clusterEndpont | 字串 | URI：管理端點的埠 |
| nodeTypeRef | 字串 | NodeType 的名稱 |
| durabilityLevel | 銅級、銀級、金級、白金級 | 允許暫停不可變 Azure 基礎結構的時間 |
| enableParallelJobs | true 或 false | 啟用計算 ParallelJobs，例如移除 VM，並以平行方式在相同擴展集中重新開機 VM |
| nicPrefixOverride | 字串 | 子網首碼，例如 "10.0.0.0/24" |
| commonNames | string[] | 已安裝叢集憑證的一般名稱 |
| 上 x509storename | 字串 | 已安裝的叢集憑證所在的存放區名稱 |
| typeHandlerVersion | 1.1 | 延伸模組的版本。 建議將1.0 版的延伸模組升級為1。1 |
| 資料路徑 | 字串 | 磁片磁碟機的路徑，用於儲存 Service Fabric 系統服務和應用程式資料的狀態。

## <a name="next-steps"></a>後續步驟

* 請參閱[「到處部署」功能和與 Azure 受控叢集比較的概觀](service-fabric-deploy-anywhere.md)。
* 瞭解叢集 [安全性](service-fabric-cluster-security.md)。
* [遠端連線](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)到特定擴展集執行個體
* 部署後在叢集 VM 上[更新 RDP 連接埠範圍值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 變更叢集 VM 的[管理員使用者名稱和密碼](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
