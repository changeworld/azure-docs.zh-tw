---
title: 沒有800計數限制的資源
description: 列出可在資源群組中有超過800個實例的 Azure 資源類型。
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 6a63fd7e41c03b542f4a43b483243702c5be5f14
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034929"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>資源不限於每個資源群組800個實例

根據預設，您可以在每個資源群組中部署最多800個資源類型的實例。 不過，某些資源類型會豁免800實例的限制。 本文列出可在資源群組中有超過800個實例的 Azure 資源類型。 所有其他資源類型的限制為800個實例。

針對某些資源類型，您必須聯絡支援人員，以移除800個實例的限制。 本文會注明這些資源類型。

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* >botservices.cs-預設限制為800個實例。 您可以聯繫支援人員來增加該限制。

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachineScaleSets-預設限制為800個實例。 您可以聯繫支援人員來增加該限制。
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* 登錄/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* 登錄/buildTasks/步驟/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftd365customerinsights"></a>D365CustomerInsights

* 執行個體

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* labs/virtualMachines-預設限制為800個實例。 
* schedules

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* 服務

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* 命名空間

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* 機器-最多支援5000個實例
* 擴充功能-支援不限數目的 VM 延伸模組實例

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/磁片區
* netAppAccounts/capacityPools/磁片區/mountTargets
* netAppAccounts/capacityPools/磁片區/快照集

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/全部
* privateDnsZones/Registrationenabled
* privateEndpoints
* privateLinkServices
* publicIPAddresses-預設限制為800個實例。 您可以聯繫支援人員來增加該限制。
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-預設限制為800個實例。 您可以聯繫支援人員來增加該限制。

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* 容量-預設限制為800個實例。 您可以聯繫支援人員來增加該限制。

## <a name="microsoftrelay"></a>Microsoft.Relay

* 命名空間

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* 命名空間

## <a name="microsoftsingularity"></a>Singularity

* accounts
* 帳戶/accountQuotaPolicies
* 帳戶/groupPolicies
* 帳戶/作業
* 帳戶/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>下一步

如需配額和限制的完整清單，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](azure-subscription-service-limits.md)。
