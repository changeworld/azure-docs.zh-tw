---
title: App Service 環境的可用性區域支援
description: 瞭解如何部署您的 App Service 環境，讓您的應用程式具有區域冗余。
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1e88aac4209f7960b2589cf43f59ead4bd129134
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605068"
---
# <a name="availability-zone-support-for-app-service-environments"></a>App Service 環境的可用性區域支援

App Service (ASE) 的環境可部署到可用性區域 (AZ) 。  客戶可以將內部負載平衡器 (ILB) Ase 部署到 Azure 區域內的特定 AZ。 如果您將 ILB ASE 釘選到特定 AZ，ILB ASE 所使用的資源將會釘選到指定的 AZ，或以區域重複的方式部署。  

因為 ILB ASE 已釘選到特定區域，所以會將明確部署到 AZ 的 ILB ASE 視為區域性資源。 下列 ILB ASE 相依性將會釘選到指定的區域：

- ASE 的內部負載平衡器 IP 位址
- ASE 用來管理和執行 web 應用程式的計算資源

部署在區域 ILB ASE 上之 web 應用程式的遠端檔案存放區會使用區域冗余儲存體 (ZRS) 。

除非遵循這篇文章中所述的步驟，否則不會以區域性的方式自動部署 ILB Ase。 您無法將具有公用 IP 位址的外部 ASE 釘選到特定的可用性區域。 

您可以在下列任何區域中建立區域性 ILB Ase：

- 澳大利亞東部
- 巴西南部
- 加拿大中部
- 美國中部
- 美國東部
- 美國東部 2
- 美國東部 2 (EUAP) 
- 法國中部 
- 德國中西部
- 日本東部
- 北歐
- 西歐
- 東南亞
- 英國南部
- 美國西部 2

部署在區域性 ILB ASE 上的應用程式將會繼續執行，並在該 ASE 上提供流量，即使在相同區域中的其他區域發生中斷的情況也一樣。  有可能是非執行時間行為，包括：應用程式服務方案調整、應用程式建立、應用程式設定和應用程式發佈可能仍會受到其他可用性區域中斷的影響。 區域性 ILB ASE 的區域固定部署只會確保已部署應用程式的持續運作時間。

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>如何在可用性區域中部署 App Service 環境 ##

您必須使用 ARM 範本建立區域性 ILB Ase。 透過 ARM 範本建立區域性 ILB ASE 之後，您可以透過 Azure 入口網站和 CLI 來查看和互動。  只有在第一次建立區域性 ILB ASE 時，才需要 ARM 範本。

ARM 範本中唯一需要用來指定區域性 ILB ASE 的變更是 [新增 ***區域*** ] 屬性。 [ ***區域*** ] 屬性應設定為 "1"、"2" 或 "3" 的值，取決於應將 ILB ASE 釘選到的邏輯可用性區域。

下方的範例 ARM 範本會顯示新的 [ ***區域*** ] 屬性，指定 ILB ASE 應釘選到區域2。

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

若要讓您的應用程式區域重複，您需要部署兩個區域性 ILB Ase。 這兩個區域 ILB Ase 必須位於不同的可用性區域中。 然後，您必須將應用程式部署到每個 ILB Ase。 建立應用程式之後，您必須設定負載平衡解決方案。 建議的解決方案是部署區域 ILB Ase 的 [區域多餘應用程式閘道](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) 上游。 

## <a name="in-region-data-residency"></a>在區域資料存放區中 ##

在可用性區域中部署的 ILB Ase 只會將客戶資料儲存在已部署區域性 ILB ASE 的區域內。 網站檔案內容以及客戶提供的設定和秘密儲存在 App Service 中，會保留在部署區域性 ILB ASE 的區域內。

客戶可依照先前「如何在可用性區域中部署 App Service 環境」一節中所述的步驟，確保單一區域資料落地。 藉由根據這些步驟設定 App Service 環境，在可用性區域中部署的 App Service 環境會滿足區域資料落地需求，包括在 [Azure 信任中心](https://azuredatacentermap.azurewebsites.net/)中指定的需求。

客戶可以依照下列步驟，驗證是否已正確設定 App Service 環境，以將資料儲存在單一區域中： 

1. 使用 [資源總管](https://resources.azure.com)，流覽至 APP SERVICE 環境的 ARM 資源。  Ase 列在 [ *提供者/Microsoft. Web/hostingEnvironments*] 之下。
2. 如果 ARM JSON 語法的觀點中有 *區域* 屬性存在，而且它包含單一值的 json 陣列，其值為 "1"、"2" 或 "3"，則會 ZONALLY 部署 ASE，而客戶資料會保留在相同的區域中。
2. 如果 *區域* 屬性不存在，或屬性沒有如先前指定的有效區域值，則不會 ZONALLY 部署 ASE，而且客戶資料不會以獨佔方式儲存在相同的區域中。
