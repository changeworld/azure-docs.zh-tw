---
title: 異地分散調整
description: 了解如何透過流量管理員和 App Service 環境，使用異地分散功能水平調整應用程式。
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85833599"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>App Service 環境的異地分散調整
## <a name="overview"></a>概觀

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

需要極高規模的應用程式案例可能會超過應用程式單一部署的可用計算資源容量。  例如，投票應用程式、體育活動及電視娛樂活動，都屬於需要極高延展性的案例。 水準相應放大應用程式可符合高擴充需求。 若要處理極高的負載需求，可以在單一區域和跨區域內進行許多應用程式部署。

App Service 環境是水準相應放大的理想平臺。在選取可支援已知要求速率的 App Service 環境設定之後，開發人員可以在「cookie 刀具」中部署額外的 App Service 環境，以達到所需的尖峰負載容量。

例如，假設在 App Service 環境設定上執行的應用程式已經過測試，可處理每秒 (RPS) 的20K 要求。  如果所需的尖峰負載容量為 100K RPS，則可以建立並設定五個 (5) App Service 環境，以確保應用程式可以處理預估的最大負載。

由於客戶通常會使用自訂 (或虛名) 網域存取應用程式，因此開發人員必須要有將應用程式要求分散到所有 App Service 環境執行個體的方法。  達成此目標的最佳方式是使用 [Azure 流量管理員設定檔][AzureTrafficManagerProfile]來解析自訂網域。  流量管理員設定檔可設定為指向所有個別的 App Service 環境。  流量管理員會根據流量管理員設定檔中的負載平衡設定，自動處理所有 App Service 環境的分配客戶。  無論所有的 App Service 環境是部署在單一 Azure 區域，還是跨多個 Azure 區域部署於世界各地，此方法都可運作。

此外，由於客戶是透過虛名網域存取應用程式的，因此無從得知執行應用程式的 App Service 環境數目。  因此，開發人員可以根據觀察到的流量負載，快速且輕鬆地新增和移除 App Service 環境。

下方的概念圖表說明在單一區域內以水平方式相應放大至三個 App Service 環境的應用程式。

![概念性架構][ConceptualArchitecture] 

本主題的其餘部分將逐步解說使用多個 App Service 環境為範例應用程式設定分散式拓撲所需的步驟。

## <a name="planning-the-topology"></a>規劃拓樸
在建置分散式應用程式的使用量之前，最好先備妥幾項資訊。

* **應用程式的自訂網域：** 客戶將用來存取應用程式的自訂網域名稱為何？  針對範例應用程式，自訂功能變數名稱為 `www.scalableasedemo.com` 。
* **流量管理員網域：** 建立 [Azure 流量管理員設定檔][AzureTrafficManagerProfile]時，請選擇功能變數名稱。  此名稱會與 *trafficmanager.net* 尾碼結合，以註冊流量管理員所管理的網域項目。  就範例應用程式而言，選擇的名稱是 *scalable-ase-demo*。  因此，流量管理員所管理的完整網域名稱是 *scalable-ase-demo.trafficmanager.net*。
* **調整應用程式使用量的策略：** 應用程式使用量是否會分散到單一區域中的多個 App Service 環境？  多個區域嗎？  兩種方法混合搭配使用嗎？  根據客戶流量的預期位置，以及應用程式的其他支援後端基礎結構的可調整程度來決定。  例如，在100% 無狀態應用程式中，應用程式可以使用每個 Azure 區域中許多 App Service 環境的組合進行大幅調整，並乘以跨多個 Azure 區域部署 App Service 的環境。  由於有 15 個以上的全球 Azure 區域可供選擇，客戶將可真正建置全球性超高延展性的應用程式使用量。  針對本文所使用的範例應用程式，已在單一 Azure 區域中建立三個 App Service 環境 (美國中南部) 。
* **App Service 環境的命名慣例：** 每個「App Service 環境」都需要一個唯一的名稱。  有兩個或更多 App Service 環境時，命名慣例將有助於識別每個 App Service 環境。  針對範例應用程式，使用了簡單的命名慣例。  三個 App Service 環境的名稱分別是 fe1ase、fe2ase 和 fe3ase。
* **應用程式的命名慣例：** 由於將會部署多個應用程式執行個體，因此每個部署的應用程式執行個體都需要一個名稱。  App Service 環境的一個簡單但簡單的功能，就是可以跨多個 App Service 環境使用相同的應用程式名稱。  由於每個 App Service 環境都有唯一的網域尾碼，開發人員可以選擇在每個環境中重複使用相同的應用程式名稱。  例如，開發人員可能會有名為的應用程式，如下所示：  *myapp.foo1.p.azurewebsites.net*、 *myapp.foo2.p.azurewebsites.net*、 *myapp.foo3.p.azurewebsites.net*等等。 不過，針對範例應用程式，每個應用程式實例也都有唯一的名稱。  所使用的應用程式執行個體名稱是 webfrontend1、webfrontend2 和 webfrontend3。

## <a name="setting-up-the-traffic-manager-profile"></a>設定流量管理員設定檔
將應用程式的多個執行個體部署在多個 App Service 環境上之後，可以使用流量管理員來註冊個別的應用程式執行個體。  針對範例應用程式， *scalable-ase-demo.trafficmanager.net* 需要流量管理員設定檔，才能將客戶路由傳送至下列任何已部署的應用程式實例：

* **webfrontend1.fe1ase.p.azurewebsites.net：** 部署在第一個「App Service 環境」上的範例應用程式執行個體。
* **webfrontend2.fe2ase.p.azurewebsites.net：** 部署在第二個「App Service 環境」上的範例應用程式執行個體。
* **webfrontend3.fe3ase.p.azurewebsites.net：** 部署在第三個「App Service 環境」上的範例應用程式執行個體。

若要註冊多個 Azure App Service 端點（全都在 **相同** 的 Azure 區域中執行），最簡單的方式就是使用 PowerShell [Azure Resource Manager 流量管理員支援][ARMTrafficManager]。  

第一個步驟是建立 Azure 流量管理員設定檔。  下列程式碼說明如何為範例應用程式建立設定檔：

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

請留意 RelativeDnsName 參數如何設為 scalable-ase-demo。  此參數會建立功能變數名稱 *scalable-ase-demo.trafficmanager.net* ，並與流量管理員設定檔產生關聯。

*TrafficRoutingMethod*參數會定義負載平衡原則流量管理員將用來判斷如何將客戶負載分散到所有可用的端點上。  在此範例中，已選擇 *加權* 方法。  基於此選擇，客戶要求會根據與每個端點相關聯的相對權數，散佈到所有已註冊的應用程式端點。 

在建立設定檔後，每個應用程式執行個體都會新增至設定檔做為原生 Azure 端點。  下列程式碼會提取每個前端 web 應用程式的參考。 然後，它會透過 *TargetResourceId* 參數，將每個應用程式新增為流量管理員端點。

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

請留意，對於每個應用程式執行個體分別會有一個 *Add-AzureTrafficManagerEndpointConfig* 呼叫。  每個 PowerShell 命令中的 *TargetResourceId* 參數都會參考三個已部署應用程式實例的其中一個。  流量管理員設定檔會將負載分散在設定檔中所註冊的所有三個端點上。

三個端點都會使用相同的值 (10) 做為 *Weight* 參數。  這種情況會導致流量管理員在這三個應用程式實例間平均分配客戶要求。 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>將應用程式的自訂網域指向流量管理員網域
最後一個必要步驟是將應用程式的自訂網域指向流量管理員網域。  針對範例應用程式，請 `www.scalableasedemo.com` 指向 `scalable-ase-demo.trafficmanager.net` 。  使用管理自訂網域的網域註冊機構完成此步驟。  

若使用註冊機構的網域管理工具，必須建立一個將自訂網域指向流量管理員網域的 CNAME 記錄。  下圖顯示此 CNAME 組態的範例：

![自訂網域的 CNAME][CNAMEforCustomDomain] 

雖然本主題並未說明，但請記住，每個應用程式執行個體也都需要註冊其自訂網域。  否則，如果要求將它帶到應用程式實例，而應用程式未向應用程式註冊自訂網域，則要求將會失敗。

在此範例中，自訂網域為 `www.scalableasedemo.com` ，而且每個應用程式實例都有與其相關聯的自訂網域。

![自訂網域][CustomDomain] 

如需向 Azure App Service apps 註冊自訂網域的回顧，請參閱 [註冊自訂網域][RegisterCustomDomain]。

## <a name="trying-out-the-distributed-topology"></a>嘗試使用分散式拓撲
使用流量管理員與 DNS 設定的最終結果，是 `www.scalableasedemo.com` 的要求會歷經下列順序的流程：

1. 瀏覽器或裝置會針對 `www.scalableasedemo.com` 進行 DNS 查閱
2. 網域註冊機構上的 CNAME 項目使 DNS 查閱重新導向至 Azure 流量管理員。
3. 對其中一個 Azure 流量管理員 DNS 伺服器執行 *scalable-ase-demo.trafficmanager.net* 的 DNS 查閱。
4. 根據稍早在 *TrafficRoutingMethod* 參數中指定的負載平衡原則，流量管理員會選取其中一個已設定的端點。 然後，它會將該端點的 FQDN 傳回至瀏覽器或裝置。
5. 因為端點的 FQDN 是在 App Service 環境上執行的應用程式實例的 Url，所以瀏覽器或裝置會要求 Microsoft Azure DNS 伺服器將 FQDN 解析為 IP 位址。 
6. 瀏覽器或裝置會將 HTTP/S 要求傳送至此 IP 位址。  
7. 要求會送達在其中一個 App Service 環境上執行的應用程式執行個體之一。

以下的主控台圖片顯示範例應用程式自訂網域的 DNS 查閱。 它會成功解析為在下列三個範例 App Service 環境中執行的應用程式實例 (在此案例中，這是三個 App Service 環境中的第二個) ：

![DNS 查閱][DNSLookup] 

## <a name="additional-links-and-information"></a>其他連結和資訊
PowerShell [Azure Resource Manager 流量管理員支援][ARMTrafficManager]的相關檔。  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
