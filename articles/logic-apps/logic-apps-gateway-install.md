---
title: 安裝內部部署資料閘道
description: 您必須先下載並安裝內部部署資料閘道，才能從 Azure Logic Apps 存取內部部署資料
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80673821"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>安裝 Azure Logic Apps 的內部部署資料閘道

[從 Azure Logic Apps 連接到內部部署資料來源](../logic-apps/logic-apps-gateway-connection.md)之前，請先在本機電腦上下載並安裝內部[部署資料閘道](https://aka.ms/on-premises-data-gateway-installer)。 閘道可做為橋接器，在內部部署的資料來源與邏輯應用程式之間快速地傳輸和加密資料。 您可以將相同的閘道安裝與其他雲端服務搭配使用，例如 Power BI、電源自動化、Power Apps 和 Azure Analysis Services。 如需如何搭配使用閘道與這些服務的相關資訊，請參閱下列文章：

* [Microsoft Power 自動化內部部署資料閘道](/power-automate/gateway-reference)
* [Microsoft Power BI 內部部署資料閘道](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 內部部署資料閘道](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

本文說明如何下載、安裝及設定您的內部部署資料閘道，讓您可以從 Azure Logic Apps 存取內部部署資料來源。 您也可以在本主題稍後深入瞭解[資料閘道的運作方式](#gateway-cloud-service)。 如需閘道的詳細資訊，請參閱[什麼是內部部署閘道](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)？ 若要自動執行閘道安裝和管理工作，請流覽 PowerShell 資源庫以取得[DataGateway powershell Cmdlet](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)。

<a name="requirements"></a>

## <a name="prerequisites"></a>先決條件

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶的 Azure 帳戶，請[註冊免費的 azure 帳戶](https://azure.microsoft.com/free/)。

  * 您的 Azure 帳戶必須屬於單一[Azure Active Directory （Azure AD）租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 您必須使用相同的 Azure 帳戶來安裝和管理本機電腦上的閘道。

  * 在閘道安裝期間，您會使用您的 Azure 帳戶登入，這會將您的閘道安裝連結至您的 Azure 帳戶，而只會將該帳戶 之後，在 Azure 入口網站中，當您建立註冊並宣告閘道安裝的 Azure 閘道資源時，您必須使用相同的 Azure 帳戶和 Azure AD 租使用者。 在 Azure Logic Apps 中，內部部署觸發程式和動作會使用閘道資源來連接到內部部署資料來源。

    > [!NOTE]
    > 您只能將一個閘道安裝和一個 Azure 閘道資源連結到彼此。 您無法將相同的閘道安裝連結至多個 Azure 帳戶或 Azure 閘道資源。 不過，Azure 帳戶可以連結至多個閘道安裝和 Azure 閘道資源。 在內部部署觸發程式或動作中，您可以從各種不同的 Azure 訂用帳戶中進行選取，然後選取相關聯的閘道資源。

  * 您必須使用工作帳戶或學校帳戶（也稱為*組織*帳戶）登入，這看起來像`username@contoso.com`這樣。 您不能使用 Azure B2B （來賓）帳戶或個人 Microsoft 帳戶，例如@hotmail.com或@outlook.com。

    > [!TIP]
    > 如果您註冊 Office 365 供應專案，但未提供公司電子郵件地址，則您的位址可能會`username@domain.onmicrosoft.com`類似。 您的帳戶會儲存在 Azure Active Directory （Azure AD）中的租使用者內。 在大部分情況下，您 Azure AD 帳戶的使用者主體名稱（UPN）與您的電子郵件地址相同。
    >
    > 若要使用連結至 Microsoft 帳戶的[Visual Studio 標準訂](https://visualstudio.microsoft.com/vs/pricing/)用帳戶，請先[在 Azure AD 中建立租](../active-directory/develop/quickstart-create-new-tenant.md)使用者，或使用預設目錄。 將具有密碼的使用者新增至目錄，然後授與該使用者對您 Azure 訂用帳戶的存取權。 接著，您就可以在安裝閘道時，使用此使用者名稱和密碼來進行登入。

* 您的本機電腦需求如下：

  **最低要求條件**

  * .NET Framework 4.7.2
  * 64 位元版本的 Windows 7 或 Windows Server 2008 R2 (或更新版本)

  **建議需求**

  * 8 核心 CPU
  * 8 GB 記憶體
  * 64位版本的 Windows Server 2012 R2 或更新版本
  * 用於緩衝處理的固態硬碟（SSD）儲存體

  > [!NOTE]
  > 閘道不支援 Windows Server Core。

* **相關考慮**

  * 只在本機電腦上安裝內部部署資料閘道，而不是網域控制站。 閘道不必安裝在和資料來源相同的電腦上。 您的所有資料來源都只需要一個閘道，因此您不需要為每個資料來源安裝閘道。

    > [!TIP]
    > 若要盡量減少延遲，您可以將閘道安裝在最靠近資料來源的位置或同一部電腦上，但前提是您有相關權限。

  * 將閘道安裝在有線網路上的電腦上、連線到網際網路、一律開啟，而且不會進入睡眠狀態。 否則，閘道將無法執行，而且效能可能會透過無線網路而受到影響。

  * 如果您打算使用 Windows 驗證，請確定您將閘道安裝在與資料來源相同 Active Directory 環境成員的電腦上。

  * 您為閘道安裝所選取的區域，是您稍後為邏輯應用程式建立 Azure 閘道資源時必須選取的相同位置。 根據預設，此區域與管理 Azure 帳戶的 Azure AD 租使用者位置相同。 不過，您可以在閘道安裝期間變更位置。

  * 如果您要將閘道安裝更新為最新版本，請先卸載您目前的閘道，以獲得更簡潔的體驗。

  * 閘道有兩種模式： [標準模式] 和 [個人模式]，僅適用于 Power BI。 同一部電腦上不能有一個以上的閘道在相同的模式下執行。

  * Azure Logic Apps 支援透過閘道進行的讀取和寫入作業。 不過，這些作業對[其裝載大小有限制](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安裝資料閘道

1. [在本機電腦上下載並執行閘道安裝程式](https://aka.ms/on-premises-data-gateway-installer)。

1. 請參閱最低需求，保留預設安裝路徑，接受使用規定，然後選取 [**安裝**]。

   ![審查需求並接受使用條款](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. 成功安裝閘道之後，請提供您 Azure 帳戶的電子郵件地址，然後選取 [登**入**]，例如：

   ![使用公司或學校帳戶登入](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   您的閘道安裝只能連結到一個 Azure 帳戶。

1. 選取 **[** 在這部 > **電腦上註冊新的閘道**]。 此步驟會向[閘道雲端服務](#gateway-cloud-service)註冊您的閘道安裝。

   ![在本機電腦上註冊閘道](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. 為您的閘道安裝提供下列資訊：

   * 在您的 Azure AD 租使用者中唯一的閘道名稱
   * 您想要使用的修復金鑰，必須至少有八個字元
   * 確認您的修復金鑰

   ![提供閘道安裝的資訊](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 將您的修復金鑰儲存並保留在安全的地方。 如果您想要變更位置、移動、復原或接管閘道安裝，則需要此金鑰。

   請注意，您可以選擇**新增至現有的閘道**叢集，而當您針對[高可用性案例](#high-availability)安裝額外的閘道時，就會選取此選項。

1. 檢查閘道雲端服務的區域，以及閘道安裝所使用的[Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)。 根據預設，此區域是與您的 Azure 帳戶 Azure AD 租使用者相同的位置。

   ![確認閘道服務和服務匯流排的區域](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 若要接受預設區域，請選取 [**設定**]。 不過，如果預設區域不是最接近您的區域，您可以變更該區域。

   *為什麼要變更閘道安裝的區域？*

   例如，若要減少延遲，您可以將閘道的區域變更為與邏輯應用程式相同的區域。 或者，您可以選取最接近的內部部署資料來源區域。 「Azure 中的閘道資源」** 和邏輯應用程式可位於不同位置。

   1. 在目前區域旁邊，選取 [變更區域]****。

      ![變更目前的閘道區域](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 在下一個頁面上，開啟 [**選取區域**] 清單，選取您想要的區域，然後選取 [**完成**]。

      ![選取閘道服務的另一個區域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 檢查最後確認視窗中的資訊。 這個範例會使用相同的帳戶來進行 Logic Apps、Power BI、電源應用程式和電源自動化，讓閘道可供所有這些服務使用。 當您準備好時，請選取 [**關閉**]。

   ![確認資料閘道資訊](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 現在，[為您的閘道安裝建立 Azure 資源](../logic-apps/logic-apps-gateway-connection.md)。

## <a name="check-or-adjust-communication-settings"></a>檢查或調整通訊設定

內部部署資料閘道取決於雲端連線[Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)，並建立與閘道相關聯 Azure 區域的對應輸出連線。 如果您的工作環境要求流量通過 proxy 或防火牆來存取網際網路，此限制可能會導致內部部署資料閘道無法連線到閘道雲端服務，並 Azure 服務匯流排。 閘道有數個通訊設定可供您調整。 如需詳細資訊，請參閱下列主題：

* [調整內部部署資料閘道的通訊設定](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [設定內部部署資料閘道的 Proxy 設定](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性支援

若要避免內部部署資料存取發生單一失敗點，您可以在不同的電腦上安裝多個閘道（僅限標準模式），並將它們設定為叢集或群組。 如此一來，如果主要閘道無法使用，則會將資料要求路由傳送至第二個閘道，依此類推。 因為您只能在一部電腦上安裝一個標準閘道，所以您必須在另一部電腦上安裝叢集中的每個額外閘道。 所有與內部部署資料閘道搭配運作的連接器都支援高可用性。

* 您必須至少有一個閘道安裝與主要閘道相同的 Azure 帳戶，以及該安裝的修復金鑰。

* 主要閘道必須執行 2017 年 11 月或更新版本的閘道更新。

設定主要閘道之後，當您移至 [安裝其他閘道] 時，選取 [**新增至現有的閘道**叢集]，選取主要閘道（即您安裝的第一個閘道），並提供該閘道的修復金鑰。 如需詳細資訊，請參閱[適用於內部部署資料閘道的高可用性叢集](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>變更位置、遷移、還原或取代現有閘道

如果您必須變更閘道的位置、閘道安裝移至新的電腦、復原受損的閘道，或取得現有閘道的擁有權，您會需要在閘道安裝期間所提供的修復金鑰。

1. 在具有現有閘道的電腦上執行閘道安裝程式。 如果您沒有最新的閘道安裝程式，請[下載最新的閘道版本](https://aka.ms/on-premises-data-gateway-installer)。

   > [!NOTE]
   > 在具有原始閘道安裝的電腦上還原閘道之前，您必須先卸載該電腦上的閘道。 此動作會中斷原始閘道的連線。
   > 如果您移除或刪除任何雲端服務的閘道叢集，就無法還原該叢集。

1. 在安裝程式開啟之後，以用來安裝閘道的相同 Azure 帳戶登入。

1. 選取 [**遷移、還原或接管現有的閘道** > **]**，例如：

   ![選取 [遷移、還原或取代現有閘道]](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 選取可用的叢集和閘道，然後輸入所選閘道的修復金鑰，例如：

   ![選取閘道並提供修復金鑰](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. 若要變更區域，請選取 [**變更區域**]，然後選取新的區域。

1. 當您準備好時，請選取 [**設定**]，讓您可以完成工作。

## <a name="tenant-level-administration"></a>租使用者層級管理

若要查看 Azure AD 租使用者中的所有內部部署資料閘道，該租使用者中的全域管理員可以使用租使用者系統管理員身分登入[Power Platform 管理中心](https://powerplatform.microsoft.com)，然後選取 [**資料閘道**] 選項。 如需詳細資訊，請參閱內部[部署資料閘道的租使用者層級管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重新啟動閘道

根據預設，在本機電腦上安裝閘道時，會以名為「內部部署資料閘道服務」的 Windows 服務帳戶的身分執行。 不過，閘道安裝會使用`NT SERVICE\PBIEgwService`其「登入身分」帳號憑證的名稱，並具有「以服務方式登入」許可權。

> [!NOTE]
> 您的 Windows 服務帳戶與用來連線到內部部署資料來源的帳戶不同，以及您登入雲端服務時所使用的 Azure 帳戶。

就像任何其他 Windows 服務一樣，您可以透過各種方式來啟動和停止閘道。 如需詳細資訊，請參閱[重新開機內部部署資料閘道](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>閘道運作方式

您組織中的使用者可以存取已獲授權存取的內部部署資料。 不過，在這些使用者可以連線到您的內部部署資料來源之前，您必須先安裝及設定內部部署資料閘道。 通常，系統管理員是安裝和設定閘道的人員。 這些動作可能需要伺服器管理員許可權或您內部部署伺服器的特殊知識。

閘道有助於加速和更安全的幕後通訊。 這項通訊會在雲端中的使用者、閘道雲端服務和您的內部部署資料來源之間流動。 閘道雲端服務會將資料來源認證和閘道詳細資料予以加密並儲存。 服務也會在使用者、閘道和您的內部部署資料來源之間路由傳送查詢和其結果。

閘道可搭配防火牆運作，而且僅使用輸出連線。 所有流量都是來自閘道代理程式的安全輸出流量。 閘道會透過[Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)，從加密通道上的內部部署來源轉送資料。 此服務匯流排會建立閘道與呼叫服務之間的通道，但不會儲存任何資料。 透過閘道傳送的所有資料都會加密。

![內部部署資料閘道的架構](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> 視雲端服務而定，您可能需要設定閘道的資料來源。

這些步驟說明當您與連線到內部部署資料來源的專案互動時，會發生什麼情況：

1. 雲端服務會建立查詢，以及資料來源的加密認證。 服務接著會將查詢和認證傳送到閘道佇列以進行處理。

1. 閘道雲端服務會分析查詢，並將要求推送至 Azure 服務匯流排。

1. Azure 服務匯流排會將暫止的要求傳送至閘道。

1. 閘道會取得查詢、將認證解密，並使用這些認證連接到一或多個資料來源。

1. 閘道會將查詢傳送至要執行的資料來源。

1. 結果會從資料來源傳送回閘道，然後再到閘道雲端服務。 閘道雲端服務接著就會使用結果。

### <a name="authentication-to-on-premises-data-sources"></a>內部部署資料來源的驗證

預存認證是用來從閘道連接到內部部署資料來源。 無論使用者為何，閘道都會使用預存認證來連接。 特定服務可能會有驗證例外狀況，例如 Power BI 中 Analysis Services 的 DirectQuery 和 LiveConnect。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft 雲端服務會使用[Azure AD](../active-directory/fundamentals/active-directory-whatis.md)來驗證使用者。 Azure AD 租使用者包含使用者名稱和安全性群組。 一般來說，您用來登入的電子郵件地址與您帳戶的使用者主體名稱（UPN）相同。

### <a name="what-is-my-upn"></a>我的 UPN 是什麼？

如果您不是網域系統管理員，您可能不知道您的 UPN。 若要尋找您帳戶的 UPN，請從`whoami /upn`您的工作站執行命令。 雖然結果看起來像是電子郵件地址，但結果會是您本機網域帳戶的 UPN。

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>同步處理內部部署 Active Directory 和 Azure AD

內部部署 Active Directory 帳戶和 Azure AD 帳戶的 UPN 必須相同。 因此，請確定每個內部部署 Active Directory 帳戶都符合您的 Azure AD 帳戶。 雲端服務只知道 Azure AD 內的帳戶。 因此，您不需要將帳戶新增至內部部署 Active Directory。 如果 Azure AD 中的帳戶不存在，您就無法使用該帳戶。

以下是您可以讓內部部署 Active Directory 帳戶與 Azure AD 相符的方式。

* 手動將帳戶新增至 Azure AD。

  在 Azure 入口網站或 Microsoft 365 系統管理中心內建立帳戶。 請確定帳戶名稱符合內部部署 Active Directory 帳戶的 UPN。

* 使用 Azure Active Directory Connect 工具，將本機帳戶同步處理至您的 Azure AD 租使用者。

  Azure AD Connect 工具會提供目錄同步作業和驗證設定的選項。 這些選項包括密碼雜湊同步處理、傳遞驗證和同盟。 如果您不是租使用者管理員或本機網域系統管理員，請洽詢您的 IT 系統管理員以取得 Azure AD Connect 設定。 Azure AD Connect 可確保您的 Azure AD UPN 符合您的本機 Active Directory UPN。 如果您使用 Analysis Services 即時連線搭配 Power BI 或單一登入（SSO）功能，這種比對會很有説明。

  > [!NOTE]
  > 使用 Azure AD Connect 工具同步處理帳戶，會在您的 Azure AD 租使用者中建立新的帳戶。

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>常見問題集和疑難排解

如需詳細資訊，請參閱下列主題：

* [內部部署資料閘道常見問題](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [對內部部署的資料閘道進行疑難排解](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [監視及調整閘道效能](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>後續步驟

* [從邏輯應用程式連接到內部部署資料](../logic-apps/logic-apps-gateway-connection.md)
* [企業整合功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)
