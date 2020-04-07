---
title: 安裝內部部署資料閘道
description: 您必須先下載並安裝內部部署資料閘道，才能從 Azure Logic Apps 存取內部部署資料
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673821"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>安裝 Azure Logic Apps 的內部部署資料閘道

在您可以從[Azure 邏輯套用到本地資料來源](../logic-apps/logic-apps-gateway-connection.md)之前,請在本地電腦上下載並安裝[本地資料閘道](https://aka.ms/on-premises-data-gateway-installer)。 閘道用作在本地資料源和邏輯應用之間提供快速資料傳輸和加密的橋樑。 您可以將同一閘道安裝與其他雲端服務(如 Power BI、電源自動、電源應用和 Azure 分析服務)一起使用。 有關如何將閘道用於這些服務的資訊,請參閱以下文章:

* [微軟電源自動本地資料閘道](/power-automate/gateway-reference)
* [Microsoft Power BI 內部部署資料閘道](/power-bi/service-gateway-onprem)
* [微軟電源應用本地資料閘道](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

本文演示如何下載、安裝和設置本地數據閘道,以便從 Azure 邏輯應用存取本地資料來源。 在本主題稍後部分,您還可以瞭解有關[數據網關工作原理](#gateway-cloud-service)的詳細資訊。 有關閘道的詳細資訊,請參閱[什麼是本地閘道](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)? 要自動執行閘道安裝與管理工作,請造訪 PowerShell 函式庫,檢視[DataGateway PowerShell cmdlet](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)。

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果沒有訂閱的 Azure 帳號,[請註冊免費 Azure 帳號](https://azure.microsoft.com/free/)。

  * Azure 帳號必須屬於單個[Azure 的動作目錄 (Azure AD) 租戶或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 您必須使用相同的 Azure 帳戶在本地電腦上安裝和管理閘道。

  * 在閘道安裝期間,使用 Azure 帳戶登錄,該帳戶將閘道安裝連結到 Azure 帳戶,並且僅將該帳戶連結到該帳戶。 稍後,在 Azure 門戶中,在創建註冊和聲明閘道安裝的 Azure 閘道資源時,必須使用相同的 Azure 帳戶和 Azure AD 租戶。 在 Azure 邏輯應用中,本地觸發器和操作使用閘道資源連接到本地數據來源。

    > [!NOTE]
    > 您只能將一個閘道安裝和一個 Azure 閘道資源相互連結。 無法將同一閘道安裝連結到多個 Azure 帳戶或 Azure 閘道資源。 但是,Azure 帳戶可以連結到多個閘道安裝和 Azure 閘道資源。 在本地觸發器或操作中,可以從各種 Azure 訂閱中選擇,然後選擇關聯的閘道資源。

  * 您需要使用工作帳戶或學校帳戶(也稱為*組織*帳戶)登入,該帳戶看起來像`username@contoso.com`。 不能使用 Azure B2B(來賓)帳戶或個人 Microsoft 帳戶@hotmail.com,@outlook.com如 。

    > [!TIP]
    > 如果您註冊了 Office 365 產品,但沒有提供您的工作電子郵件地址,則您的位址`username@domain.onmicrosoft.com`可能看起來像 。 您的帳戶存儲在 Azure 活動目錄 (Azure AD) 中的租戶中。 在大多數情況下,Azure AD 帳戶的用戶主體名稱 (UPN) 與您的電子郵寄地址相同。
    >
    > 要使用連結到 Microsoft 帳戶的[可視化工作室標準訂閱](https://visualstudio.microsoft.com/vs/pricing/),請先在[Azure AD 中創建租戶](../active-directory/develop/quickstart-create-new-tenant.md)或使用預設目錄。 向目錄添加具有密碼的使用者,然後授予該使用者對 Azure 訂閱的訪問許可權。 接著，您就可以在安裝閘道時，使用此使用者名稱和密碼來進行登入。

* 您的本機電腦需求如下：

  **最低要求**

  * .NET Framework 4.7.2
  * 64 位元版本的 Windows 7 或 Windows Server 2008 R2 (或更新版本)

  **建議需求**

  * 8 核心 CPU
  * 8 GB 記憶體
  * 64 位元版本的 Windows 伺服器 2012 R2 或更高版本
  * 用於背景的固態驅動程式 (SSD) 儲存

  > [!NOTE]
  > 閘道不支援 Windows 伺服器核心。

* **相關注意事項**

  * 僅在本地電腦上安裝本地數據閘道,而不是域控制器。 閘道不必安裝在和資料來源相同的電腦上。 對於所有數據源,您只需要一個閘道,因此無需為每個資料源安裝閘道。

    > [!TIP]
    > 若要盡量減少延遲，您可以將閘道安裝在最靠近資料來源的位置或同一部電腦上，但前提是您有相關權限。

  * 在有線網路上安裝閘道,連接到網路,始終打開,並且不會進入睡眠狀態。 否則,閘道無法運行,並且性能可能會因無線網路而受到影響。

  * 如果計劃使用 Windows 身份驗證,請確保在與數據源屬於同一活動目錄環境的電腦上安裝閘道。

  * 為閘道安裝選擇的區域與以後為邏輯應用創建 Azure 閘道資源時必須選擇的位置相同。 默認情況下,此區域與管理 Azure 帳戶的 Azure AD 租戶的位置相同。 但是,您可以在閘道安裝期間更改位置。

  * 如果要將閘道安裝更新到最新版本,請先卸載當前閘道以獲得更清潔的體驗。

  * 閘道有兩種模式:標準模式和個人模式,僅適用於 Power BI。 在同一台電腦上不能有多個閘道以同一模式運行。

  * Azure 邏輯應用支援通過閘道執行讀取和寫入操作。 但是,這些操作[的有效載荷大小有限制](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安裝資料閘道

1. [在本機電腦上下載並執行閘道安裝程式](https://aka.ms/on-premises-data-gateway-installer)。

1. 查看最低要求,保留預設安裝路徑,接受使用條款,然後選擇「**安裝**」。

   ![審查要求並接受使用條款](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. 成功安裝閘道後,為 Azure 帳號提供電子郵件地址,然後選擇 **「登入**」,例如:

   ![使用公司或學校帳戶登入](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   閘道安裝只能連結到一個 Azure 帳戶。

1.  > 選擇**在此電腦上註冊新閘道****「下一步**」 。 此步驟將閘道安裝註冊到[閘道雲服務](#gateway-cloud-service)。

   ![在本地電腦上註冊閘道](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 為您的閘道安裝提供下列資訊：

   * 在 Azure AD 租戶唯一的閘道名稱
   * 要使用復失金鑰(必須至少包含八個字元)
   * 確認您的修復金鑰

   ![提供閘道安裝資訊](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 將您的修復金鑰儲存並保留在安全的地方。 如果要更改位置、移動、恢復或接管閘道安裝,則需要此密鑰。

   請注意添加到**現有閘道群集**的選項,在安裝用於[高可用性方案](#high-availability)的其他閘道時選擇該選項。

1. 檢查閘道安裝使用的閘道雲服務和[Azure 服務總線](https://azure.microsoft.com/services/service-bus/)的區域。 默認情況下,此區域與 Azure 帳戶的 Azure AD 租戶的位置相同。

   ![確認閘道服務和服務匯流排的區域](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 要接受預設區域,請選擇「**設定**」 。 但是,如果默認區域不是最接近您的區域,則可以更改該區域。

   *為什麼要變更閘道安裝的區域？*

   例如，若要減少延遲，您可以將閘道的區域變更為與邏輯應用程式相同的區域。 或者，您可以選取最接近的內部部署資料來源區域。 「Azure 中的閘道資源」** 和邏輯應用程式可位於不同位置。

   1. 在目前區域旁邊，選取 [變更區域]****。

      ![變更目前閘道區域](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 在下一頁上,打開 **「選擇區域**」清單,選擇所需的區域,然後選擇 **「完成**」。。

      ![為閘道服務選擇其他區域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 查看最終確認視窗中的資訊。 此示例對邏輯應用、電源 BI、電源應用和電源自動應用使用相同的帳戶,因此閘道可用於所有這些服務。 準備就緒后,選擇 **"關閉**"。

   ![確認資料閘道資訊](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 現在[為閘道安裝建立 Azure 資源](../logic-apps/logic-apps-gateway-connection.md)。

## <a name="check-or-adjust-communication-settings"></a>檢查或調整通訊設定

本地資料閘道依賴於[Azure 服務匯流線](../service-bus-messaging/service-bus-messaging-overview.md)進行雲端連接,並建立到閘道關聯的 Azure 區域的相應出站連接。 如果工作環境要求流量通過代理或防火牆訪問 Internet,則此限制可能會阻止本地數據閘道連接到閘道雲服務和 Azure 服務總線。 閘道具有多個通訊設置,您可以調整這些設置。 如需詳細資訊，請參閱下列主題：

* [調整本地資料閘道的通訊設定](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [設定內部部署資料閘道的 Proxy 設定](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性支援

為了避免本地數據訪問的單點故障,可以在不同的計算機上使用多個閘道安裝(僅限標準模式),並將其設置為群集或組。 這樣,如果主閘道不可用,數據請求將路由到第二個閘道,等等。 由於只能在電腦上安裝一個標準閘道,因此必須在不同的電腦上安裝群集中的每個附加閘道。 與本地數據閘道配合使用的所有連接器都支援高可用性。

* 您必須至少有一個閘道安裝,其 Azure 帳戶與主閘道和該安裝的復複密鑰相同。

* 主要閘道必須執行 2017 年 11 月或更新版本的閘道更新。

設定主閘道後,當您開始安裝另一個閘道時,選擇 **「新增到現有閘道群集**」,選擇主閘道(即安裝的第一個閘道)並為該閘道提供恢複密鑰。 如需詳細資訊，請參閱[適用於內部部署資料閘道的高可用性叢集](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>變更位置、遷移、還原或取代現有閘道

如果您必須變更閘道的位置、閘道安裝移至新的電腦、復原受損的閘道，或取得現有閘道的擁有權，您會需要在閘道安裝期間所提供的修復金鑰。

1. 在具有現有閘道的電腦上運行閘道安裝程式。 如果沒有最新的閘道安裝程式,[請下載最新的閘道版本](https://aka.ms/on-premises-data-gateway-installer)。

   > [!NOTE]
   > 在還原具有原始閘道安裝的電腦上的閘道之前,必須首先卸載該電腦上的閘道。 此操作將斷開原始網關。
   > 如果刪除或刪除任何雲服務的閘道群集,則無法還原該群集。

1. 安裝程式打開後,使用用於安裝閘道的相同 Azure 帳戶登錄。

1. 選擇 **'移植、還原或接管現有閘道** > **下一步**',例如:

   ![選取 [遷移、還原或取代現有閘道]](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 從可用群組和閘道中選擇,然後輸入所選閘道的復失鑰,例如:

   ![選擇閘道並提供復失金鑰](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 要更改區域,請選擇 **「更改區域**」並選擇新區域。

1. 準備就緒后,選擇 **「設定」,** 以便完成任務。

## <a name="tenant-level-administration"></a>租戶級管理

要查看 Azure AD 租戶中的所有本地數據閘道,該租戶中的全域管理員可以作為租戶管理員登錄到[Power 平臺管理中心](https://powerplatform.microsoft.com),並選擇 **「數據閘道」** 選項。 有關詳細資訊,請參閱[本地資料閘道的租戶級管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重新啟動閘道

預設情況下,本地電腦上的閘道安裝運行為名為「本地資料閘道服務」的 Windows 服務帳戶。 但是,閘道安裝使用`NT SERVICE\PBIEgwService`名稱為其「登錄為」帳戶憑據,並且具有「以服務身份登錄」的許可權。

> [!NOTE]
> Windows 服務帳戶不同於用於連接到本地數據源的帳戶,不同於登錄到雲服務時使用的 Azure 帳戶。

與任何其他 Windows 服務一樣,您可以通過多種方式啟動和停止閘道。 有關詳細資訊,請參閱[重新啟動本地資料閘道](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>閘道運作方式

組織中的用戶可以訪問他們已為其具有授權訪問許可權的本地數據。 但是,在這些使用者可以連接到本地數據來源之前,您需要安裝和設置本地資料閘道。 通常,管理員是安裝和設置閘道的人員。 這些操作可能需要伺服器管理員許可權或有關本地伺服器的特殊知識。

閘道有助於促進更快、更安全的幕後通信。 此通信在雲中的使用者、閘道雲服務和本地數據源之間流動。 閘道雲端服務會將資料來源認證和閘道詳細資料予以加密並儲存。 該服務還會在使用者、閘道和本地數據源之間路由查詢及其結果。

閘道可搭配防火牆運作，而且僅使用輸出連線。 所有流量都作為安全出站流量源自網關代理。 閘道通過[Azure 服務總線](../service-bus-messaging/service-bus-messaging-overview.md)在加密通道上中繼來自本地源的數據。 此服務匯流排會建立閘道與呼叫服務之間的通道，但不會儲存任何資料。 透過閘道傳送的所有資料都會加密。

![本地資料閘道的結構](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 根據雲服務的不同,您可能需要為閘道設定數據來源。

這些步驟描述與連線到本地資料來源的元素互動時發生的情況:

1. 雲服務創建查詢以及數據源的加密認證。 然後,服務將查詢和憑據發送到閘道列進行處理。

1. 閘道雲服務分析查詢並將請求推送到 Azure 服務總線。

1. Azure 服務總線將掛起的請求發送到閘道。

1. 閘道獲取查詢、解密認證,並使用這些認證到一個或多個資料來源。

1. 閘道將查詢發送到數據源以進行運行。

1. 結果會從資料來源傳送回閘道，然後再到閘道雲端服務。 閘道雲端服務接著就會使用結果。

### <a name="authentication-to-on-premises-data-sources"></a>內部部署資料來源的驗證

存儲的憑據用於從閘道連接到本地資料來源。 無論使用者如何,閘道都使用儲存的憑據進行連接。 特定服務(如 Power BI 中的 DirectQuery 和 LiveConnect)分析服務可能存在身份驗證異常。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft 雲服務使用[Azure AD](../active-directory/fundamentals/active-directory-whatis.md)對使用者進行身份驗證。 Azure AD 租戶包含使用者名和安全組。 通常,用於登錄的電子郵寄地址與帳戶的用戶主體名稱 (UPN) 相同。

### <a name="what-is-my-upn"></a>我的 UPN 是什麼?

如果您不是域管理員,您可能不知道您的 UPN。 要查找帳戶的 UPN,請從工作站`whoami /upn`運行該命令。 儘管結果看起來像一個電子郵寄地址,但結果是本地域帳戶的 UPN。

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>同步處理內部部署 Active Directory 和 Azure AD

本地活動目錄帳戶和 Azure AD 帳戶的 UPN 必須相同。 因此,請確保每個本地活動目錄帳戶與您的 Azure AD 帳戶匹配。 雲服務僅瞭解 Azure AD 中的帳戶。 因此,您無需將帳戶添加到本地活動目錄。 如果 Azure AD 中不存在該帳戶,則不能使用該帳戶。

以下是將本地活動目錄帳戶與 Azure AD 匹配的方法。

* 手動將帳戶添加到 Azure AD。

  在 Azure 門戶或 Microsoft 365 管理中心中創建帳戶。 確保帳戶名稱與本地活動目錄帳戶的 UPN 匹配。

* 使用 Azure 活動目錄連接工具將本地帳戶同步到 Azure AD 租戶。

  Azure AD 連接工具提供目錄同步和身份驗證設置的選項。 這些選項包括密碼哈希同步、直通身份驗證和聯合。 如果您不是租戶管理員或本地域管理員,請與IT管理員聯繫以設置Azure AD連接。 Azure AD 連接可確保 Azure AD UPN 與本地活動目錄 UPN 匹配。 如果您使用具有 Power BI 或單一登錄 (SSO) 功能的即時連接,則此匹配會有所説明。

  > [!NOTE]
  > 將帳戶與 Azure AD 連接工具同步會在 Azure AD 租戶中創建新帳戶。

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>常見問題解答和故障排除

如需詳細資訊，請參閱下列主題：

* [本地資料閘道常見問題解答](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [對內部部署的資料閘道進行疑難排解](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [監視及調整閘道效能](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>後續步驟

* [從邏輯套用連線到本地資料](../logic-apps/logic-apps-gateway-connection.md)
* [企業整合功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)
