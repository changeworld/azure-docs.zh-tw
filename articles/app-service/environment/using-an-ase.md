---
title: 使用和管理 App Service 環境
description: 如何在 Azure App Service 環境中建立、發佈及調整應用程式。 在一份檔中尋找一般工作。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565628"
---
# <a name="use-an-app-service-environment"></a>使用 App Service Environment #

App Service 環境（ASE）是將 Azure App Service 部署到客戶的 Azure 虛擬網路中的子網。 ASE 包含：

- **前端**：前端是 HTTP/HTTPS 在 App Service 環境中結束的位置。
- **背景工作**：這些背景工作是裝載應用程式的資源。
- **資料庫**：資料庫會保留定義環境的資訊。
- **儲存體**：儲存體可用來裝載客戶發佈的應用程式。

您可以使用外部或內部 VIP 來部署 ASE，以進行應用程式存取。 使用外部 VIP 的部署常稱為外部 ASE。 內部版本稱為 ILB ASE，因為它是使用內部負載平衡器 (ILB)。 若要深入瞭解 ILB ASE，請參閱[建立和使用 ILB ase][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中建立應用程式 ##

若要在 ASE 中建立應用程式，可以使用和一般建立程序相同的程序，但有幾個小差異。 當您建立新的 App Service 方案（ASP）時：

- 您可以挑選 ASE 作為位置，而不用挑選部署應用程式的地理位置。
- 在 ASE 中建立的所有 App Service 計畫只能位於隔離的定價層。

如果您沒有 ASE，可以遵循[建立 App Service 環境][MakeExternalASE]中的指示建立一個。

若要在 ASE 中建立應用程式：

1. 選取 [建立資源] >  [Web + 行動] >  [Web 應用程式]。

2. 輸入應用程式的名稱。 如果您已在 ASE 中選取 App Service 方案，則應用程式的網域名稱會反映 ASE 的網域名稱。

    ![選取應用程式名稱][1]

1. 選取一個訂用帳戶。

1. 輸入新資源群組的名稱，或選取 [使用現有] 並從下拉式清單中挑選一個。

1. 選取您的作業系統。 

1. 選取 ASE 中現有的 App Service 方案，或透過下列步驟建立一個新的方案：

    a. 從 Azure 入口網站左側功能表中，選取 [**建立資源] [> Web 應用程式**]。

    b. 選取訂用帳戶。
    
    c. 選取或建立資源群組。
    
    d. 提供您的 web 應用程式名稱。
    
    e. 選取 [程式碼] 或 [DockerContainer]。
    
    f. 選取 [執行時間堆疊]。
    
    g. 選取 [Linux] 或 [Windows]。 
    
    h. 在 [**區域**] 下拉式清單中選取您的 ASE。 
    
    i. 選取或建立新的 App Service 方案。 如果建立新的 App Service 方案，請選取適當的**隔離**SKU 大小。
    
    ![隔離定價層][2]

    > [!NOTE]
    > Linux 應用程式和 Windows 應用程式不能在相同的 App Service 方案中，但可位於相同的 App Service 環境中。 
    >

2. 選取 [審核] [ **+ 建立**]，然後選取 [**建立**] （如果資訊正確的話）。

## <a name="how-scale-works"></a>調整方式 ##

每個 App Service 應用程式都會在 App Service 方案中執行。 App Service Environment 可保存 App Service 方案，而 App Service 方案可保存應用程式。 當您調整應用程式時，您可調整 App Service 方案，因而調整相同方案中的所有應用程式。

當您調整 App Service 計畫時，會自動新增所需的基礎結構。 新增基礎結構後，調整作業會有時間延遲。 如果您依序執行數個調整作業，則會對第一個基礎結構規模的要求採取動作，而其他的佇列則會啟動。 當第一次調整作業完成時，其他基礎結構要求會一起運作。 新增基礎結構時，會適當地指派 App Service 計畫。 建立新的 App Service 方案本身就是調整作業，因為它會要求額外的硬體。 

在多租使用者 App Service 中，調整是立即的，因為資源集區可供使用，以支援它。 ASE 中沒有這類緩衝區，並在需要時配置資源。

在 ASE 中，您可以將 App Service 方案調整為最多100個實例。 ASE 在 ASE 中的所有 App Service 方案中，最多可以有201個實例。 

## <a name="ip-addresses"></a>IP 位址 ##

App Service 能夠將專用的 IP 位址配置給應用程式。 設定以 IP 為基礎的 SSL 之後，就可以使用將專用 IP 配置給應用程式的功能，如將[現有的自訂 ssl 憑證系結至 Azure App Service][ConfigureSSL]中所述。 在 ILB ASE 中，您無法新增要用於以 IP 為主的 SSL 的其他 IP 位址。

使用外部 ASE 時，您可以用您在多租使用者 App Service 中執行的相同方式，為您的應用程式設定以 IP 為基礎的 SSL。 ASE 中一律會有一個備用位址，最多可有30個 IP 位址。 每次您使用一個位址時，就會新增另一個位址，如此一律會有立即可供使用的位址。 配置另一個 IP 位址時，需有時間延遲，以避免快速連續新增 IP 位址。

## <a name="front-end-scaling"></a>前端調整大小 ##

當您將 App Service 方案相應放大時，會自動新增背景工作來支援它們。 每個 ASE 都會以兩個前端建立。 前端會針對每個總計 15 App Service 方案實例，以一個前端的速率自動相應放大。 如果您有三個 App Service 計畫，每個都有五個實例，則總共會有15個實例和三個前端。 如果您將總計調整為30個實例，則您會有四個前端，依此類推。 

預設配置的前端數目適用于中等負載。 您可以將比例變更為每五個實例最少一個前端。 您也可以變更前端的大小。 根據預設，它們是單一核心。 您可以改為將入口網站中的前端大小變更為兩個或四個核心大小。 變更比率或前端大小會產生費用。 如需詳細資訊，請參閱[Azure App Service 定價][Pricing]。 如果您想要改善 ASE 的負載容量，您可以先擴充至兩個核心前端，再調整縮放比例，以獲得更好的改善。 變更前端的核心大小會導致您的 ASE 升級，而且應該在一般上班時間以外完成。

前端資源是 ASE 的 HTTP/HTTPS 端點。 使用預設的前端設定時，每個前端的記憶體使用量持續大約60%。 調整前端的主要原因是 CPU，主要是由 HTTPS 流量所驅動。

## <a name="app-access"></a>應用程式存取 ##

在外部 ASE 中，用於建立應用程式的網域尾碼為 *.&lt;asename&gt;. p.azurewebsites.net*。 如果您的 ASE 命名為_external-ASE_ ，而且您在該 ase 中裝載名為_contoso_的應用程式，您可以在下列 url 中找到它：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

如需如何建立外部 ASE 的詳細資訊，請參閱[建立 App Service 環境][MakeExternalASE]

在 ILB ASE 中，用於建立應用程式的網域尾碼為 *.&lt;asename&gt;. appserviceenvironment.net*。 如果您的 ASE 命名為_ilb-ase_ ，而且您在該 ase 中裝載名為_contoso_的應用程式，您可以在下列 url 中找到它：

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

如需有關如何建立 ILB ASE 的詳細資訊，請參閱[建立和使用 ILB ase][MakeILBASE]。 

Scm URL 是用來存取 Kudu 主控台，或使用 web deploy 來發佈您的應用程式。 如需 Kudu 主控台的詳細資訊，請參閱[適用于 Azure App Service 的 Kudu 主控台][Kudu]。 Kudu 主控台提供給您的 Web UI 可供偵錯、上傳檔案、編輯檔案及更多功能。

## <a name="publishing"></a>發佈 ##

如同多租用戶 App Service，在 ASE 中，您可以透過下列各項發佈：

- Web 部署。
- FTP。
- 持續整合。
- 在 Kudu 主控台中拖放。
- IDE，例如 Visual Studio、Eclipse 或 IntelliJ IDEA。

使用外部 ASE 時，這些發行選項的行為相同。 如需詳細資訊，請參閱[Azure App Service 中的部署][AppDeploy]。 

在發佈上的主要差異和 ILB ASE 有關。 使用 ILB ASE 時，所有發佈端點都只能透過 ILB 取得。 ILB 位於虛擬網路的 ASE 子網路中的私人 IP。 如果您沒有 ILB 的網路存取權，便無法在該 ASE 上發佈任何應用程式。 如[建立和使用 ILB ASE][MakeILBASE]中所述，您需要為系統中的應用程式設定 DNS。 其中包含 SCM 端點。 如果未正確定義它們，就無法發佈。 您的 IDE 也需要有 ILB 的網路存取權，以便直接發佈到它。

根據預設，以網際網路為基礎的 CI 系統（例如 GitHub 和 Azure DevOps）不會與 ILB ASE 搭配使用，因為發佈端點無法存取網際網路。 您可以藉由在包含 ILB ASE 的 VNet 中安裝自我裝載的發行代理程式，從 Azure DevOps 啟用發佈至 ILB ASE。 或者，您也可以使用使用提取模型的 CI 系統，例如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 您可以在應用程式的發行設定檔，以及應用程式的入口網站刀鋒視窗中看到 (在 [概觀] >  [基本資訊]，以及 [屬性] 中)。 

## <a name="storage"></a>儲存體

ASE 在 ASE 內的所有應用程式都有 1 TB 的儲存體。 根據預設，隔離的 SKU App Service 方案的限制為 250 GB。 如果您有五個以上的 App Service 方案，您必須小心不要超過 ASE 的 1 TB 限制。 如果您在一個 App Service 方案中需要超過 250 GB 的限制，請洽詢支援人員，將 App Service 方案限制調整為最多 1 TB。 調整計畫限制之後，ASE 中的所有 App Service 計畫仍然會有 1 TB 的限制。 

## <a name="logging"></a>記錄 ##

您可以將 ASE 與 Azure 監視器整合，以將 ASE 的相關記錄傳送至儲存體、事件中樞或 Log Analytics。 今天所記錄的專案如下：

| 實際 | 訊息 |
|---------|----------|
| ASE 狀況不良 | 因為虛擬網路設定無效，指定的 ASE 狀況不良。 如果狀況不良狀態繼續，ASE 將會暫停。 請確定已遵循此處定義的指導方針： https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 子網幾乎空間不足 | 指定的 ASE 位於幾乎空間不足的子網中。 剩餘的位址 {0}。 這些位址一旦用盡，ASE 就無法調整  |
| ASE 已接近總實例限制 | 指定的 ASE 已接近 ASE 的總實例限制。 它目前包含最多201個實例的 {0} App Service 計畫實例。 |
| ASE 無法到達相依性 | 指定的 ASE 無法觸達 {0}。  請確定已遵循此處定義的指導方針： https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 已暫停 | 指定的 ASE 已暫停。 ASE 暫止可能是因為帳戶 shortfall 或不正確虛擬網路設定所造成。 解決根本原因並繼續 ASE 以繼續服務流量 |
| ASE 升級已啟動 | 已開始對指定 ASE 進行平臺升級。 在調整作業中預期會延遲 |
| ASE 升級已完成 | 指定 ASE 的平臺升級已完成 |
| 調整規模作業已開始 | App Service 計畫（{0}）已開始調整。 預期狀態： {1} 我{2} 背景工作角色 
| 調整作業已完成 | App Service 計畫（{0}）已完成調整。 目前狀態： {1} 我{2} 背景工作 |
| 調整作業失敗 | App Service 計畫（{0}）無法進行調整。 目前狀態： {1} 我{2} 背景工作 |

若要在您的 ASE 上啟用記錄功能： 

1. 移至入口網站中的 [診斷設定]。  
1. 選取 [新增診斷設定]。
1. 提供記錄整合的名稱
1. 檢查並設定所需的記錄目的地。 
1. 檢查 AppServiceEnvironmentPlatformLogs

![ASE 診斷記錄設定][4]

如果您與 Log Analytics 整合，您可以從 ASE 入口網站選取記錄，並針對 AppServiceEnvironmentPlatformLogs 建立查詢，以查看記錄。 

## <a name="upgrade-preference"></a>升級喜好設定 ##

如果您有多個 Ase，您可能會想要讓某些 Ase 在其他人之前升級。 在 ASE HostingEnvironment Resource Manager 物件中，您可以設定 UpgradePreference 的值。 您可以透過範本、ARMClient 或 https://resources.azure.com來設定 upgradePreference 設定。  這三個值的選擇如下：

* 無-None 是預設值，表示 Azure 不會在特定批次中升級您的 ASE
* 早期就表示您的 ASE 將會在 App Service 升級的前半部升級
* 晚期延遲表示您的 ASE 將會在 App Service 升級的下半年升級

如果您使用 https://resources.azure.com，您可以藉由下列方式設定 upgradePreferences 值：

1. 前往 resources.azure.com 並使用您的 Azure 帳戶登入
1. 流覽訂用帳戶\/\[訂用帳戶名稱\]\/resourceGroups\/\[資源組名\]\/提供者\/Microsoft。 Web\/hostingEnvironments\/\[ASE 名稱\]
1. 選取頂端的 [讀取/寫入]
1. 選取 [編輯]
1. 將 [upgradePreference] 的值變更為所需的三個選項。
1. 選取修補程式

![資源 azure com 顯示][5]

當您有多個 Ase 時，upgradePreferences 功能其實是最合理的，因為「早期」升級的 Ase 會在您的「延遲」 Ase 之前升級。 當您有多個 Ase 時，您應該將 [開發/測試 Ase] 設定為 [早期]，並將您的生產 Ase 設定為 [晚期]。

## <a name="pricing"></a>價格 ##

稱為「**獨立**」的定價 SKU 僅供搭配 ASE 使用。 ASE 中裝載的所有 App Service 方案都位於隔離定價 SKU 中。 隔離的 App Service 方案費率會因區域而有所不同。 

除了 App Service 方案的價格，ASE 本身有固定費率。 您的 ASE 大小不會改變，而是針對每15個 App Service 計畫實例的一個額外前端，以預設的調整速率來支付 ASE 基礎結構的費用。  

如果每15個 App Service 計畫實例的一個前端的預設縮放速率不夠快，您可以調整新增前端的比率或前端的大小，。  當您調整比例或大小時，會針對預設不會新增的前端核心付費。  

例如，如果您將縮放比例調整為10，則會為您 App Service 計畫中的每10個實例新增一個前端。 一般費用涵蓋每15個實例一個前端的規模費率。 調整比例為10時，您需支付針對10個 App Service 方案實例新增的第三個前端費用。 在您達到 15 個執行個體時，不需要付費，因為它是自動新增的。

如果您將前端的大小調整為兩個核心，但不調整比例，則您需支付額外的核心費用。  ASE 建立時有兩個前端，因此，即使低於自動調整閾值，如果您將大小增加至兩個核心前端，則需要支付兩個額外核心的費用。

如需詳細資訊，請參閱[Azure App Service 定價][Pricing]。

## <a name="delete-an-ase"></a>刪除 ASE ##

若要刪除 ASE： 

1. 請使用 [App Service Environment] 刀鋒視窗頂端的 [刪除]。 

1. 輸入 ASE 的名稱以確認您想要將它刪除。 當您刪除 ASE 時，將同時刪除其中包含的所有內容。 

    ![ASE 刪除][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
