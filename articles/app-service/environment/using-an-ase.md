---
title: 使用和管理 App Service 環境
description: 瞭解如何在 App Service 環境中建立、發佈及調整應用程式。 尋找本文中的所有一般工作。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 3/26/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4565580feeddc2df8f6ed3011302016bb39977b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80586126"
---
# <a name="use-an-app-service-environment"></a>使用 App Service 環境

App Service 環境（ASE）是在客戶的 Azure 虛擬網路實例的子網中 Azure App Service 部署。 ASE 包含：

- **前端**： HTTP 或 HTTPS 在 App Service 環境中終止的位置
- 背景**工作**：裝載您應用程式的資源
- **資料庫**：保存定義環境的資訊
- **儲存體**：用來裝載客戶發佈的應用程式

您可以使用外部或內部虛擬 IP （VIP）來部署 ASE，以進行應用程式存取。 具有外部 VIP 的部署通常稱為*外部 ASE*。 具有內部 VIP 的部署稱為*ILB ASE* ，因為它使用內部負載平衡器（ILB）。 若要深入了解 ILB ASE，請參閱[建立和使用 ILB ASE][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中建立應用程式

若要在 ASE 中建立應用程式，您可以使用與一般建立應用程式相同的進程，但有幾個小差異。 當您建立新的 App Service 方案時：

- 您可以挑選 ASE 作為位置，而不用挑選部署應用程式的地理位置。
- 在 ASE 中建立的所有 App Service 計畫只能位於隔離的定價層。

如果您沒有 ASE，可以遵循[建立 App Service 環境][MakeExternalASE]中的指示建立一個。

若要在 ASE 中建立應用程式：

1. 選取 [**建立資源** > ] [**web +** > 行動] [**web 應用程式**]。

1. 輸入應用程式的名稱。 如果您已在 ASE 中選取 App Service 方案，則應用程式的功能變數名稱會反映 ASE 的功能變數名稱：

    ![選取應用程式名稱][1]

1. 選取一個訂用帳戶。

1. 輸入新資源群組的名稱，或選取 [使用現有]**** 並從下拉式清單中挑選一個。

1. 選取您的作業系統。

1. 選取 ASE 中現有的 App Service 方案，或透過下列步驟建立一個新的方案：

    a. 從 Azure 入口網站的左側功能表中，選取 [**建立資源] [> Web 應用程式**]。

    b. 選取訂用帳戶。

    c. 選取或建立資源群組。

    d. 輸入您的 web 應用程式名稱。

    e. 選取 [程式**代碼**] 或 [ **DockerContainer**]。

    f. 選取 [執行時間堆疊]。

    g. 選取 [Linux]**** 或 [Windows]****。 

    h. 在 [**區域**] 下拉式清單中選取您的 ASE。 

    i. 選取或建立新的 App Service 方案。 如果建立新的 App Service 方案，請選取適當的**隔離**SKU 大小。

    ![隔離定價層][2]

    > [!NOTE]
    > Linux 應用程式和 Windows 應用程式不能位於相同的 App Service 方案中，但可以是相同的 App Service 環境。
    >

1. 選取 [**審查 + 建立**]，確認資訊正確，然後選取 [**建立**]。

## <a name="how-scale-works"></a>調整方式

每個 App Service 應用程式都會在 App Service 方案中執行。 App Service Environment 可保存 App Service 方案，而 App Service 方案可保存應用程式。 當您調整應用程式時，您也可以調整 App Service 方案和相同方案中的所有應用程式。

當您調整 App Service 計畫時，會自動新增所需的基礎結構。 新增基礎結構時，調整作業會有時間延遲。 如果您依序執行數個調整作業，則會對第一個基礎結構調整要求採取動作，而其他則會排入佇列。 當第一次調整作業完成時，其他基礎結構要求會一起運作。 當新增基礎結構時，會適當地指派 App Service 計畫。 建立新的 App Service 方案本身是一種調整作業，因為它會要求額外的硬體。

在多租使用者 App Service 中，調整是立即的，因為資源集區可供使用，以支援它。 在 ASE 中，沒有這類緩衝區，而且資源會根據需求進行配置。

在 ASE 中，您可以將 App Service 方案調整為最多100個實例。 ASE 在該 ASE 的所有 App Service 計畫中，最多可以有201個實例。

## <a name="ip-addresses"></a>IP 位址

App Service 可以將專用的 IP 位址配置給應用程式。 當您設定以 IP 為基礎的 SSL 之後，就可以使用這項功能，如將[現有的自訂 TLS/SSL 憑證系結至 Azure App Service][ConfigureSSL]中所述。 在 ILB ASE 中，您無法新增更多 IP 位址以用於以 IP 為主的 SSL。

透過外部 ASE，您可以使用與多租使用者 App Service 中相同的方式，為您的應用程式設定以 IP 為主的 SSL。 ASE 中一律會有一個備用位址，最多30個 IP 位址。 每次使用一個時，都會加入另一個，讓位址隨時都可供使用。 需要時間延遲才能配置另一個 IP 位址。 該延遲會導致快速連續新增 IP 位址。

## <a name="front-end-scaling"></a>前端調整大小

當您將 App Service 方案相應放大時，會自動新增背景工作來支援它們。 每個 ASE 建立時都會包含兩個前端。 前端會針對每一組15個 App Service 方案實例，以一個前端的速率自動相應放大。 例如，如果您有三個 App Service 計畫，每個都有五個實例，則總共會有15個實例和三個前端。 如果您將總計調整為30個實例，則會有四個前端。 當您相應放大時，此模式會繼續進行。

預設配置的前端數目適用于中等負載。 您可以將比率降低為每五個實例最少一個前端。 您也可以變更前端的大小。 根據預設，它們是單一核心。 在 Azure 入口網站中，您可以改為將其大小變更為兩個或四個核心。

變更比率或前端大小會產生費用。 如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。 如果您想要改善 ASE 的負載容量，您可以先調整至雙核心前端，再調整縮放比例，以提高您的功能。 變更前端的核心大小會導致您的 ASE 升級，而且應該在正常上班時間以外完成。

前端資源是 ASE 的 HTTP/HTTPS 端點。 依照預設前端組態，每個前端的記憶體使用量始終大約為 60%。 調整前端的主要原因是 CPU 使用量，主要是由 HTTPS 流量所驅動。

## <a name="app-access"></a>應用程式存取

在外部 ASE 中，用於建立應用程式的網域尾碼是 *。&lt;asename&gt;. p.azurewebsites.net*。 如果您的 ASE 命名為_external-ASE_ ，而且您在該 ase 中裝載名為_contoso_的應用程式，您可以在下列 url 中找到它：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

如需有關如何建立外部 ASE 的詳細資訊，請參閱[建立 App Service 環境][MakeExternalASE]。

在 ILB ASE 中，用於建立應用程式的網域尾碼是 *。&lt;asename&gt;. appserviceenvironment.net*。 如果您的 ASE 命名為_ilb-ase_ ，而且您在該 ase 中裝載名為_contoso_的應用程式，您可以在下列 url 中找到它：

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

如需有關如何建立 ILB ASE 的詳細資訊，請參閱[建立和使用 ILB ase][MakeILBASE]。

SCM URL 是用來存取 Kudu 主控台，或使用 Web Deploy 來發佈您的應用程式。 如需 Kudu 主控台的詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 Kudu 主控台提供給您的 Web UI 可供偵錯、上傳檔案、編輯檔案及更多功能。

### <a name="dns-configuration"></a>DNS 組態 

當您使用外部 ASE 時，在 ASE 中建立的應用程式會向 Azure DNS 註冊。 透過 ILB ASE，您必須管理您自己的 DNS。 

若要使用您的 ILB ASE 設定 DNS：

    create a zone for <ASE name>.appserviceenvironment.net
    create an A record in that zone that points * to the ILB IP address
    create an A record in that zone that points @ to the ILB IP address
    create a zone in <ASE name>.appserviceenvironment.net named scm
    create an A record in the scm zone that points * to the ILB IP address

ASE 預設網域尾碼的 DNS 設定並不會將您的應用程式限制為僅供那些名稱存取。 在 ILB ASE 中，您可以在應用程式上設定自訂功能變數名稱，而不會進行任何驗證。 如果您想要建立名為*contoso.net*的區域，您可以這麼做，並將它指向 ILB IP 位址。 自訂功能變數名稱適用于應用程式要求，但不適用於 scm 網站。 Scm 網站僅適用于* &lt;appname&gt;. scm。&lt;asename&gt;. appserviceenvironment.net*。 

名為的*區域&lt; 。asename&gt;。 appserviceenvironment.net*是全域唯一的。 在5月2019日前，客戶可以指定 ILB ASE 的網域尾碼。 如果您想要使用*contoso.com*做為網域尾碼，您可以這麼做，這會包含 scm 網站。 該模型有挑戰，包括：管理預設 SSL 憑證、缺少與 scm 網站的單一登入，以及使用萬用字元憑證的需求。 ILB ASE 預設憑證升級程式也會造成干擾，並導致應用程式重新開機。 為了解決這些問題，ILB ASE 行為已變更為根據 ASE 名稱和 Microsoft 擁有的尾碼來使用網域尾碼。 ILB ASE 行為的變更只會影響5月2019之後所做的 ILB Ase。 預先存在的 ILB Ase 仍然必須管理 ASE 的預設憑證和其 DNS 設定。

## <a name="publishing"></a>發佈

在 ASE 中，如同多租使用者 App Service，您可以透過下列方法來發佈：

- Web 部署
- FTP
- 持續整合（CI）
- 在 Kudu 主控台中拖放
- IDE，例如 Visual Studio、Eclipse 或 IntelliJ 的想法

使用外部 ASE 時，這些發行選項全都以相同的方式執行。 如需詳細資訊，請參閱[在 Azure App Service 中部署][AppDeploy]。

使用 ILB ASE 時，只會透過 ILB 提供發佈端點。 ILB 位於虛擬網路的 ASE 子網路中的私人 IP。 如果您沒有 ILB 的網路存取權，就無法在該 ASE 上發佈任何應用程式。 如[建立和使用 ILB ASE][MakeILBASE]中所述，您必須為系統中的應用程式設定 DNS。 該需求包括 SCM 端點。 如果未正確定義端點，您就無法發行。 您的 Ide 也必須具有 ILB 的網路存取權，才能直接發行至該電腦。

如果沒有其他變更，以網際網路為基礎的 CI 系統（例如 GitHub 和 Azure DevOps）不會與 ILB ASE 搭配使用，因為發佈端點無法存取網際網路。 您可以藉由在包含 ILB ASE 的虛擬網路中安裝自我裝載的發行代理程式，從 Azure DevOps 啟用發佈至 ILB ASE。 或者，您也可以使用使用提取模型的 CI 系統，例如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 您可以在應用程式的發行設定檔和應用程式的入口網站窗格中看到它（在 [**總覽** > ] [**基本**] 和 [**屬性**] 中）。

## <a name="storage"></a>儲存體

ASE 針對 ASE 中的所有應用程式都有 1 TB 的儲存體。 根據預設，隔離定價 SKU 中的 App Service 方案有 250 GB 的限制。 如果您有五個以上的 App Service 方案，請小心不要超過 ASE 的 1 TB 限制。 如果您在一個 App Service 方案中需要超過 250 GB 的限制，請洽詢支援人員，將 App Service 方案限制調整為最多 1 TB。 調整計畫限制之後，ASE 中的所有 App Service 方案仍有 1 TB 的限制。

## <a name="logging"></a>記錄

您可以將 ASE 與 Azure 監視器整合，以將 ASE 的相關記錄傳送到 Azure 儲存體、Azure 事件中樞或 Log Analytics。 今天會記錄下列專案：

| 實際 | 訊息 |
|---------|----------|
| ASE 狀況不良 | 因為虛擬網路設定無效，指定的 ASE 狀況不良。 如果狀況不良狀態繼續，ASE 將會暫停。 請確定已遵循此處定義的指導https://docs.microsoft.com/azure/app-service/environment/network-info方針：。 |
| ASE 子網幾乎空間不足 | 指定的 ASE 位於幾乎空間不足的子網中。 有剩餘{0}的位址。 一旦這些位址耗盡之後，ASE 就無法調整。  |
| ASE 已接近總實例限制 | 指定的 ASE 已接近 ASE 的總實例限制。 它目前包含{0}最多201個實例的 App Service 計畫實例。 |
| ASE 無法到達相依性 | 無法連線到指定的 ASE {0}。  請確定已遵循此處定義的指導https://docs.microsoft.com/azure/app-service/environment/network-info方針：。 |
| ASE 已暫停 | 指定的 ASE 已暫停。 ASE 暫止可能是因為帳戶 shortfall 或不正確虛擬網路設定所造成。 解決根本原因並繼續 ASE 以繼續服務流量。 |
| ASE 升級已啟動 | 已開始對指定 ASE 進行平臺升級。 在調整作業中預期會延遲。 |
| ASE 升級已完成 | 指定 ASE 的平臺升級已完成。 |
| 調整規模作業已開始 | App Service 計畫（{0}）已開始調整。 預期狀態： {1}我{2}的工作者。
| 調整作業已完成 | App Service 計畫（{0}）已完成調整。 目前狀態： {1}我{2}的工作者。 |
| 調整作業失敗 | App Service 方案（{0}）無法調整。 目前狀態： {1}我{2}的工作者。 |

若要在您的 ASE 上啟用記錄功能：

1. 在入口網站中，移至 [**診斷設定**]。
1. 選取 [**新增診斷設定**]。
1. 提供記錄整合的名稱。
1. 選取並設定您想要的記錄目的地。
1. 選取 [ **AppServiceEnvironmentPlatformLogs**]。

![ASE 診斷記錄設定][4]

如果您與 Log Analytics 整合，您可以從 ASE 入口網站選取**記錄**，並針對**AppServiceEnvironmentPlatformLogs**建立查詢，以查看記錄。 只有當您的 ASE 具有會觸發它的事件時，才會發出記錄。 如果您的 ASE 沒有這類事件，就不會有任何記錄檔。 若要快速查看 Log Analytics 工作區中的記錄範例，請在 ASE 中使用其中一個 App Service 方案來執行調整作業。 接著，您可以針對**AppServiceEnvironmentPlatformLogs**執行查詢，以查看這些記錄。 

**建立警示**

若要針對您的記錄建立警示，請依照[使用 Azure 監視器建立、查看和記錄管理警示][logalerts]中的指示進行。 簡單地說︰

* 在您的 ASE 入口網站中開啟 [警示] 頁面
* 選取**新的警示規則**
* 選取您的資源做為您的 Log Analytics 工作區
* 使用自訂記錄搜尋來設定您的條件，以使用 "AppServiceEnvironmentPlatformLogs |" 之類的查詢其中 ResultDescription 包含「已開始調整」或您想要的任何內容。 適當地設定閾值。 
* 視需要新增或建立動作群組。 動作群組可讓您定義對警示的回應，例如傳送電子郵件或 SMS 訊息。
* 為您的警示命名並加以儲存。

## <a name="upgrade-preference"></a>升級喜好設定

如果您有多個 Ase，您可能會想要在其他人之前先升級某些 Ase。 在 ASE **HostingEnvironment Resource Manager**物件中，您可以設定**upgradePreference**的值。 您**upgradePreference**可以使用範本、ARMClient 或https://resources.azure.com來設定 upgradePreference 設定。 三種可能的值如下：

- **無**： Azure 不會在特定的批次中升級您的 ASE。 這是預設值。
- **早期**：您的 ASE 將會在 App Service 升級的前半部升級。
- **晚期**：您的 ASE 將會在 App Service 升級的下半年升級。

如果您使用https://resources.azure.com的是，請遵循下列步驟來設定**upgradePreferences**值：

1. 請移至 resources.azure.com，並使用您的 Azure 帳戶登入。
1. 前往\/\[訂用帳戶的資源訂用\]\/帳戶\/\[名稱 resourceGroups 資源\]\/組\/名提供者\/Microsoft\/\[. Web\]hostingEnvironments ASE 名稱。
1. 選取頂端的 [**讀取/寫入**]。
1. 選取 [編輯]  。
1. 將 [ **upgradePreference** ] 設定為您想要的三個值中的哪一個。
1. 選取 [**修補**]。

![資源 azure com 顯示][5]

當您有多個 Ase 時， **upgradePreferences**功能最有意義，因為您的「早期」 ase 會在您的「延遲」 ase 之前升級。 當您有多個 Ase 時，您應該將開發和測試 Ase 設定為「早期」，而您的生產 Ase 會是「延遲」。

## <a name="pricing"></a>定價

稱為「*獨立*」的定價 SKU 僅供搭配 ase 使用。 ASE 中裝載的所有 App Service 方案都位於隔離定價 SKU 中。 App Service 方案的隔離費率可能因地區而異。

除了您的 App Service 方案的價格之外，ASE 本身也有固定費率。 您的 ASE 大小不會變更非固定速率。 它會針對每15個 App Service 方案實例的一個額外前端，以預設的調整速率支付 ASE 基礎結構的費用。

如果每15個 App Service 計畫實例的一個前端的預設縮放速率不夠快，您可以調整新增前端的比率或前端的大小。 當您調整比率或大小時，需要為預設時未加入的前端核心付費。

例如，如果您將級別比率調整為 10，系統就會針對 App Service 方案中每 10 個執行個體新增一個前端。 固定費用涵蓋每隔 15 個執行個體 1 個前端的級別費率。 使用 10 級別比率時，您必須支付針對 10 個 App Service 方案執行個體新增的第三個前端費用。 在您達到 15 個執行個體時，不需要付費，因為它是自動新增的。

如果您將前端的大小調整為兩個核心，但不調整比例，則需支付額外的核心。 ASE 建立時有兩個前端，因此，即使低於自動調整閾值，如果您將大小增加至兩核心前端，則需要支付兩個額外核心的費用。

如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。

## <a name="delete-an-ase"></a>刪除 ASE

若要刪除 ASE：

1. 選取 [ **App Service 環境**] 窗格頂端的 [**刪除**]。

1. 輸入 ASE 的名稱以確認您想要將它刪除。 當您刪除 ASE 時，您也會刪除其中的所有內容。

    ![ASE 刪除][3]

1. 選取 [確定]  。

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
[logalerts]: ../../azure-monitor/platform/alerts-log.md
