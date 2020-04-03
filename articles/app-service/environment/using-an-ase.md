---
title: 使用與管理應用程式服務環境
description: 瞭解如何在應用服務環境中創建、發佈和縮放應用。 在本文中查找所有常見任務。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 3/26/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4565580feeddc2df8f6ed3011302016bb39977b4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586126"
---
# <a name="use-an-app-service-environment"></a>使用 App Service 環境

應用服務環境 (ASE) 是將 Azure 應用服務部署到客戶 Azure 虛擬網路實例中的子網。 ASE 包括:

- **前端**介面 :HTTP 或 HTTPS 在應用程式服務環境中終止的位置
- **輔助功能**:託管應用的資源
- **資料庫**:儲存定義環境的資訊
- **儲存**:用於託管客戶的應用

您可以使用外部或內部虛擬 IP (VIP) 部署 ASE 以進行應用存取。 具有外部 VIP 的部署通常稱為外部*ASE。* 具有內部 VIP 的部署稱為*ILB ASE,* 因為它使用內部負載均衡器 (ILB)。 若要深入了解 ILB ASE，請參閱[建立和使用 ILB ASE][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中建立應用程式

要在 ASE 中創建應用,請使用與通常創建應用時相同的過程,但存在一些小差異。 當您建立新的 App Service 方案時：

- 您可以挑選 ASE 作為位置，而不用挑選部署應用程式的地理位置。
- 在 ASE 中創建的所有應用服務計畫只能位於隔離定價層中。

如果沒有 ASE,則可以按照["創建應用服務環境][MakeExternalASE]"中的說明創建 ASE。

若要在 ASE 中建立應用程式：

1. 選擇 **「創建資源** > **Web + 行動** > **Web 應用**」 。

1. 輸入應用程式的名稱。 如果您在 ASE 中選擇了應用服務計畫,則應用的網域設定 ASE 的網域名稱:

    ![選取應用程式名稱][1]

1. 選取一個訂用帳戶。

1. 輸入新資源群組的名稱，或選取 [使用現有]**** 並從下拉式清單中挑選一個。

1. 選取您的作業系統。

1. 選取 ASE 中現有的 App Service 方案，或透過下列步驟建立一個新的方案：

    a. 從 Azure 門戶左側選單中,選擇「**建立資源# A0 Web 應用**。

    b. 選取訂用帳戶。

    c. 選擇或創建資源群組。

    d. 輸入 Web 應用的名稱。

    e. 選擇**代碼**或**Docker 容器**。

    f. 選擇運行時堆疊。

    g. 選取 [Linux]**** 或 [Windows]****。 

    h. 在 **「區域**」下拉清單中選擇 ASE。 

    i. 選擇或創建新的應用服務計劃。 如果創建新的應用服務計劃,請選擇相應的**獨立**SKU 大小。

    ![隔離定價層][2]

    > [!NOTE]
    > Linux 應用和 Windows 應用不能位於相同的應用服務計劃中,但它們可以位於相同的應用服務環境中。
    >

1. 選擇 **"審閱" = 創建**,請確保資訊正確,然後選擇"**創建**"。

## <a name="how-scale-works"></a>調整方式

每個 App Service 應用程式都會在 App Service 方案中執行。 App Service Environment 可保存 App Service 方案，而 App Service 方案可保存應用程式。 縮放應用時,還可以縮放應用服務計劃和同一計劃中的所有應用。

縮放應用服務計畫時,將自動添加所需的基礎結構。 在添加基礎結構時,擴展操作的時間延遲。 如果按順序執行多個縮放操作,則對第一個基礎結構規模請求執行操作,而其他基礎結構規模請求將排隊。 當第一個規模操作完成時,其他基礎結構請求都一起操作。 添加基礎結構時,將根據需要分配應用服務計劃。 創建新的應用服務計劃本身就是一個規模操作,因為它請求其他硬體。

在多租戶應用服務中,縮放是即時的,因為資源池隨時可用於支援它。 在 ASE 中,沒有此類緩衝區,並且資源會根據需要分配。

在 ASE 中,可以縮放最多 100 個實例的應用服務計劃。 ASE 可以在整個 ASE 中的所有應用服務計畫中擁有最多 201 個實例。

## <a name="ip-addresses"></a>IP 位址

應用服務可以將專用 IP 位址分配給應用。 在配置基於 IP 的 SSL 後,此功能可用,如[將現有自訂 TLS/SSL 憑證綁定到 Azure 應用服務][ConfigureSSL]中所述。 在 ILB ASE 中,無法增加更多 IP 位址以用於基於 IP 的 SSL。

使用外部 ASE,您可以像在多租戶應用服務中一樣為應用配置基於 IP 的 SSL。 ASE 中始終有一個備用位址,最多 30 個 IP 位址。 每次使用另一個位址時,都會添加另一個位址,以便地址始終隨時可用。 分配另一個 IP 位址需要時間延遲。 這種延遲可防止快速連續添加 IP 位址。

## <a name="front-end-scaling"></a>前端調整大小

橫向擴展應用服務計劃時,將自動添加工作人員以支援這些計劃。 每個 ASE 建立時都會包含兩個前端。 對於每組 15 個應用服務計畫實例,前端以一個前端的速率自動橫向擴展。 例如,如果您有三個應用服務計劃,每個計劃有 5 個實例,則總共有 15 個實例和 3 個前端。 如果擴展到總共 30 個實例,則有四個前端。 當您橫向擴展時,此模式將繼續。

默認情況下分配的前端數適用於中等負載。 您可以將比率降低為每五個實例一個前端。 您還可以更改前端的大小。 默認情況下,它們是單核。 在 Azure 門戶中,可以將其大小更改為兩個或四個內核。

更改比率或前端尺寸需要付費。 如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。 如果要提高 ASE 的負載容量,在調整比例之前,首先縮放到雙核前端,從而獲得更多的改進。 更改前端的核心尺寸將導致 ASE 的升級,並且應在正常工作時間之外完成。

前端資源是 ASE 的 HTTP/HTTPS 端點。 依照預設前端組態，每個前端的記憶體使用量始終大約為 60%。 擴展前端的主要原因是 CPU 使用率,這主要是由 HTTPS 流量驅動的。

## <a name="app-access"></a>應用程式存取

在外部 ASE 中,用於建立應用的網域的字串為*&lt;。asename&gt;.p.azurewebsites.net*。 如果您的 ASE 被命名為_外部 ase,_ 並且您託管了該 ASE 中名為_contoso_的應用,則可以透過以下網址 存取它:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

有關如何建立外部 ASE 的資訊,請參閱[建立應用服務環境][MakeExternalASE]。

在 ILB ASE 中,用於建立應用的網域的字串為 *。&lt;asename&gt;.appserviceenvironment.net*. . 如果您的 ASE 名為_ilb-ase,_ 並且您託管了該 ASE 中名為_contoso_的應用,則透過以下網址 存取它:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

有關如何建立 ILB ASE 的資訊,請參閱[建立及使用 ILB ASE][MakeILBASE]。

SCM URL 用於造訪 Kudu 主控台或使用 Web 部署發布應用。 如需 Kudu 主控台的詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 Kudu 主控台提供給您的 Web UI 可供偵錯、上傳檔案、編輯檔案及更多功能。

### <a name="dns-configuration"></a>DNS 組態 

使用外部 ASE 時,在 ASE 中創建的應用將註冊到 Azure DNS。 使用 ILB ASE,您必須管理自己的 DNS。 

要使用 ILB ASE 設定 DNS,請進行以下處理:

    create a zone for <ASE name>.appserviceenvironment.net
    create an A record in that zone that points * to the ILB IP address
    create an A record in that zone that points @ to the ILB IP address
    create a zone in <ASE name>.appserviceenvironment.net named scm
    create an A record in the scm zone that points * to the ILB IP address

ASE 預設網後綴的 DNS 設定不會將應用限制為僅由這些名稱訪問。 您可以在 ILB ASE 中的應用上設置自定義功能變數名稱,而無需進行任何驗證。 如果然後想要創建名為*contoso.net*的區域,則可以這樣做並將其指向 ILB IP 位址。 自定義功能變數名稱適用於應用請求,但不適用於 scm 網站。 scm 網站只*&lt;在 應用程式&gt;名稱 .scm&lt;上可用 。asename&gt;.appserviceenvironment.net*. . 

名為*的區域。&lt;asename&gt;.appserviceenvironment.net*是全球獨一無二的。 在 2019 年 5 月之前,客戶能夠指定 ILB ASE 的域後綴。 如果要對後後綴使用 *.contoso.com,* 則可以這樣做,這將包括 scm 網站。 這種模式存在挑戰,包括:管理預設 SSL 憑證、缺少 scm 網站的單一登錄以及使用通配符證書的要求。 ILB ASE 默認證書升級過程也具有破壞性,並導致應用程式重新啟動。 為了解決這些問題,ILB ASE 行為被更改為使用基於 ASE 的名稱和 Microsoft 擁有的後綴的域後綴。 對 ILB ASE 行為的更改僅影響 2019 年 5 月之後做出的 ILB ASE。 預先存在的 ILB ASE 仍必須管理 ASE 的預設證書及其 DNS 配置。

## <a name="publishing"></a>發佈

在 ASE 中,與多租戶應用服務一樣,可以通過以下方法發佈:

- Web 部署
- FTP
- 持續整合 (CI)
- 在 Kudu 主控台中拖放
- IDE,如視覺工作室、Eclipse 或 IntelliJ IDEA

使用外部 ASE 時,這些發表選項的工作方式都相同。 如需詳細資訊，請參閱[在 Azure App Service 中部署][AppDeploy]。

使用 ILB ASE,發佈終結點只能通過 ILB 提供。 ILB 位於虛擬網路的 ASE 子網路中的私人 IP。 如果您沒有對 ILB 的網路存取許可權,則無法在此 ASE 上發表任何應用。 如[創建和使用 ILB ASE][MakeILBASE]中所述,您必須為系統中的應用配置 DNS。 該要求包括 SCM 終結點。 如果終結點定義不正確,則無法發佈。 您的 IDA 還必須具有對 ILB 的網路存取許可權才能直接發布到 ILB。

如果沒有其他更改,基於 Internet 的 CI 系統(如 GitHub 和 Azure DevOps)將不能與 ILB ASE 配合使用,因為發布終結點無法存取 Internet。 通過在包含 ILB ASE 的虛擬網路中安裝自託管發布代理,可以從 Azure DevOps 啟用發佈到 ILB ASE。 或者,您也可以使用使用拉取模型的 CI 系統,如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 您可以在應用的發佈配置檔和應用的門戶窗格中(在**概述** > **基本值**和**屬性**中)中看到它。

## <a name="storage"></a>儲存體

ASE 對於 ASE 中的所有應用都有 1 TB 的儲存空間。 默認情況下,隔離定價 SKU 中的應用服務計劃限制為 250 GB。 如果您有五個或更多應用服務計劃,請注意不要超過 ASE 的 1-TB 限制。 如果您在一個應用服務計劃中需要超過 250 GB 的限制,請聯絡支援人員將應用服務計劃限制調整為最多 1 TB。 調整計劃限制后,ASE 中的所有應用服務計劃仍有 1 TB 的限制。

## <a name="logging"></a>記錄

您可以將 ASE 與 Azure 監視器整合,以便將有關 ASE 的日誌發送到 Azure 儲存、Azure 事件中心或日誌分析。 這些專案今天記錄:

| 情況 | 訊息 |
|---------|----------|
| ASE 不正常 | 由於虛擬網路配置無效,指定的 ASE 不正常。 如果不正常狀態繼續,ASE 將掛起。 確保遵循此處定義的準則: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 子網幾乎空間不足 | 指定的 ASE 位於幾乎空間不足的子網中。 {0}其餘位址。 一旦這些位址用盡,ASE將無法縮放。  |
| ASE 正在接近總實例限制 | 指定的 ASE 正在接近 ASE 的總實例限制。 它當前包含{0}最多 201 個實例的應用服務計劃實例。 |
| ASE 無法存取相依項 | 指定的 ASE{0}無法存取 。  確保遵循此處定義的準則: https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 已掛起 | 指定的 ASE 掛起。 ASE 掛起可能是由於帳戶不足或虛擬網路配置無效造成的。 解決根本原因並恢復 ASE 以繼續服務流量。 |
| ASE 升級已啟動 | 平台升級到指定的 ASE 已經開始。 預計縮放操作會出現延遲。 |
| ASE 升級已完成 | 平台升級到指定的 ASE 已完成。 |
| 調整操作已啟動 | 應用服務計劃({0}) 已開始擴展。 所需狀態:{1}{2}我 工作。
| 縮放動作已完成 | 應用服務計劃({0}) 已完成縮放。 當前狀態:{1}{2}我 工作。 |
| 縮放操作失敗 | 應用服務計劃({0}) 無法擴展。 當前狀態:{1}{2}我 工作。 |

在 ASE 上啟用紀錄紀錄,請:

1. 在門戶中,轉到**診斷設定**。
1. 選擇 **「添加診斷設置**」。
1. 提供日誌集成的名稱。
1. 選擇並配置所需的日誌目標。
1. 選擇**套用服務環境平台紀錄**。

![ASE 診斷紀錄設定][4]

如果與日誌分析集成,則可以通過從 ASE 門戶中選擇**日誌**並針對**AppService 環境平臺日誌**創建查詢來查看日誌。 僅當 ASE 具有將觸發該事件的事件時,才會發出日誌。 如果您的 ASE 沒有此類事件,則沒有任何日誌。 要快速查看日誌分析工作區中的日誌示例,請使用 ASE 中的一個應用服務計畫執行縮放操作。 然後,您可以針對**AppService 環境平臺日誌**運行查詢以查看這些日誌。 

**建立警示**

要針對紀錄建立警示,請使用 Azure 監視器 以[「建立」 中的說明進行檢視與管理紀錄警報][logalerts]。 簡單地說︰

* 在 ASE 門戶中打開「警報」頁
* 選擇**新的警示規則**
* 選擇您的資源為紀錄分析工作區
* 使用自定義日誌搜索設置您的條件,以使用類似「AppService環境平臺日誌 |其中結果描述包含「已經開始縮放」或任何您想要的。 根據需要設置閾值。 
* 根據需要添加或創建操作組。 操作群組是定義對警示的回應的位置,例如寄送電子郵件或 SMS 訊息
* 命名警報並將其保存。

## <a name="upgrade-preference"></a>升級偏好設定

如果您有多個 ASEs,您可能希望在其它 ASEs 之前升級某些 ASA。 在 ASE**託管環境資源管理員**物件中,您可以設置**升級首選項**的值。 可以使用範本、ARMClienthttps://resources.azure.com或配置**升級首選項**設置。 三個可能的值是:

- **無**:Azure 將在沒有特定批處理中升級 ASE。 這是預設值。
- **早期**:您的 ASE 將在應用服務升級的前半部分升級。
- **延遲**:您的 ASE 將在應用服務升級的後半部分升級。

如果您使用的https://resources.azure.com是 ,請按照以下步驟設定**升級首選項**值:

1. 轉到resources.azure.com,然後使用 Azure 帳戶登錄。
1. \]\/\/\[\]\/\/\/\/\[\]透過資源訂閱 訂閱名稱資源群組 資源群組名稱 提供程式 Microsoft.Web 託管環境 ASE 名稱 。 \/ \[
1. 選擇頂部**的"讀/寫**"。
1. 選取 [編輯]****。
1. 將 **「升級首選項」** 設定為所需的三個值之一。
1. 選擇 **「補丁**」。

![資源 azure com 顯示][5]

當您有多個 ASEs 時,**升級首選項**功能最有意義,因為"早期"ASEs 將在"後期"ASEs 之前進行升級。 當您有多個 ASEs 時,應將開發和測試 ASEs 設置為"早期",並將生產 ASEs 設置為"後期"。

## <a name="pricing"></a>定價

稱為 *「隔離」* 的定價 SKU 僅適用於 ASEs。 託管在 ASE 中的所有應用服務計畫都位於隔離定價 SKU 中。 應用服務計劃的隔離費率可能因地區而異。

除了應用服務計劃的價格外,ASE 本身還有統一費率。 統一速率不會隨 ASE 的大小而變化。 它為每 15 個應用服務計畫實例的預設比例為一個前端的 ASE 基礎結構付費。

如果每 15 個應用服務計畫實例的預設前端比例不夠快,則可以調整添加前端的比率或前端的大小。 當您調整比率或大小時，需要為預設時未加入的前端核心付費。

例如，如果您將級別比率調整為 10，系統就會針對 App Service 方案中每 10 個執行個體新增一個前端。 固定費用涵蓋每隔 15 個執行個體 1 個前端的級別費率。 使用 10 級別比率時，您必須支付針對 10 個 App Service 方案執行個體新增的第三個前端費用。 在您達到 15 個執行個體時，不需要付費，因為它是自動新增的。

如果將前端的大小調整為兩個內核,但不調整比率,則為額外的內核付費。 ASE 是創建具有兩個前端的,因此,如果將大小增加到雙核前端,即使低於自動縮放閾值,您也會為兩個額外的內核付費。

如需詳細資訊，請參閱 [Azure App Service 價格][Pricing]。

## <a name="delete-an-ase"></a>刪除 ASE

若要刪除 ASE：

1. 在 **「應用服務環境**」窗格的頂部選擇 **「刪除**」。

1. 輸入 ASE 的名稱以確認您想要將它刪除。 刪除 ASE 時,還會刪除其中的所有內容。

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
