---
title: 鎖定輸出流量
description: 瞭解如何與 Azure 防火牆整合，以保護從 App Service 環境內的輸出流量。
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f24a984a4b3e13039f1f9dcf0be459425c048c41
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565718"
---
# <a name="locking-down-an-app-service-environment"></a>鎖定 App Service 環境

App Service Environment (ASE) 有一些外部相依性，它需要能夠存取，才能正確運作。 ASE 位於客戶 Azure 虛擬網路 (VNet)。 客戶必須允許 ASE 相依性流量，這對於想要鎖定從其 VNet 所有輸出流量的客戶而言會是個問題。

有數個輸入端點可用來管理 ASE。 輸入的管理流量不能透過防火牆裝置傳送。 此流量的來源位址已知，且發佈在 [App Service Environment 管理位址](https://docs.microsoft.com/azure/app-service/environment/management-addresses)文件。 另外還有一個名為 AppServiceManagement 的服務標記，可以與網路安全性群組（Nsg）搭配使用，以保護輸入流量。

ASE 輸出相依性幾乎完全使用 FQDN 定義，它背後並沒有靜態位址。 缺少靜態位址表示「網路安全性群組」無法用來鎖定 ASE 的輸出流量。 地址經常變更，使得無法根據目前的解析度來設定規則，以用來建立 NSG。 

保護輸出位址的解決方案在於使用可以根據網域名稱控制輸出流量的防火牆裝置。 Azure 防火牆可以依據目的地的 FQDN 限制 HTTP 和 HTTPS 流量輸出。  

## <a name="system-architecture"></a>系統架構

透過防火牆裝置部署具有輸出流量的 ASE，需要變更 ASE 子網上的路由。 路由會在 IP 層級運作。 如果您不小心定義路由，可以將 TCP 回復流量強制從另一個位址到來源。 當您的回復位址與傳送至的位址流量不同時，此問題稱為「非對稱式路由」，而它將會中斷 TCP。

必須定義路由，才能讓 ASE 的輸入流量以流量傳入的相同方式回復。 您必須針對輸入管理要求和輸入應用程式要求來定義路由。

進出 ASE 的流量必須遵守下列慣例

* 使用防火牆裝置時，不支援 Azure SQL、儲存體和事件中樞的流量。 此流量必須直接傳送至這些服務。 進行這項操作的方法是設定這三個服務的服務端點。 
* 您必須定義路由表規則，以將輸入管理流量從其來源傳送回來。
* 必須定義路由表規則，將輸入應用程式流量從它的位置送回。 
* 所有其他保留 ASE 的流量，都可以使用路由表規則傳送到您的防火牆裝置。

![具有 Azure 防火牆連線流程的 ASE][5]

## <a name="locking-down-inbound-management-traffic"></a>鎖定輸入管理流量

如果您的 ASE 子網尚未指派 NSG，請建立一個。 在 NSG 內，設定第一個規則，以允許來自埠454，455上名為 AppServiceManagement 的服務標記流量。 允許從 AppServiceManagement 標記存取的規則，是公用 Ip 用來管理 ASE 的唯一必要條件。 該服務標籤後方的位址只會用來管理 Azure App Service。 流經這些連線的管理流量會經過加密，並使用驗證憑證來保護。 此通道上的一般流量包括客戶起始的命令和健康狀態探查等。 

透過入口網站使用新子網進行的 Ase，是以包含 AppServiceManagement 標籤允許規則的 NSG 來建立。  

您的 ASE 也必須允許埠16001上來自 Load Balancer 標記的輸入要求。 來自埠16001上 Load Balancer 的要求會在 Load Balancer 與 ASE 前端之間保持運作檢查。 如果埠16001遭到封鎖，ASE 將會變成狀況不良。

## <a name="configuring-azure-firewall-with-your-ase"></a>使用 ASE 設定 Azure 防火牆 

使用 Azure 防火牆來鎖定現有 ASE 輸出流量的步驟如下：

1. 在 ASE 子網路上為 SQL、儲存體和事件中樞啟用服務端點。 若要啟用服務端點，請移至網路入口網站 > 子網，然後從 [服務端點] 下拉式清單中選取 [Microsoft]、[microsoft] 和 [Microsoft]。 當您為 Azure SQL 啟用服務端點時，任何具有 Azure SQL 相依性的應用程式也必須設定服務端點。 

   ![選取服務端點][2]
  
1. 在存在 ASE 的 VNet 中建立名為 AzureFirewallSubnet 的子網路。 請遵照 [Azure 防火牆文件](https://docs.microsoft.com/azure/firewall/)的指示，來建立您的 Azure 防火牆。

1. 從 [Azure 防火牆 UI > 規則 > 應用程式規則集合] 中，選取 [新增應用程式規則集合]。 提供名稱、優先順序，並設定為 [允許]。 在 [FQDN 標記] 區段中，提供名稱、將來源位址設定為 *，然後選取 [App Service 環境 FQDN 標記] 和 [Windows Update]。 
   
   ![加入應用程式規則][1]
   
1. 從 [Azure 防火牆 UI > 規則 > 網路規則集合] 中，選取 [新增網路規則集合]。 提供名稱、優先順序，並設定為 [允許]。 在 [IP 位址] 底下的 [規則] 區段中，提供名稱、選取 [**任何**]、[設定 * 至來源] 和 [目的地位址] 的 ptocol，然後將埠設定為123。 此規則可讓系統使用 NTP 執行時鐘同步。 以與連接埠 12000 相同的方式建立另一個規則，以協助分類任何系統問題。 

   ![加入 NTP 網路規則][3]
   
1. 從 [Azure 防火牆 UI > 規則 > 網路規則集合] 中，選取 [新增網路規則集合]。 提供名稱、優先順序，並設定為 [允許]。 在 [服務標籤] 底下的 [規則] 區段中，提供名稱、選取 [**任何**] 的通訊協定、將 * 設定為 [來源位址]、選取 AzureMonitor 的服務標籤，然後將埠設定為80，443。 此規則可讓系統提供具有健康情況和計量資訊的 Azure 監視器。

   ![新增 NTP 服務標記網路規則][6]
   
1. 建立路由表，其中具有來自 [App Service Environment 管理位址]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)的管理位址，以及網際網路的下一個躍點。 需要路由表項目，才能避免發生非對稱式路由問題。 針對底下＜IP 位址相依性＞所註明的 IP 位址相依性，新增具有網際網路下一個躍點的路由。 在路由表中新增 0.0.0.0/0 的虛擬設備路由，並且以 Azure 防火牆私人IP 位址作為下一個躍點。 

   ![建立路由表][4]
   
1. 將您建立的路由表指派給 ASE 子網路。

#### <a name="deploying-your-ase-behind-a-firewall"></a>在防火牆後面部署 ASE

將 ASE 部署在防火牆後面的步驟與使用 Azure 防火牆設定現有 ASE 的步驟相同，除非您需要建立 ASE 子網路，則依照先前的步驟。 若要在預先存在的子網中建立 ASE，您需要使用 Resource Manager 範本，如[使用 Resource Manager 範本建立 ASE](https://docs.microsoft.com/azure/app-service/environment/create-from-template) 中的文件所述。

## <a name="application-traffic"></a>應用程式流量 

上述步驟可讓您的 ASE 正常運作。 您仍然需要設定以容納應用程式需求。 針對已設定 Azure 防火牆的 ASE，其中的應用程式有兩個問題。  

- 應用程式相依性必須新增至 Azure 防火牆或路由表。 
- 必須為應用程式流量建立路由，以避免非對稱式路由問題

如果您的應用程式有相依性，它們必須新增至您的 Azure 防火牆。 建立應用程式規則來允許 HTTP/HTTPS 流量，以及建立網路規則來允許其他一切流量。 

如果您知道應用程式要求流量來源的位址範圍，可以將它新增到指派給您 ASE 子網路的路由表。 如果位址範圍很大或未指定，則可以使用網路設備，例如應用程式閘道，提供您一個位址以便新增至路由表。 如需設定應用程式閘道與 ILB ASE 的詳細資訊，請參閱[整合 ILB ASE 與應用程式閘道](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

此應用程式閘道的使用方式只是如何設定系統的範例。 如果您確實遵循此路徑，則您需要將路由新增至 ASE 子網路路由表，以便傳送至應用程式閘道的回覆流量會直接傳送至該處。 

## <a name="logging"></a>記錄 

Azure 防火牆可以將記錄傳送至 Azure 儲存體、事件中樞或 Azure 監視器記錄。 若要將您的應用程式與任何支援的目的地整合，請移至 [Azure 防火牆入口網站 &gt; 診斷記錄]，並啟用所需目的地的記錄。 如果您與 Azure 監視器記錄整合，則可以看到傳送至 Azure 防火牆的任何流量記錄。 若要查看被拒絕的流量，請開啟 [Log Analytics 工作區入口網站] &gt; [記錄]，然後輸入如下查詢 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
當您不知道所有應用程式相依性時，第一次讓應用程式運作時，將 Azure 防火牆與 Azure 監視器記錄整合會很有用。 您可以從[Azure 監視器中的分析記錄資料](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)，深入瞭解 Azure 監視器記錄。
 
## <a name="dependencies"></a>相依性

當您想要設定 Azure 防 火牆以外的防火牆設備時，才需要下列資訊。 

- 應使用服務端點來設定支援的服務端點服務。
- 適用於非 HTTP/S 流量的 IP 地址相依性 (TCP 與 UDP 流量)
- FQDN HTTP/HTTPS 端點可以放在您的防火牆裝置。
- 萬用字元 HTTP/HTTPS 端點是根據一些限定條件，可能隨著 ASE 而變的相依性。 
- 只有在您要將 Linux 應用程式部署到 ASE 時，才需要考量 Linux 相依性。 如果您沒有要將 Linux 應用程式部署到 ASE，則這些位址不需要新增至您的防火牆。 

#### <a name="service-endpoint-capable-dependencies"></a>服務端點功能相依性 

| 端點 |
|----------|
| Azure SQL |
| Azure 儲存體 |
| Azure 事件中樞 |

#### <a name="ip-address-dependencies"></a>IP 位址相依性

| 端點 | 詳細資料 |
|----------| ----- |
| \*:123 | NTP 時鐘檢查。 在連接埠 123 上的多個端點檢查流量 |
| \*:12000 | 此連接埠用於某些系統監視。 如果遭到封鎖，則某些問題將難以分級，但您的 ASE 會繼續運作 |
| 40.77.24.27：80 | 需要監視 ASE 問題併發出警示 |
| 40.77.24.27:443 | 需要監視 ASE 問題併發出警示 |
| 13.90.249.229：80 | 需要監視 ASE 問題併發出警示 |
| 13.90.249.229:443 | 需要監視 ASE 問題併發出警示 |
| 104.45.230.69：80 | 需要監視 ASE 問題併發出警示 |
| 104.45.230.69:443 | 需要監視 ASE 問題併發出警示 |
| 13.82.184.151：80 | 需要監視 ASE 問題併發出警示 |
| 13.82.184.151:443 | 需要監視 ASE 問題併發出警示 |

使用 Azure 防火牆，您可以自動獲得以下使用 FQDN 標記設定的所有內容。 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性 

| 端點 |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>萬用字元 HTTP/HTTPS 相依性 

| 端點 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*。 identity.azure.net:443 |

#### <a name="linux-dependencies"></a>Linux 相依項目 

| 端點 |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |
| \*。 cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov 相依性

如需 US Gov 區域中的 Ase，請依照本檔的[使用 ase 設定 Azure 防火牆](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase)一節中的指示，使用您的 ase 來設定 azure 防火牆。

如果您想要在 US Gov 中使用 Azure 防火牆以外的裝置 

* 應使用服務端點來設定支援的服務端點服務。
* FQDN HTTP/HTTPS 端點可以放在您的防火牆裝置。
* 萬用字元 HTTP/HTTPS 端點是根據一些限定條件，可能隨著 ASE 而變的相依性。

Linux 在 US Gov 區域中無法使用，因此不會列為選擇性設定。

#### <a name="service-endpoint-capable-dependencies"></a>服務端點功能相依性 ####

| 端點 |
|----------|
| Azure SQL |
| Azure 儲存體 |
| Azure 事件中樞 |

#### <a name="ip-address-dependencies"></a>IP 位址相依性

| 端點 | 詳細資料 |
|----------| ----- |
| \*:123 | NTP 時鐘檢查。 在連接埠 123 上的多個端點檢查流量 |
| \*:12000 | 此連接埠用於某些系統監視。 如果遭到封鎖，則某些問題將難以分級，但您的 ASE 會繼續運作 |
| 40.77.24.27：80 | 需要監視 ASE 問題併發出警示 |
| 40.77.24.27:443 | 需要監視 ASE 問題併發出警示 |
| 13.90.249.229：80 | 需要監視 ASE 問題併發出警示 |
| 13.90.249.229:443 | 需要監視 ASE 問題併發出警示 |
| 104.45.230.69：80 | 需要監視 ASE 問題併發出警示 |
| 104.45.230.69:443 | 需要監視 ASE 問題併發出警示 |
| 13.82.184.151：80 | 需要監視 ASE 問題併發出警示 |
| 13.82.184.151:443 | 需要監視 ASE 問題併發出警示 |

#### <a name="dependencies"></a>相依性 ####

| 端點 |
|----------|
| \*。 ctldl.windowsupdate.com:80 |
| \*。 management.usgovcloudapi.net:80 |
| \*。 update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp. digicert 0 |
|ocsp.msocsp.co|
|ocsp verisign 0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*。 management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
