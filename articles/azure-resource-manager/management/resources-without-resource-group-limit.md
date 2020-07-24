---
title: 沒有800計數限制的資源
description: 列出資源群組中可有超過800個實例的 Azure 資源類型。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 6f7b45ec3691bc3a48d9aa3dfa072c1c68189007
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040755"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>每個資源群組的資源不限於800實例

根據預設，您可以在每個資源群組中部署最多800個資源類型的實例。 不過，某些資源類型會豁免800實例的限制。 本文列出在資源群組中可以有超過800個實例的 Azure 資源類型。 所有其他資源類型的限制為800實例。

針對某些資源類型，您必須聯絡支援人員以移除800實例的限制。 這篇文章中注明了這些資源類型。

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-根據預設，限制為800個實例。 這項限制可以透過聯絡支援人員來增加。

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
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

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servers
* serversv2
* singleServers

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* 排程-根據預設，限制為800實例。 這項限制可以透過聯絡支援人員來增加。

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

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

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
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses-根據預設，限制為800個實例。 這項限制可以透過聯絡支援人員來增加。
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-根據預設，限制為800個實例。 這項限制可以透過聯絡支援人員來增加。

## <a name="microsoftrelay"></a>Microsoft.Relay

* 命名空間

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* 命名空間

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* 應用程式所需
* containerGroups
* gateways
* networks
* 密碼
* 磁碟區

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>後續步驟

如需配額和限制的完整清單，請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](azure-subscription-service-limits.md)。
