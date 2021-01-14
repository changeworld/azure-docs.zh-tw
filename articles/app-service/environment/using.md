---
title: 使用 App Service 環境
description: 瞭解如何使用您的 App Service 環境來裝載隔離的應用程式。
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba06a0f9d520f445965329203aecd6c576666737
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209724"
---
# <a name="using-an-app-service-environment"></a>使用 App Service 環境

App Service 環境 (ASE) 是 Azure App Service 的單一租使用者部署，可直接插入您所選擇的 Azure 虛擬網路 (VNet) 。 它是僅供一個客戶使用的系統。 部署到 ASE 的應用程式受限於 ASE 子網所套用的網路功能。 您的應用程式不需要啟用任何額外的功能，因此必須遵守這些網路功能。 

## <a name="create-an-app-in-an-ase"></a>在 ASE 中建立應用程式

若要在 ASE 中建立應用程式，您可以使用一般建立應用程式時所使用的相同程式，但有幾個小差異。 當您建立新的 App Service 方案時：

- 您可以挑選 ASE 作為位置，而不用挑選部署應用程式的地理位置。
- 在 ASE 中建立的所有 App Service 計畫只能位於隔離的 v2 定價層。

如果您沒有 ASE，您可以遵循 [建立 App Service 環境][MakeASE]中的指示建立一個。

若要在 ASE 中建立應用程式：

1. 選取 [**建立資源**  >  **web + Mobile**  >  **Web 應用程式**]。

1. 選取一個訂用帳戶。

1. 輸入新資源群組的名稱，或選取 [使用現有] 並從下拉式清單中挑選一個。

1. 輸入應用程式的名稱。 如果您已在 ASE 中選取 App Service 方案，則應用程式的功能變數名稱會反映 ASE 的功能變數名稱：

    ![在 ASE 中建立應用程式][1]

1. 選取您的發行類型、堆疊和作業系統。

1.  選取 [區域]。 您必須在這裡選取既有的 App Service 環境 v3。  您無法在應用程式建立期間進行 ASEv3 

1. 在您的 ASE 中選取現有的 App Service 方案，或建立一個新方案。 如果要建立新的應用程式，請選取您要用於 App Service 方案的大小。 您可以為應用程式選取的唯一 SKU 是隔離的 v2 定價 SKU。

    ![隔離 v2 定價層][2]

    > [!NOTE]
    > Linux 應用程式和 Windows 應用程式不能位於相同的 App Service 方案中，但可位於相同的 App Service 環境。
    >

1. 選取 **[下一步：監視]**  如果您想要使用您的應用程式啟用 app Insights，您可以在建立流程期間執行此動作。 

1.  選取 **[下一步：標記]** 將任何您想要的標記新增至應用程式  

1. 選取 [ **審核 + 建立**]，確認資訊正確無誤，然後選取 [ **建立**]。

## <a name="how-scale-works"></a>調整方式

每個 App Service 應用程式都會在 App Service 方案中執行。 App Service Environment 可保存 App Service 方案，而 App Service 方案可保存應用程式。 當您調整應用程式時，也會調整該相同方案中的 App Service 方案和所有應用程式。

當您調整 App Service 方案時，會自動新增所需的基礎結構。 在新增基礎結構時調整作業的時間延遲。 當您調整 App Service 方案時，任何其他需要相同作業系統和大小的規模調整作業都會等到第一個調整作業完成為止。 在封鎖調整作業完成之後，所有已排入佇列的要求都會在同一時間進行處理。 一個大小和 OS 的調整規模作業不會封鎖調整其他大小和 OS 組合。 例如，如果您已調整 Windows I2v2 App Service 計畫，則在該 ASE 中調整 Windows I2v2 的任何其他要求都會排入佇列，直到完成為止。   

在多組織使用者共用 App Service 中，因為資源集區可立即提供支援，所以規模調整會立即進行。 ASE 中沒有這類緩衝區，而且資源會根據需求進行配置。

## <a name="app-access"></a>應用程式存取

在 ASE 中，用於建立應用程式的網域尾碼為 *。 &lt;asename &gt; . appserviceenvironment.net*。 如果您的 ASE 名為 _my-ase_ ，而您在該 ase 中裝載名為 _contoso_ 的應用程式，您可以在下列 url 中找到它：

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

如需有關如何建立 ASE 的詳細資訊，請參閱 [建立 App Service 環境][MakeASE]。

SCM URL 是用來存取 Kudu 主控台，或是使用 Web Deploy 來發佈您的應用程式。 如需 Kudu 主控台的詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 Kudu 主控台提供給您的 Web UI 可供偵錯、上傳檔案、編輯檔案及更多功能。

### <a name="dns-configuration"></a>DNS 組態 

ASE 會針對輸入流量使用私人端點。 它不會使用 Azure DNS 私人區域自動設定。 如果您想要使用自己的 DNS 伺服器，您需要新增下列記錄：

1. 為 &lt;ASE 名稱&gt;.appserviceenvironment.net 建立一個區域
1. 在該區域中建立 A 記錄，並將 * 指向 ASE 私人端點所使用的輸入 IP 位址
1. 在該區域中建立 A 記錄，以將 @ 指向 ASE 私人端點所使用的輸入 IP 位址
1. 在名為 scm 的 &lt;ASE 名稱&gt;.appserviceenvironment.net 中建立一個區域
1. 在 scm 區域中建立 A 記錄，並將 * 指向 ASE 私人端點所使用的 IP 位址

若要在 Azure DNS 私人區域中設定 DNS：

1. 建立名為 <ASE name>.appserviceenvironment.net 的 Azure DNS 私人區域
1. 在該區域中建立一個指向 ILB IP 位址的 A 記錄
1. 在該區域中建立一個將 @ 指向 ILB IP 位址的 A 記錄
1. 在該區域中建立一個將 *.scm 指向 ILB IP 位址的 A 記錄

ASE 預設網域尾碼的 DNS 設定，不會將您的應用程式限制為只能透過這些名稱存取。 您可以設定自訂功能變數名稱，而不需要對 ASE 中的應用程式進行任何驗證。 如果您想要建立名為 *contoso.net* 的區域，您可以這樣做，並將其指向輸入 IP 位址。 自訂網域名稱適用於應用程式要求，但不適用於 scm 網站。 Scm 網站僅適用于 *&lt; appname &gt; . scm &lt; 。asename &gt; . appserviceenvironment.net*。 

## <a name="publishing"></a>發佈

在 ASE 中，如同多租使用者 App Service，您可以透過下列方法來發佈：

- Web 部署
- 持續整合 (CI)
- 在 Kudu 主控台中拖放
- IDE，例如 Visual Studio、Eclipse 或 IntelliJ 構想

使用 ASE 時，只能透過私人端點所使用的輸入位址使用發佈端點。 如果您沒有私人端點位址的網路存取權，便無法在該 ASE 上發佈任何應用程式。  您的 Ide 也必須具有 ILB 的網路存取權，才能直接發佈至該電腦。

如果沒有其他變更，以網際網路為基礎的 CI 系統（例如 GitHub 和 Azure DevOps）將無法與 ILB ASE 搭配運作，因為發佈端點無法存取網際網路。 您可以在包含 ILB ASE 的虛擬網路中安裝自我裝載的發行代理程式，藉以啟用從 Azure DevOps 發佈至 ILB ASE。 

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
1. 選取 [新增診斷設定]。
1. 提供記錄整合的名稱。
1. 選取並設定您想要的記錄目的地。
1. 選取 [ **AppServiceEnvironmentPlatformLogs**]。

![ASE 診斷記錄設定][4]

如果您與 Log Analytics 整合，您可以從 ASE 入口網站選取 **記錄** ，並針對 **AppServiceEnvironmentPlatformLogs** 建立查詢，以查看記錄。 只有當您的 ASE 具有將觸發的事件時，才會發出記錄。 如果您的 ASE 沒有這類事件，就不會有任何記錄。 若要快速查看 Log Analytics 工作區中的記錄範例，請使用 ASE 中的其中一個 App Service 方案來執行調整作業。 然後，您可以針對 **AppServiceEnvironmentPlatformLogs** 執行查詢，以查看這些記錄。 

**建立警示**

若要針對您的記錄建立警示，請依照 [使用 Azure 監視器建立、查看和記錄管理警示](../../azure-monitor/platform/alerts-log.md)中的指示進行。 簡單地說︰

* 開啟 ASE 入口網站中的 [警示] 頁面
* 選取 **新的警示規則**
* 選取您的資源以作為 Log Analytics 工作區
* 使用自訂記錄搜尋設定您的條件，以使用像是 "AppServiceEnvironmentPlatformLogs |其中 ResultDescription 包含「已開始調整」或您想要的任何內容。 設定適當的閾值。 
* 視需要新增或建立動作群組。 動作群組可讓您定義警示的回應，例如傳送電子郵件或 SMS 訊息
* 為您的警示命名並加以儲存。

## <a name="internal-encryption"></a>內部加密

App Service 環境會以黑箱系統的方式運作，您看不到內部元件或系統內的通訊。 若要達到更高的輸送量，內部元件之間預設不會啟用加密。 系統是安全的，因為流量完全無法存取，而無法進行監視或存取。 如果您的合規性需求需要從端對端加密完整加密資料路徑，您可以 **在 ASE 設定** UI 中啟用此功能。

![啟用內部加密][5]

這會加密前端和背景工作角色之間 ASE 中的內部網路流量，加密分頁檔，同時加密背景工作角色磁碟。 啟用 InternalEncryption clusterSetting 之後，可能會對系統效能造成影響。 當您進行變更以啟用 InternalEncryption 時，您的 ASE 將會處於不穩定的狀態，直到變更完全傳播為止。 視您的 ASE 中有多少執行個體而定，變更的完整傳播可能需要幾小時才能完成。 強烈建議您不要在使用中的 ASE 上啟用此功能。 如果您需要在主動使用的 ASE 上啟用此功能，強烈建議您將流量轉向備份環境，直到作業完成為止。

## <a name="upgrade-preference"></a>升級喜好設定

如果您有多個 Ase，您可能會想要在其他 Ase 之前升級。 在 ASE **HostingEnvironment Resource Manager** 物件中，您可以設定 **upgradePreference** 的值。 您可以使用範本、ARMClient 或來設定 **upgradePreference** 設定 https://resources.azure.com 。 三個可能的值為：

- **無**： Azure 會在沒有特定批次的情況中升級您的 ASE。 此值為預設。
- **早期**：您的 ASE 將在 App Service 升級的前半部升級。
- **延遲**：您的 ASE 將在 App Service 升級的下半年升級。

若要設定您的升級喜好設定，請移 **至 ASE 設定** UI。 

當您有多個 Ase 時， **upgradePreferences** 功能會有最大的意義，因為您的「早期」 ase 將會在「晚期」 ase 之前升級。 當您有多個 Ase 時，您應該將您的開發和測試 Ase 設定為「早期」，而您的生產 Ase 將會「遲到」。

## <a name="delete-an-ase"></a>刪除 ASE

若要刪除 ASE：

1. 選取 [ **App Service 環境**] 窗格頂端的 [**刪除**]。

1. 輸入 ASE 的名稱以確認您想要將它刪除。 當您刪除 ASE 時，也會刪除其中的所有內容。

    ![ASE 刪除][3]

1. 選取 [確定]。

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
