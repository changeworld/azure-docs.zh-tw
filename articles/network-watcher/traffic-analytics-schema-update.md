---
title: Azure 流量分析架構更新 - 2020 年 3 月 |微軟文件
description: 在流量分析架構中使用新欄位的範例查詢。
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
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396615"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>在流量分析架構(2019 年 8 月架構更新)中使用新欄位的範例查詢

[流量分析紀錄架構](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema)包括以下新欄位 **:SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s** ** **。 **DestPublicIPs_s** 新欄位提供有關源 IP 和目標 IP 的資訊,並簡化查詢。

在接下來的幾個月里,以下老油田將被棄用:VMIP_s、Subscription_g、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d。 **NIC_s** **VMIP_s** **Subscription_g** **Region_s** **NSGRules_s** **Subnet_s** **VM_s** **PublicIPs_s** **FlowCount_d**

以下三個示例演示如何用新欄位替換舊欄位。

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>範例 1:VMIP_s、Subscription_g、Region_s、Subnet_s、VM_s、NIC_s和PublicIPs_s欄位

我們不必從 Azure 公共和外部公共流的**FlowDirection_s**欄位中推斷源和目標案例。 對於網路虛擬設備,使用**FlowDirection_s**欄位也不合適。

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

## <a name="example-2-nsgrules_s-field"></a>範例 2:NSGRules_s欄位

舊欄位使用格式:

<索引值 0)>|<NSG_规则名称>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

我們不再跨網路安全組 (NSG) 聚合數據。 在更新的架構中 **,NSGList_s**僅包含一個 NSG。 此外 **,NSGRules**只包含一個規則。 我們在此處和其他欄位中刪除了複雜的格式設置,如示例中所示。

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

## <a name="example-3-flowcount_d-field"></a>範例 3:FlowCount_d欄位

因為我們不在整個 NSG 中支援數據,**因此FlowCount_d**只是:

**AllowedInFlows_d**** ** **AllowedOutFlows_d** AllowedInFlows_dDeniedInFlows_dAllowedOutFlows_dDeniedOutFlows_d  +  ** **  +   + 

四個字段中只有一個為非零。 其他三個字段將為零。 欄位填充以指示捕獲流的 NIC 中的狀態和計數。

為了說明這些條件:

- 如果允許流,將填充其中一個"允許"前綴欄位。
- 如果流被拒絕,將填充其中一個"已拒絕"前綴欄位。
- 如果流是入站,將填充其中一個"InFlows_d"後綴欄位。
- 如果流是出站,將填充其中一個"OutFlows_d"後綴欄位。

根據條件,我們知道將填充四個字段中的哪一個。

## <a name="next-steps"></a>後續步驟

- 若要取得常見問題的解答，請參閱[流量分析常見問題集](traffic-analytics-faq.md)。
- 要檢視有關功能的詳細資訊,請參考[資料分析文件](traffic-analytics.md)。
