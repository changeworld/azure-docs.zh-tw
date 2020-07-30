---
title: 安裝內部部署資料閘道
description: 您必須先下載並安裝內部部署資料閘道，才能從 Azure Logic Apps 存取內部部署資料
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 9e50cdb16ee6acbdb903681984dcfbd7bfe170fa
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386124"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>安裝 Azure Logic Apps 的內部部署資料閘道

您必須先在本機電腦下載並安裝[內部部署的資料閘道](https://aka.ms/on-premises-data-gateway-installer)，才能[從 Azure Logic Apps 連線至內部部署資料來源](../logic-apps/logic-apps-gateway-connection.md)。 此閘道可作為橋樑，讓內部部署資料來源與邏輯應用程式之間快速地傳輸及加密資料。 您可以使用相同的閘道安裝搭配其他雲端服務，例如 Power BI、Power Automate、Power Apps 與 Azure Analysis Services。 如需如何搭配這些服務使用閘道的資訊，請參閱下列文章：

* [Microsoft Power Automate 內部部署的資料閘道](/power-automate/gateway-reference)
* [Microsoft Power BI 內部部署資料閘道](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 內部部署的資料閘道](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

此文章顯示如何下載、安裝及設定您的內部部署的資料閘道，讓您可以從 Azure Logic Apps 存取內部部署資料來源。 稍後在此主題中，您也可以深入了解[資料閘道的運作方式](#gateway-cloud-service)。 如需有關閘道的詳細資訊，請參閱[什麼是內部部署閘道？](/data-integration/gateway/service-gateway-onprem)\(部分機器翻譯\) 若要自動執行閘道安裝及管理工作，請瀏覽 PowerShell 資源庫以取得 [DataGateway PowerShell Cmdlet](https://www.powershellgallery.com/packages/DataGateway/3000.15.15) \(英文\)。

<a name="requirements"></a>

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有包含訂用帳戶的 Azure 帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

  * 您的 Azure 帳戶必須是公司帳戶或學校帳戶，如下所示 `username@contoso.com` 。 您不能使用 Azure B2B (來賓) 帳戶或個人 Microsoft 帳戶，例如 @hotmail.com 或 @outlook.com。

    > [!NOTE]
    > 如果您註冊 Office 365 供應項目，但未提供公司電子郵件地址，您的地址可能會像 `username@domain.onmicrosoft.com`。 您的帳戶會儲存在 Azure AD 租使用者中。 在大部分情況下，您的 Azure 帳戶的使用者主要名稱（UPN）與您的電子郵件地址相同。

    若要使用與 Microsoft 帳戶相關聯的[Visual Studio 標準訂](https://visualstudio.microsoft.com/vs/pricing/)用帳戶，請先[建立 Azure AD 租](../active-directory/develop/quickstart-create-new-tenant.md)使用者，或使用預設目錄。 將使用者連同密碼新增至目錄，然後將您 Azure 訂用帳戶的存取權授與該使用者。 接著，您就可以在安裝閘道時，使用此使用者名稱和密碼來進行登入。

  * 您的 Azure 帳戶必須只屬於單一[Azure Active Directory （Azure AD）租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 您必須使用相同的 Azure 帳戶來安裝和管理本機電腦上的閘道。

  * 當您安裝閘道時，您會使用您的 Azure 帳戶登入，這會將您的閘道安裝連結至您的 Azure 帳戶，而僅限該帳戶。 您無法將相同的閘道安裝連結到多個 Azure 帳戶或 Azure AD 的租使用者。

  * 稍後在 Azure 入口網站中，您必須使用相同的 Azure 帳戶來建立連結至閘道安裝的 Azure 閘道資源。 您只能將一個閘道安裝與一個 Azure 閘道資源互相連結。 不過，您的 Azure 帳戶可以連結到與 Azure 閘道資源相關聯的不同閘道安裝。 然後，您的邏輯應用程式就可以在觸發程式和可存取內部部署資料來源的動作中使用此閘道資源。

* 您的本機電腦需求如下：

  **最低需求**

  * .NET Framework 4.7.2
  * 64 位元版本的 Windows 7 或 Windows Server 2008 R2 (或更新版本)

  **建議需求**

  * 8 核心 CPU
  * 8 GB 記憶體
  * 64 位元版本的 Windows Server 2012 R2 或更新版本
  * 用於多工緩衝處理的固態硬碟 (SSD) 儲存體

  > [!NOTE]
  > 閘道不支援 Windows Server Core。

* **相關考量**

  * 只在本機電腦上安裝內部部署的資料閘道，而不是在網域控制站上安裝。 閘道不必安裝在和資料來源相同的電腦上。 針對所有資料來源，您只需要一個閘道，因此不需要針對每個資料來源安裝閘道。

    > [!TIP]
    > 若要盡量減少延遲，您可以將閘道安裝在最靠近資料來源的位置或同一部電腦上，但前提是您有相關權限。

  * 將閘道安裝在位在有線網路上、連線至網際網路、一律開啟且不會進入睡眠模式的本機電腦上。 否則，閘道無法執行，而且使用無線網路時，閘道效能可能會受到影響。

  * 如果您打算使用 Windows 驗證，請務必將閘道安裝在屬於與資料來源相同之 Active Directory 環境成員的電腦上。

  * 您為閘道安裝所選取的區域，是您稍後為邏輯應用程式建立 Azure 閘道資源時必須選取的相同位置。 根據預設，此區域是與 Azure AD 租用戶 (用來管理您的 Azure 帳戶) 相同的位置。 不過，您可以在閘道安裝期間變更此位置。

  * 如果您正在更新您的閘道安裝，請先將您目前的閘道解除安裝，以獲得更簡潔的體驗。

    最佳做法是確定您使用的是支援的版本。 Microsoft 會每個月發行一次新的內部部署的資料閘道更新，而且目前僅支援內部部署的資料閘道的最後六個版本。 如果您使用的版本發生問題，請嘗試[升級至最新版本](https://aka.ms/on-premises-data-gateway-installer)，因為您的問題可能會在最新版本中解決。

  * 閘道有兩種模式：標準模式與個人模式 (僅適用於 Power BI)。 同一部電腦上不能有一個以上的閘道以相同模式執行。

  * Azure Logic Apps 支援透過閘道進行讀取及寫入作業。 不過，這些作業的[承載大小有限制](/data-integration/gateway/service-gateway-onprem#considerations) \(部分機器翻譯\)。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安裝資料閘道

1. [在本機電腦上下載並執行閘道安裝程式](https://aka.ms/on-premises-data-gateway-installer)。

1. 請檢閱最低需求、維持預設安裝路徑、接受使用規定，然後選取 [安裝]。

   ![檢閱需求並接受使用規定](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. 成功安裝閘道之後，請提供您 Azure 帳戶的電子郵件地址，然後選取 [登入]，例如：

   ![使用公司或學校帳戶登入](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   您的閘道安裝只能連結到一個 Azure 帳戶。

1. 選取 [在此電腦上註冊新的閘道] > [下一步]。 此步驟將會向[閘道雲端服務](#gateway-cloud-service)註冊您的閘道安裝。

   ![在本機電腦上註冊閘道](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 為您的閘道安裝提供下列資訊：

   * 在您 Azure AD 租用戶中唯一的閘道名稱
   * 您想要使用的修復金鑰必須至少有八個字元
   * 確認您的修復金鑰

   ![提供閘道安裝的資訊](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 將您的修復金鑰儲存並保留在安全的地方。 如果您想要變更位置、移動、復原或接管閘道安裝，則需要此金鑰。

   請注意 [新增到現有的閘道叢集] 的選項，當您針對[高可用性案例](#high-availability)安裝額外閘道時，就會選取該叢集。

1. 檢查針對閘道安裝所使用的閘道雲端服務與 [Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)的區域。 根據預設，此區域是與您 Azure 帳戶的 Azure AD 租用戶相同的位置。

   ![確認閘道服務與服務匯流排的區域](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 若要接受預設區域，請選取 [設定]。 不過，如果預設區域不是最接近您的區域，您可以變更區域。

   *為什麼要變更閘道安裝的區域？*

   例如，若要減少延遲，您可以將閘道的區域變更為與邏輯應用程式相同的區域。 或者，您可以選取最接近的內部部署資料來源區域。 「Azure 中的閘道資源」和邏輯應用程式可位於不同位置。

   1. 在目前區域旁邊，選取 [變更區域]。

      ![變更目前的閘道區域](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 在下一頁上，開啟 [選取區域] 清單，選取您要的區域，然後選取 [完成]。

      ![選取閘道服務的另一個區域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 檢閱最後確認視窗中的資訊。 此範例會為 Logic Apps、Power BI、Power Apps 與 Power Automate 使用相同帳戶，讓所有這些服務都可使用閘道。 當您準備好時，請選取 [關閉]。

   ![確認資料閘道資訊](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 現在，[為閘道安裝建立 Azure 資源](../logic-apps/logic-apps-gateway-connection.md)。

## <a name="check-or-adjust-communication-settings"></a>檢查或調整通訊設定

內部部署的資料閘道依賴 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)來使用雲端連線，以及建立與閘道相關聯 Azure 區域對應的連出連線。 如果您的工作環境要求流量經由 Proxy 或防火牆存取網際網路，此限制可能會使內部部署的資料閘道無法連線到閘道雲端服務與 Azure 服務匯流排。 閘道有數個通訊設定可供您調整。 如需詳細資訊，請參閱下列主題：

* [調整內部部署的資料閘道的通訊設定](/data-integration/gateway/service-gateway-communication) \(部分機器翻譯\)
* [設定內部部署的資料閘道的 Proxy 設定](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性支援

若要避免內部部署資料存取發生單一失敗點，您可以在不同電腦上有多個閘道安裝 (僅限標準模式)，並將其設定為叢集或群組。 如此一來，如果主要閘道無法使用，則系統會將資料要求路由傳送至第二個閘道，依此類推。 因為您只能在一部電腦上安裝一個標準閘道，所以您必須將每個額外閘道都安裝在叢集中的其他電腦上。 所有搭配內部部署的資料閘道運作的連接器都支援高可用性。

* 您必須已經有至少一個閘道安裝 (使用相同的 Azure 帳戶) 作為主要閘道，以及該安裝的修復金鑰。

* 主要閘道必須執行 2017 年 11 月或更新版本的閘道更新。

設定主要閘道之後，當要安裝另一個閘道時，請選取 [新增到現有的閘道叢集]、選取主要閘道 (您安裝的第一個閘道)，並提供該閘道的修復金鑰。 如需詳細資訊，請參閱[適用於內部部署資料閘道的高可用性叢集](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>變更位置、遷移、還原或取代現有閘道

如果您必須變更閘道的位置、閘道安裝移至新的電腦、復原受損的閘道，或取得現有閘道的擁有權，您會需要在閘道安裝期間所提供的修復金鑰。

> [!NOTE]
> 在具有原始閘道安裝的電腦上還原閘道之前，您必須先將該電腦上的閘道解除安裝。 此動作會中斷原始閘道的連線。
> 如果您移除或刪除任何雲端服務的閘道叢集，就無法還原該叢集。

1. 在現有閘道所在的電腦上執行閘道安裝程式。

1. 安裝程式開啟後，登入用來安裝閘道的同一個 Azure 帳戶。

1. 選取 [移轉、還原或接管現有的閘道] > [下一步]，例如：

   ![選取 [遷移、還原或取代現有閘道]](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 選取可用的叢集和閘道，然後輸入所選閘道的修復金鑰，例如：

   ![選取閘道並提供修復金鑰](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 若要變更區域，請選取 [變更區域]，並選取新區域。

1. 當您準備好時，選取 [設定] 即可完成工作。

## <a name="tenant-level-administration"></a>租用戶層級管理

若要了解 Azure AD 租用戶中所有內部部署的資料閘道，該租用戶中的全域管理員可以使用租用戶系統管理員的身分登入 [Power Platform 系統管理中心](https://powerplatform.microsoft.com)，然後選取 [資料閘道] 選項。 如需詳細資訊，請參閱[內部部署的資料閘道的租用戶層級管理](/data-integration/gateway/service-gateway-tenant-level-admin)。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重新啟動閘道

根據預設，本機電腦上安裝的閘道會以名為「內部部署的資料閘道服務」的 Windows 服務帳戶執行。 不過，閘道安裝會針對其「登入身分」帳戶認證使用 `NT SERVICE\PBIEgwService` 名稱，並具有「以服務方式登入」權限。

> [!NOTE]
> 您的 Windows 服務帳戶與用來連線至內部部署資料來源的帳戶不同，也與您用來登入雲端服務的 Azure 帳戶不同。

和其他任何 Windows 服務一樣，您可以透過各種方式來啟動和停止閘道。 如需詳細資訊，請參閱[重新啟動內部部署的資料閘道](/data-integration/gateway/service-gateway-restart) \(部分機器翻譯\)。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>閘道運作方式

您組織中的使用者可以存取已獲授權存取的內部部署資料。 不過，在這些使用者可以連線到您的內部部署資料來源之前，您必須先安裝及設定內部部署的資料閘道。 通常，系統管理員是安裝及設定閘道的人員。 這些動作可能需要伺服器管理員權限或您內部部署伺服器的特殊知識。

閘道有助於讓幕後通訊更快且更安全。 此通訊在雲端使用者、閘道雲端服務與內部部署資料來源之間進行。 閘道雲端服務會將資料來源認證和閘道詳細資料予以加密並儲存。 服務也會在使用者、閘道與您的內部部署資料來源之間路由傳送查詢與其結果。

閘道可搭配防火牆運作，而且僅使用輸出連線。 源自閘道代理程式的所有流量都是安全連出流量。 閘道會在加密通道上經過 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)轉送來自內部部署來源的資料。 此服務匯流排會建立閘道與呼叫服務之間的通道，但不會儲存任何資料。 透過閘道傳送的所有資料都會加密。

![內部部署的資料閘道的架構](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 視雲端服務而定，您可能必須設定閘道的資料來源。

這些步驟說明當您與連線到內部部署資料來源的元素互動時，會發生什麼狀況︰

1. 雲端服務會建立查詢，以及資料來源的加密認證。 服務接著會將查詢與認證傳送到閘道佇列以進行處理。

1. 閘道雲端服務會分析該查詢，並將要求推送至 Azure 服務匯流排。

1. Azure 服務匯流排會將暫止的要求傳送至閘道。

1. 閘道收到查詢、解密認證，並使用那些認證連線至一或多個資料來源。

1. 閘道將查詢傳送至資料來源執行。

1. 結果會從資料來源傳送回閘道，然後再到閘道雲端服務。 閘道雲端服務接著就會使用結果。

### <a name="authentication-to-on-premises-data-sources"></a>對內部部署資料來源進行驗證

預存認證是用來從閘道連線到內部部署資料來源。 無論使用者是誰，閘道都會使用預存認證來連線。 特定服務可能會有驗證例外狀況，例如 Power BI 中 Analysis Services 的 DirectQuery 與 LiveConnect。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft 雲端服務使用 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) 來驗證使用者。 Azure AD 租用戶包含使用者名稱與安全性群組。 一般來說，您用來登入的電子郵件地址與您帳戶的使用者主體名稱 (UPN) 相同。

### <a name="what-is-my-upn"></a>我的 UPN 為何？

如果您不是網域系統管理員，您可能不知道您的 UPN。 若要尋找您帳戶的 UPN，請從您的工作站執行 `whoami /upn` 命令。 雖然結果看起來像是電子郵件地址，但該結果是您本機網域帳戶的 UPN。

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>將內部部署 Active Directory 與 Azure AD 同步

內部部署 Active Directory 帳戶與 Azure AD 帳戶的 UPN 必須相同。 因此，請確定每個內部部署 Active Directory 帳戶都符合您的 Azure AD 帳戶。 雲端服務只知道 Azure AD 內的帳戶。 因此，您不需要將帳戶新增至內部部署 Active Directory。 如果帳戶不存在於 Azure AD 中，您就無法使用該帳戶。

以下是您可以讓內部部署 Active Directory 帳戶與 Azure AD 相符的方式。

* 手動將帳戶新增至 Azure AD。

  在 Azure 入口網站或 Microsoft 365 系統管理中心內建立帳戶。 請確定帳戶名稱符合內部部署 Active Directory 帳戶的 UPN。

* 使用 Azure Active Directory Connect 工具，將本機帳戶同步至您的 Azure AD 租用戶。

  Azure AD Connect 工具會提供目錄同步與驗證設定的選項。 這些選項包括密碼雜湊同步處理、傳遞驗證與同盟。 如果您不是租用戶管理員或本機網域管理員，請連絡您的 IT 管理員以設定 Azure AD Connect。 Azure AD Connect 可確保您的 Azure AD UPN 符合您的本機 Active Directory UPN。 如果您搭配 Power BI 或單一登入 (SSO) 功能使用 Analysis Services 即時連線，這種比對會很有用。

  > [!NOTE]
  > 使用 Azure AD Connect 工具同步處理帳戶會在您的 Azure AD 租用戶中建立新帳戶。

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>常見問題集和疑難排解

* [內部部署資料閘道常見問題集](/data-integration/gateway/service-gateway-onprem-faq)
* [針對內部部署的資料閘道進行疑難排解](/data-integration/gateway/service-gateway-tshoot) \(部分機器翻譯\)
* [監視及最佳化閘道效能](/data-integration/gateway/service-gateway-performance) \(部分機器翻譯\)

## <a name="next-steps"></a>後續步驟

* [從邏輯應用程式連線至內部部署資料](../logic-apps/logic-apps-gateway-connection.md)
* [企業整合功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)
