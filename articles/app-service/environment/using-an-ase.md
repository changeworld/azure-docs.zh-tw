---
title: 使用和管理 App Service 環境
description: 瞭解如何在 App Service 環境中建立、發佈及調整應用程式。 尋找本文中的所有一般工作。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5e3cb07730aafed7d1c339f543e7fb09fe956cab
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961902"
---
# <a name="use-an-app-service-environment"></a>使用 App Service 環境

App Service 環境 (ASE) 是 Azure App Service 部署到客戶 Azure 虛擬網路實例中子網的部署。 ASE 包含：

- **前端**： HTTP 或 HTTPS 在 App Service 環境中終止的位置
- 背景**工作角色**：裝載您應用程式的資源
- **資料庫**：保留定義環境的資訊
- **儲存體**：用來裝載客戶發佈的應用程式

您可以使用 (VIP) 的外部或內部虛擬 IP 來部署 ASE，以進行應用程式存取。 具有外部 VIP 的部署通常稱為 *外部 ASE*。 具有內部 VIP 的部署稱為 *ILB ASE* ，因為它會使用內部負載平衡器 (ILB) 。 若要深入了解 ILB ASE，請參閱[建立和使用 ILB ASE][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中建立應用程式

若要在 ASE 中建立應用程式，您可以使用一般建立應用程式時所使用的相同程式，但有幾個小差異。 當您建立新的 App Service 方案時：

- 您可以挑選 ASE 作為位置，而不用挑選部署應用程式的地理位置。
- 在 ASE 中建立的所有 App Service 計畫只能位於隔離的定價層。

如果您沒有 ASE，您可以遵循 [建立 App Service 環境][MakeExternalASE]中的指示建立一個。

若要在 ASE 中建立應用程式：

1. 選取 [**建立資源**  >  **web + Mobile**  >  **Web 應用程式**]。

1. 輸入應用程式的名稱。 如果您已在 ASE 中選取 App Service 方案，則應用程式的功能變數名稱會反映 ASE 的功能變數名稱：

    ![選取應用程式名稱][1]

1. 選取一個訂用帳戶。

1. 輸入新資源群組的名稱，或選取 [使用現有]**** 並從下拉式清單中挑選一個。

1. 選取您的作業系統。

1. 選取 ASE 中現有的 App Service 方案，或透過下列步驟建立一個新的方案：

    a. 從 Azure 入口網站左側功能表中，選取 [ **建立資源] > Web 應用程式**。

    b. 選取訂用帳戶。

    c. 選取或建立資源群組。

    d. 輸入您的 web 應用程式名稱。

    e. 選取 [程式 **代碼** ] 或 [ **DockerContainer**]。

    f. 選取執行時間堆疊。

    g. 選取 [Linux] 或 [Windows]。 

    h. 在 [ **區域** ] 下拉式清單中選取您的 ASE。 

    i. 選取或建立新的 App Service 方案。 如果要建立新的 App Service 方案，請選取適當的 **隔離** SKU 大小。

    ![隔離定價層][2]

    > [!NOTE]
    > Linux 應用程式和 Windows 應用程式不能位於相同的 App Service 方案中，但可位於相同的 App Service 環境。
    >

1. 選取 [ **審核 + 建立**]，確認資訊正確無誤，然後選取 [ **建立**]。

## <a name="how-scale-works"></a>調整方式

每個 App Service 應用程式都會在 App Service 方案中執行。 App Service Environment 可保存 App Service 方案，而 App Service 方案可保存應用程式。 當您調整應用程式時，也會調整該相同方案中的 App Service 方案和所有應用程式。

當您調整 App Service 方案時，會自動新增所需的基礎結構。 在新增基礎結構時調整作業的時間延遲。 如果您依序進行數個調整作業，則會對第一個基礎結構規模要求進行處理，而其他則會排入佇列。 當第一次調整作業完成時，其他基礎結構要求都會一起運作。 新增基礎結構之後，就會適當地指派 App Service 計畫。 建立新的 App Service 方案本身就是調整規模作業，因為它會要求額外的硬體。

在多組織使用者共用 App Service 中，因為資源集區可立即提供支援，所以規模調整會立即進行。 ASE 中沒有這類緩衝區，而且資源會根據需求進行配置。

在 ASE 中，您可以將 App Service 方案調整為最多100個實例。 ASE 最多可在該 ASE 中的所有 App Service 方案中擁有201個實例的總數。

## <a name="ip-addresses"></a>IP 位址

App Service 可以將私人 IP 位址配置給應用程式。 當您設定以 IP 為基礎的 SSL 之後，就可以使用這項功能，如將 [現有的自訂 TLS/SSL 憑證系結至 Azure App Service][ConfigureSSL]所述。 在 ILB ASE 中，您無法新增更多 IP 位址以用於以 IP 為基礎的 SSL。

您可以使用外部 ASE，以與多租使用者 App Service 中的相同方式，為您的應用程式設定以 IP 為基礎的 SSL。 ASE 中一律會有一個備用位址，最多30個 IP 位址。 每次使用時，系統會新增另一個位址，以便隨時都能使用位址。 配置另一個 IP 位址需要時間延遲。 延遲會導致快速連續新增 IP 位址。

## <a name="front-end-scaling"></a>前端調整大小

當您相應放大 App Service 方案時，會自動新增背景工作來支援它們。 每個 ASE 建立時都會包含兩個前端。 前端會針對每一組 15 App Service 方案實例，以一個前端的速率自動相應放大。 例如，如果您有三個 App Service 計畫，每個都有五個實例，則總共會有15個實例和三個前端。 如果您將規模調整為總計30個實例，則會有四個前端。 當您相應放大時，此模式會繼續進行。

預設配置的前端數目適用于中等負載。 您可以將比例降低為每五個實例最少一個前端。 您也可以變更前端的大小。 依預設，它們是單一核心。 在 Azure 入口網站中，您可以改為將其大小變更為兩個或四個核心。

變更比率或前端大小需要付費。 如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。 如果您想要改善 ASE 的負載容量，您可以先調整為雙核心前端，然後再調整調整比例，以獲得更多改進。 變更前端的核心大小將會導致您的 ASE 升級，並應在正常上班時間以外執行。

前端資源是 ASE 的 HTTP/HTTPS 端點。 依照預設前端組態，每個前端的記憶體使用量始終大約為 60%。 調整前端的主要原因是 CPU 使用率，這主要是由 HTTPS 流量所驅動。

## <a name="app-access"></a>應用程式存取

在外部 ASE 中，用於建立應用程式的網域尾碼為 *。 &lt;asename &gt; . p.azurewebsites.net*。 如果您的 ASE 名為 _external-ASE_ ，而您在該 ase 中裝載名為 _contoso_ 的應用程式，您可以在下列 url 中找到它：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

如需有關如何建立外部 ASE 的詳細資訊，請參閱 [建立 App Service 環境][MakeExternalASE]。

在 ILB ASE 中，用於建立應用程式的網域尾碼為 *。 &lt;asename &gt; . appserviceenvironment.net*。 如果您的 ASE 名為 _ilb-ASE_ ，而您在該 ase 中裝載名為 _contoso_ 的應用程式，您可以在下列 url 中找到它：

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

如需有關如何建立 ILB ASE 的詳細資訊，請參閱 [建立和使用 ILB ase][MakeILBASE]。

SCM URL 是用來存取 Kudu 主控台，或是使用 Web Deploy 來發佈您的應用程式。 如需 Kudu 主控台的詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 Kudu 主控台提供給您的 Web UI 可供偵錯、上傳檔案、編輯檔案及更多功能。

### <a name="dns-configuration"></a>DNS 組態 

當您使用外部 ASE 時，您的 ASE 中所建立的應用程式會向 Azure DNS 註冊。 外部 ASE 中沒有任何額外的步驟，可供您的應用程式公開使用。 使用 ILB ASE 時，您必須管理您自己的 DNS。 您可以在自己的 DNS 伺服器或 Azure DNS 私人區域中進行這項作業。

若要在您自己的 DNS 伺服器中使用 ILB ASE 設定 DNS：

1. 為 &lt;ASE 名稱&gt;.appserviceenvironment.net 建立一個區域
1. 在該區域中建立一個指向 ILB IP 位址的 A 記錄
1. 在該區域中建立一個將 @ 指向 ILB IP 位址的 A 記錄
1. 在名為 scm 的 &lt;ASE 名稱&gt;.appserviceenvironment.net 中建立一個區域
1. 在 scm 區域中建立一個將 * 指向 ILB IP 位址的 A 記錄

若要在 Azure DNS 私人區域中設定 DNS：

1. 建立名為 ASE 名稱的 Azure DNS 私人區域 &lt; &gt; 。 appserviceenvironment.net
1. 在該區域中建立一個指向 ILB IP 位址的 A 記錄
1. 在該區域中建立一個將 @ 指向 ILB IP 位址的 A 記錄
1. 在該區域中建立 A 記錄，並將 *. scm 指向 ILB 的 IP 位址

ASE 預設網域尾碼的 DNS 設定，不會將您的應用程式限制為只能透過這些名稱存取。 您可以設定自訂功能變數名稱，而不需要在 ILB ASE 中的應用程式進行任何驗證。 如果您想要建立名為 *contoso.net*的區域，您可以這樣做，並指向 ILB IP 位址。 自訂功能變數名稱適用于應用程式要求，但不適用於 scm 網站。 Scm 網站僅適用于* &lt; appname &gt; . scm &lt; 。asename &gt; . appserviceenvironment.net*。 

名為的區域 *。 &lt;asename &gt; 。 appserviceenvironment.net* 是全域唯一的。 在2019月之前，客戶可以指定 ILB ASE 的網域尾碼。 如果您想要使用 *contoso.com* 作為網域尾碼，您可以這麼做，其中包括 scm 網站。 該模型有一些挑戰，包括：管理預設 SSL 憑證、缺少與 scm 網站的單一登入，以及使用萬用字元憑證的需求。 ILB ASE 預設憑證升級程式也會造成干擾，並導致應用程式重新開機。 為了解決這些問題，ILB ASE 行為已變更為根據 ASE 名稱和 Microsoft 擁有的尾碼來使用網域尾碼。 ILB ASE 行為的變更只會影響2019月之後所做的 ILB Ase。 預先存在的 ILB Ase 仍然必須管理 ASE 的預設憑證及其 DNS 設定。

## <a name="publishing"></a>發佈

在 ASE 中，如同多租使用者 App Service，您可以透過下列方法來發佈：

- Web 部署
- FTP
-  (CI) 的持續整合
- 在 Kudu 主控台中拖放
- IDE，例如 Visual Studio、Eclipse 或 IntelliJ 構想

使用外部 ASE 時，這些發佈選項的運作方式都相同。 如需詳細資訊，請參閱[在 Azure App Service 中部署][AppDeploy]。

使用 ILB ASE 時，只能透過 ILB 使用發行端點。 ILB 位於虛擬網路的 ASE 子網路中的私人 IP。 如果您沒有 ILB 的網路存取權，就無法在該 ASE 上發佈任何應用程式。 如 [建立和使用 ILB ASE][MakeILBASE]所述，您必須為系統中的應用程式設定 DNS。 該需求包含 SCM 端點。 如果端點未正確定義，您就無法發行。 您的 Ide 也必須具有 ILB 的網路存取權，才能直接發佈至該電腦。

如果沒有其他變更，以網際網路為基礎的 CI 系統（例如 GitHub 和 Azure DevOps）將無法與 ILB ASE 搭配運作，因為發佈端點無法存取網際網路。 您可以在包含 ILB ASE 的虛擬網路中安裝自我裝載的發行代理程式，藉以啟用從 Azure DevOps 發佈至 ILB ASE。 或者，您也可以使用使用提取模型的 CI 系統，例如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 您可以在應用程式的發行設定檔和應用程式的入口網站窗格中看到它， (在**總覽**  >  **Essentials**和 [**屬性**]) 中。

## <a name="storage"></a>儲存體

ASE 具有 1 TB 的儲存體，適用于 ASE 中的所有應用程式。 隔離定價 SKU 中的 App Service 方案有 250 GB 的限制。 在 ASE 中，每 App Service 方案都會新增 250 GB 的儲存體，最多可達 1 TB 的限制。 您可以有更多的 App Service 計畫，而不只是四個，但已新增超過 1 TB 限制的儲存體。

## <a name="logging"></a>記錄

您可以將 ASE 與 Azure 監視器整合，以將 ASE 相關記錄傳送到 Azure 儲存體、Azure 事件中樞或 Log Analytics。 現在會記錄這些專案：

| 情況 | 訊息 |
|---------|----------|
| ASE 狀況不良 | 因為虛擬網路設定無效，所以指定的 ASE 狀況不良。 如果狀況不良狀態繼續，ASE 將會暫止。 請確定下列定義的指導方針如下： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 子網即將用盡空間 | 指定的 ASE 位於接近空間的子網中。 還有 {0} 其他位址。 一旦這些位址用盡，ASE 將無法調整。  |
| ASE 已接近實例限制的總計 | 指定的 ASE 已接近 ASE 的總實例限制。 它目前包含 {0} 最大201實例的 App Service 方案實例。 |
| ASE 無法連接相依性 | 指定的 ASE 無法連接 {0} 。  請確定下列定義的指導方針如下： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 已暫止 | 指定的 ASE 已暫止。 ASE 暫止可能是因為帳戶 shortfall 或不正確虛擬網路設定所致。 解決根本原因，並繼續 ASE 以繼續提供流量。 |
| ASE 升級已開始 | 已開始將平臺升級至指定的 ASE。 需要調整規模作業的延遲。 |
| ASE 升級已完成 | 升級至指定 ASE 的平臺已完成。 |
| 調整規模作業已開始 | App Service 計畫 ({0}) 已開始調整。 預期狀態： {1} 我 {2} 的工作者。
| 調整作業已完成 | App Service 計畫 ({0}) 完成調整。 目前狀態： {1} 我 {2} 的工作者。 |
| 調整規模作業失敗 |  () 的 App Service 方案 {0} 無法調整。 目前狀態： {1} 我 {2} 的工作者。 |

若要在您的 ASE 上啟用記錄：

1. 在入口網站中，移至 [ **診斷設定**]。
1. 選取 [ **新增診斷設定**]。
1. 提供記錄整合的名稱。
1. 選取並設定您想要的記錄目的地。
1. 選取 [ **AppServiceEnvironmentPlatformLogs**]。

![ASE 診斷記錄設定][4]

如果您與 Log Analytics 整合，您可以從 ASE 入口網站選取 **記錄** ，並針對 **AppServiceEnvironmentPlatformLogs**建立查詢，以查看記錄。 只有當您的 ASE 具有將觸發的事件時，才會發出記錄。 如果您的 ASE 沒有這類事件，就不會有任何記錄。 若要快速查看 Log Analytics 工作區中的記錄範例，請使用 ASE 中的其中一個 App Service 方案來執行調整作業。 然後，您可以針對 **AppServiceEnvironmentPlatformLogs** 執行查詢，以查看這些記錄。 

**建立警示**

若要針對您的記錄建立警示，請依照 [使用 Azure 監視器建立、查看和記錄管理警示][logalerts]中的指示進行。 簡單地說︰

* 開啟 ASE 入口網站中的 [警示] 頁面
* 選取 **新的警示規則**
* 選取您的資源以作為 Log Analytics 工作區
* 使用自訂記錄搜尋設定您的條件，以使用像是 "AppServiceEnvironmentPlatformLogs |其中 ResultDescription 包含「已開始調整」或您想要的任何內容。 設定適當的閾值。 
* 視需要新增或建立動作群組。 動作群組可讓您定義警示的回應，例如傳送電子郵件或 SMS 訊息
* 為您的警示命名並加以儲存。

## <a name="upgrade-preference"></a>升級喜好設定

如果您有多個 Ase，您可能會想要在其他 Ase 之前升級。 在 ASE **HostingEnvironment Resource Manager** 物件中，您可以設定 **upgradePreference**的值。 您可以使用範本、ARMClient 或來設定 **upgradePreference** 設定 https://resources.azure.com 。 三個可能的值為：

- **無**： Azure 會在沒有特定批次的情況中升級您的 ASE。 此值為預設。
- **早期**：您的 ASE 將在 App Service 升級的前半部升級。
- **延遲**：您的 ASE 將在 App Service 升級的下半年升級。

如果您使用的是 https://resources.azure.com ，請依照下列步驟設定 **upgradePreferences** 值：

1. 移至 resources.azure.com，並使用您的 Azure 帳戶登入。
1. 將資源移至訂用帳戶訂用帳戶 \/ \[ 名稱 \] \/ resourceGroups \/ \[ 資源組名 \] \/ 提供者 \/ \/ hostingEnvironments \/ \[ ASE 名稱 \] 。
1. 選取頂端的 [ **讀取/寫入** ]。
1. 選取 [編輯]  。
1. 將 **upgradePreference** 設定為您想要的三個值中的哪一個。
1. 選取 [ **修補**]。

![azure com 顯示的資源][5]

當您有多個 Ase 時， **upgradePreferences** 功能會有最大的意義，因為您的「早期」 ase 將會在「晚期」 ase 之前升級。 當您有多個 Ase 時，您應該將您的開發和測試 Ase 設定為「早期」，而您的生產 Ase 將會「遲到」。

## <a name="pricing"></a>定價

稱為「 *隔離* 」的定價 SKU 僅供與 ase 搭配使用。 ASE 中裝載的所有 App Service 方案都是在隔離的定價 SKU 中。 App Service 方案的隔離費率可能會因地區而異。

除了 App Service 方案的價格，ASE 本身也有固定的費率。 不會隨著 ASE 的大小變更非固定費率。 它會針對每15個 App Service 方案實例，以一個額外前端的預設規模費率支付 ASE 基礎結構的費用。

如果每15個 App Service 方案實例的一個前端預設比例費率不夠快，您可以調整新增前端的比率或前端的大小。 當您調整比率或大小時，需要為預設時未加入的前端核心付費。

例如，如果您將級別比率調整為 10，系統就會針對 App Service 方案中每 10 個執行個體新增一個前端。 固定費用涵蓋每隔 15 個執行個體 1 個前端的級別費率。 使用 10 級別比率時，您必須支付針對 10 個 App Service 方案執行個體新增的第三個前端費用。 在您達到 15 個執行個體時，不需要付費，因為它是自動新增的。

如果您將前端的大小調整為兩個核心，但未調整比例，則需支付額外的核心費用。 ASE 會使用兩個前端來建立，因此即使在自動調整閾值的正下方，如果您將大小增加到兩核心前端，也需要支付兩個額外的核心。

如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。

## <a name="delete-an-ase"></a>刪除 ASE

若要刪除 ASE：

1. 選取 [ **App Service 環境**] 窗格頂端的 [**刪除**]。

1. 輸入 ASE 的名稱以確認您想要將它刪除。 當您刪除 ASE 時，也會刪除其中的所有內容。

    ![ASE 刪除][3]

1. 選取 [確定]  。

## <a name="ase-cli"></a>ASE CLI

有命令列功能可管理 ASE。  Az cli 命令如下所示。

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



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
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md