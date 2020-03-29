---
title: Azure 流量分析架構更新 - 2020 年 3 月 |微軟文檔
description: 在流量分析架構中使用新欄位的依例查詢。
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969068"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>在流量分析架構（2019 年 8 月架構更新）中具有新欄位的依例查詢

[流量分析日誌架構](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema)已更新，包括以下新欄位：SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s ** **。 **NSGRule_s** **SrcPublicIPs_s** 在接下來的幾個月裡，以下老油田將被棄用：VMIP_s、Subscription_g、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d。 **NIC_s** **VMIP_s** **Subscription_g** **Region_s** **NSGRules_s** **Subnet_s** **VM_s** **PublicIPs_s** **FlowCount_d**
新欄位提供有關源和目標 IP 的資訊並簡化查詢。

下面是三個示例，演示如何用新欄位替換舊欄位。

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>示例 1 - VMIP_s、Subscription_g、Region_s、Subnet_s、VM_s、NIC_s、PublicIPs_s

我們不必專門推斷 Azure 公共和外部公共流的"Azure"和"外部公共流"的源和目的案例，FlowDirection_s欄位為 Azure 公共和外部公共流。 對於 NVA（網路虛擬裝置），FlowDirection_s欄位也可能不適合使用。

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>示例 2 - NSGRules_s

前面的欄位是格式：<索引值 0）>|<NSG_RULENAME>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

早些時候，我們用於聚合 NSG 和 NSGRules 的資料。 現在我們不聚合。 因此NSGList_s只包含一個 NSG，NSGRules_s也用於僅包含一個規則。 因此，我們在此處刪除了複雜的格式，在以下其他欄位中可以找到相同的格式：

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>示例 3 - FlowCount_d

由於我們不通過 NSG 將資料進行俱樂部，因此FlowCount_d只是AllowedInFlows_d • DeniedInFlows_d = AllowedOutFlows_d = DeniedOutFlows_d。
上述 4 中只有 1 個為非零，其餘 3 個為 0。 它將指示捕獲流的 NIC 中的狀態和計數。

如果允許流，將填充以"允許"為綴的欄位之一。 否則，將填充一個以"已拒絕"為綴的欄位。
如果流是入站，將填充尾碼為"d"（\_如"InFlows_d"尾碼欄位）的欄位之一。 否則將填充"OutFlows_d"。

根據上述 2 個條件，我們知道將填充 4 個中的哪一個。


## <a name="next-steps"></a>後續步驟
要獲取常見問題解答，請參閱[流量分析常見問題解答](traffic-analytics-faq.md)有關功能的詳細資訊，請參閱[流量分析文檔](traffic-analytics.md)
