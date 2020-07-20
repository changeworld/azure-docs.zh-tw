---
title: 商務持續性和災害復原
description: 設計您的策略來保護資料、從干擾性事件快速復原、還原重要商務功能所需的資源，以及維護 Azure Logic Apps 的商務持續性
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80437707"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Azure Logic Apps 的商務持續性和嚴重損壞修復

為了協助降低無法預測的事件對您企業和客戶的影響和影響，請確定您已備妥嚴重損壞[*修復*（DR）](https://en.wikipedia.org/wiki/Disaster_recovery)解決方案，讓您可以保護資料、快速還原支援重要商務功能的資源，並讓作業繼續執行，以維護[*業務持續性*（BC）](https://en.wikipedia.org/wiki/Business_continuity_planning)。 例如，中斷可能包括中斷、基礎結構或元件遺失、儲存、網路或計算資源、無法復原的應用程式失敗，甚至是整個資料中心遺失。 藉由讓商務持續性和嚴重損壞修復（BCDR）解決方案就緒，您的企業或組織可以更快速地回應中斷、規劃或未計畫，並縮短客戶的停機時間。

本文提供當您使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)建立自動化工作流程時，可以套用的 BCDR 指引和策略。 邏輯應用程式工作流程可讓您更輕鬆地在應用程式、雲端服務和內部部署系統之間整合和協調資料，方法是減少您必須撰寫的程式碼數量。 當您規劃 BCDR 時，請務必考慮您的邏輯應用程式，但您也不想要使用與邏輯應用程式搭配的下列 Azure 資源：

* 您從邏輯應用程式建立到其他應用程式、服務和系統的[連接](../connectors/apis-list.md)。 如需詳細資訊，請參閱本主題稍後的[連接到資源](#resource-connections)。

* [內部部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)，這是您在邏輯應用程式中建立及使用的 Azure 資源，以存取內部部署系統中的資料。 每個閘道資源代表本機電腦上的個別[資料閘道安裝](../logic-apps/logic-apps-gateway-install.md)。 如需詳細資訊，請參閱本主題稍後的[內部部署資料閘道](#on-premises-data-gateways)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中定義和儲存邏輯應用程式用於企業[對企業（B2B）企業整合](../logic-apps/logic-apps-enterprise-integration-overview.md)案例的構件。 例如，您可以[為整合帳戶設定跨區域的](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)嚴重損壞修復。

* [整合服務環境（ise）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ，您可以在其中建立邏輯應用程式，以在 Azure 虛擬網路內的隔離 Logic Apps 執行時間實例中執行。 然後，這些邏輯應用程式就可以存取該虛擬網路中防火牆後方受保護的資源。

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>主要和次要位置

每個邏輯應用程式都必須指定您想要用於部署的位置。 此位置是全域多租使用者 Azure 中的公用區域，例如「美國西部」，或是您先前建立並部署到 Azure 虛擬網路的整合服務環境（ISE）。 在 ISE 中執行邏輯應用程式類似于在全球 Azure 區域中執行邏輯應用程式，這表示您的嚴重損壞修復策略適用于這兩種情況。 不過，Ise 有其他考慮，例如設定僅供 Ise 使用的資源存取權。

> [!NOTE]
> 如果您的邏輯應用程式也適用于 B2B 成品，例如交易夥伴、合約、架構、對應，以及儲存在整合帳戶中的憑證，則您的整合帳戶和邏輯應用程式都必須指定相同的位置。

此嚴重損壞修復策略的重點在於設定主要邏輯應用程式，以[*容錯移轉*](https://en.wikipedia.org/wiki/Failover)至替代位置（也可以使用 Azure Logic Apps）中的待命或備份邏輯應用程式。 如此一來，如果主要複本有損失、中斷或失敗，次要複本就會花在工作上。 此策略會要求您的次要邏輯應用程式和相依資源已部署，並可在替代位置中備妥。

如果您遵循良好的 DevOps 做法，您已經使用[Azure Resource Manager 範本](../azure-resource-manager/management/overview.md)來定義和部署邏輯應用程式及其相依資源。 Resource Manager 範本可讓您使用單一部署定義，然後使用參數檔案來提供要用於每個部署目的地的設定值。 這項功能表示您可以將相同的邏輯應用程式部署到不同的環境，例如開發、測試和生產。 您也可以將相同的邏輯應用程式部署到不同的 Azure 區域或 Ise，以支援使用[配對區域](../best-practices-availability-paired-regions.md)的嚴重損壞修復策略。

針對容錯移轉策略，您的邏輯應用程式和位置必須符合下列需求：

* 次要邏輯應用程式實例可以存取與主要邏輯應用程式實例相同的應用程式、服務和系統。

* 這兩個邏輯應用程式實例都有相同的主機類型。 因此，這兩個實例都部署到全域多租使用者 Azure 中的區域，或這兩個實例都部署至 Ise，可讓您的邏輯應用程式直接存取 Azure 虛擬網路中的資源。 如需 BCDR 的配對區域的最佳做法和詳細資訊，請參閱[商務持續性和嚴重損壞修復（BCDR）： Azure 配對的區域](../best-practices-availability-paired-regions.md)。

  例如，當主要邏輯應用程式在 ISE 中執行時，主要和次要位置都必須 Ise，並使用[ise 版本設定連接器](../connectors/apis-list.md#ise-connectors)、HTTP 動作來呼叫 Azure 虛擬網路中的資源，或兩者都是。 在此案例中，次要邏輯應用程式在次要位置中也必須有類似的設定，做為主要邏輯應用程式。

  > [!NOTE]
  > 針對更先進的案例，您可以混合使用多租使用者 Azure 和 ISE 作為位置。 不過，請確定您考慮並瞭解[邏輯應用程式在 ISE 與多租使用者 Azure 中的執行方式之間的差異](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

* 如果您使用 Ise，請[確定它們已相應放大或具有足夠的容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)來處理負載。

#### <a name="example-multi-tenant-azure"></a>範例：多租使用者 Azure

此範例顯示主要和次要邏輯應用程式實例，其部署到全域多租使用者 Azure 中的不同區域，適用于此案例。 單一[Resource Manager 範本](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)會定義邏輯應用程式實例，以及這些邏輯應用程式所需的相依資源。 個別的參數檔案會指定要用於每個部署位置的設定值：

![不同位置中的主要和次要邏輯應用程式實例](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>範例：整合服務環境

這個範例會顯示先前的主要和次要邏輯應用程式實例，但部署到個別的 Ise。 單一 Resource Manager 範本會定義邏輯應用程式實例、這些邏輯應用程式所需的相依資源，以及 Ise 作為部署位置。 個別的參數檔案會定義要用於每個位置之部署的設定值：

![不同位置中的主要和次要邏輯應用程式](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>資源的連接

Azure Logic Apps 提供內建的觸發程式和動作，以及您的邏輯應用程式可用來處理其他應用程式、服務、系統和其他資源（例如 Azure 儲存體帳戶、SQL Server 資料庫、Office 365 Outlook 電子郵件帳戶等）的[數百個受管理連接器](../connectors/apis-list.md)。 如果您的邏輯應用程式需要存取這些資源，您可以建立連線以驗證這些資源的存取權。 每個連線都是個別的 Azure 資源，存在於特定位置，而且無法供其他位置的資源使用。

針對您的嚴重損壞修復策略，請考慮相依資源相對於邏輯應用程式實例的位置：

* 您的主要實例和相依資源存在於不同的位置。 在此情況下，您的次要實例可以連接到相同的相依資源或端點。 不過，您應該特別為次要實例建立連接。 如此一來，如果您的主要位置變得無法使用，您的次要連線不會受到影響。

  例如，假設您的主要邏輯應用程式連接到外部服務，例如 Salesforce。 一般而言，外部服務的可用性和位置與邏輯應用程式的可用性無關。 在此情況下，您的次要實例可以連接到相同的服務，但應該要有自己的連接。

* 您的主要實例和相依資源都存在於相同的位置。 在此情況下，相依資源的備份或複寫版本應位於不同的位置，讓您的次要實例仍然可以存取這些資源。

  例如，假設您的主要邏輯應用程式連接到位於相同位置或區域中的服務，例如 Azure SQL Database。 如果整個區域變得無法使用，該區域中的 Azure SQL Database 服務也可能無法使用。 在此情況下，您會想要讓次要實例使用複寫或備份資料庫，以及與該資料庫的個別連接。

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>內部部署資料閘道

如果您的邏輯應用程式在多租使用者 Azure 中執行，而且需要存取內部部署資源（例如 SQL Server 資料庫），您就必須在本機電腦上安裝內部[部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。 接著，您可以在 Azure 入口網站中建立資料閘道資源，讓邏輯應用程式可以在建立資源連線時使用閘道。

資料閘道資源會與位置或 Azure 區域相關聯，就像您的邏輯應用程式資源一樣。 在您的嚴重損壞修復策略中，請確定您的邏輯應用程式仍可使用資料閘道。 當您有多個閘道安裝時，您可以[為閘道啟用高可用性](../logic-apps/logic-apps-gateway-install.md#high-availability)。

> [!NOTE]
> 如果您的邏輯應用程式在整合服務環境（ISE）中執行，且僅針對內部部署資料來源使用 ISE 版本設定的連接器，則您不需要資料閘道，因為 ISE 連接器會提供內部部署資源的直接存取權。
>
> 如果您想要的內部部署資源沒有可使用 ISE 版本設定的連接器，則邏輯應用程式仍可使用非 ISE 連接器來建立連線，而該連接器會在全域多租使用者 Azure 中執行，而不是您的 ISE。 不過，此連接需要內部部署資料閘道。

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>主動-主動與主動-被動角色

您可以設定您的主要和次要位置，讓這些位置中的邏輯應用程式實例可以扮演下列角色：

| 主要-次要角色 | 描述 |
|------------------------|-------------|
| *主動-主動* | 這兩個位置中的主要和次要邏輯應用程式實例會遵循下列其中一種模式，主動處理要求： <p><p>- *負載平衡*：您可以讓兩個實例接聽端點，並視需要將流量負載平衡到每個實例。 <p>- *競爭取用者*：您可以讓兩個實例作為競爭取用者，讓實例競爭佇列中的訊息。 如果一個實例失敗，另一個實例會接管工作負載。 |
| *主動-被動* | 主要邏輯應用程式實例會主動處理整個工作負載，而次要實例則為被動（已停用或非使用中）。 次要會等候主要無法使用或因為中斷或失敗而無法運作的信號，並將工作負載視為作用中的實例。 |
| 合併 | 有些邏輯應用程式會扮演主動-主動角色，而其他邏輯應用程式則扮演主動-被動角色。 |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>主動-主動範例

這些範例會顯示主動-主動設定，其中兩個邏輯應用程式實例會主動處理要求或訊息。 有些其他系統或服務會在實例之間散發要求或訊息，例如下列其中一個選項：

* 「實體」負載平衡器，例如路由傳送流量的一段硬體

* 「軟性」負載平衡器，例如[Azure Load Balancer](../load-balancer/load-balancer-overview.md)或[Azure API 管理](../api-management/api-management-key-concepts.md)。 透過 API 管理，您可以指定原則來決定如何對傳入流量進行負載平衡。 或者，您可以使用支援狀態追蹤的服務，例如[Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)。

  雖然此範例主要顯示 Azure Load Balancer，但您可以使用最適合您案例需求的選項：

  ![使用負載平衡器或具狀態服務的「主動-主動」安裝程式](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* 每個邏輯應用程式實例會作為取用者，並讓兩個實例與佇列中的訊息競爭：

  ![使用「競爭取用者」的「主動-主動」安裝程式](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>主動-被動範例

這個範例會顯示在一個位置中，主要邏輯應用程式實例在使用中的主動-被動設定，而次要實例則會在另一個位置維持非作用中狀態。 如果主要發生中斷或失敗，您可以讓操作員執行腳本來啟動次要資料庫來執行工作負載。

![使用「競爭取用者」的「主動-被動」安裝程式](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>結合主動-主動和主動-被動

這個範例會顯示主要位置同時具有作用中邏輯應用程式實例的結合設定，而次要位置則具有主動-被動邏輯應用程式實例。 如果主要位置發生中斷或失敗，次要位置（已處理部分工作負載）中的作用中邏輯應用程式可以接管整個工作負載。

* 在主要位置中，作用中的邏輯應用程式會接聽訊息的 Azure 服務匯流排佇列，而另一個作用中的邏輯應用程式會使用 Office 365 Outlook 輪詢觸發程式來檢查電子郵件。

* 在次要位置中，作用中的邏輯應用程式會在主要位置中使用邏輯應用程式，方法是接聽並爭用來自相同服務匯流排佇列的訊息。 同時，被動非作用中的邏輯應用程式會在主要位置變成無法使用但已*停*用以避免重新讀取電子郵件時，等待待命來檢查電子郵件。

![使用週期觸發程式的「主動-被動」和「主動-被動」組合](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>邏輯應用程式狀態和歷程記錄

當您的邏輯應用程式觸發並開始執行時，應用程式的狀態會儲存在應用程式啟動所在的相同位置，而且無法轉移至另一個位置。 如果發生失敗或中斷，則會放棄任何進行中的工作流程實例。 當您設定主要和次要位置時，新的工作流程實例就會開始在次要位置執行。

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>減少已放棄的進行中實例

若要將已放棄的進行中工作流程實例數目降至最低，您可以從各種可執行檔訊息模式中選擇，例如：

* [固定路由傳送模式](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  這種企業訊息模式會將商務程式分割成較小的階段。 針對每個階段，您可以設定邏輯應用程式來處理該階段的工作負載。 若要彼此通訊，您的邏輯應用程式會使用非同步訊息通訊協定，例如 Azure 服務匯流排的佇列或主題。 當您將程式分成較小的階段時，您可以減少可能會停滯在失敗的邏輯應用程式實例上的商務進程數目。 如需此模式的一般資訊，請參閱[企業整合模式-路由單](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)。

  這個範例會顯示一個路由條模式，其中每個邏輯應用程式都代表一個階段，並使用服務匯流排的佇列與進程中的下一個邏輯應用程式進行通訊。

  ![將商務程式分割成邏輯應用程式所代表的階段，使用 Azure 服務匯流排的佇列彼此通訊](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  如果主要和次要邏輯應用程式實例在其位置遵循相同的路由傳送模式，您可以為這些實例設定[主動-主動角色](#roles)，以執行[競爭取用者模式](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers)。

* [進程管理員（broker）模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [不含 timeout 模式的查看鎖定](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>觸發程式和執行歷程記錄的存取權

若要取得邏輯應用程式過去工作流程執行的詳細資訊，您可以查看應用程式的觸發程式並執行歷程記錄。 邏輯應用程式的歷程記錄執行歷程記錄會儲存在該邏輯應用程式執行所在的相同位置或區域中，這表示您無法將此歷程記錄遷移至不同的位置。 如果您的主要實例故障切換到次要實例，您只能存取每個實例的觸發程式，並在這些實例執行所在的個別位置中執行記錄。 不過，您可以藉由設定邏輯應用程式將診斷事件傳送至 Azure Log Analytics 工作區，來取得邏輯應用程式歷程記錄的無關位置資訊。 接著，您可以跨多個位置執行的邏輯應用程式，檢查其健康情況和歷程記錄。

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>觸發程式類型指引

您在邏輯應用程式中使用的觸發程式類型會決定您可以如何在嚴重損壞修復策略中跨位置設定邏輯應用程式的選項。 以下是您可以在邏輯應用程式中使用的可用觸發程式類型：

* [循環觸發程序](#recurrence-trigger)
* [輪詢觸發程序](#polling-trigger)
* [要求觸發程序](#request-trigger)
* [Webhook 觸發程序](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>循環觸發程序

「**週期**」觸發程式與任何特定服務或端點無關，而且只會根據指定的排程引發，而不是其他準則，例如：

* 固定頻率和間隔，例如每10分鐘
* 更先進的排程，例如每個月的最後一個星期一下午5:00

當您的邏輯應用程式以迴圈觸發程式開始時，您必須設定具有[主動-被動角色](#roles)的主要和次要邏輯應用程式實例。 若要減少復原*時間目標*（RTO），這是指在中斷或嚴重損壞之後還原商務程式的目標持續時間，您可以使用[主動-被動角色](#roles)和[被動-主動角色](#roles)的組合來設定邏輯應用程式實例。 在此設定中，您會將排程分割成不同的位置。

例如，假設您有一個需要每隔10分鐘執行一次的邏輯應用程式。 您可以設定您的邏輯應用程式和位置，如此一來，如果主要位置變得無法使用，次要位置就可以接管工作：

![使用週期觸發程式的「主動-被動」和「被動-主動」組合](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* 在主要位置中，為這些邏輯應用程式設定[主動-被動角色](#roles)：

  * 針對作用*中的邏輯*應用程式，將 [週期] 觸發程式設定為在該小時的最上方開始，並每隔20分鐘重複一次，例如 9:00 AM、9:20 am 等等。

  * 針對*被動*停用的邏輯應用程式，將迴圈觸發程式設定為相同的排程，但在該小時的10分鐘開始，並每隔20分鐘重複一次，例如 9:10 AM、9:30 AM 等等。

* 在次要位置中，為這些邏輯應用程式設定[被動-主動](#roles)：

  * 針對*被動*停用的邏輯應用程式，將「週期」觸發程式設為與主要位置中作用中邏輯應用程式相同的排程（在小時的頂端），並每隔20分鐘重複一次，例如 9:00 AM、9:10 AM 等等。

  * 針對作用*中啟用的*邏輯應用程式，請將 [週期] 觸發程式設為與主要位置中的被動邏輯應用程式相同的排程，也就是在一小時的10分鐘開始，並每隔20分鐘重複一次，例如 9:10 AM、9:20 AM 等等。

現在，如果主要位置發生干擾性事件，請在替代位置中啟用被動邏輯應用程式。 如此一來，如果找出失敗的時間，則此設定會限制在該延遲期間遺漏的重複次數。

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>輪詢觸發程序

若要定期檢查是否有新的資料可從特定服務或端點取得，您的邏輯應用程式可以使用*輪詢*觸發程式，根據固定的週期排程重複呼叫服務或端點。 服務或端點所提供的資料可以有下列其中一種類型：

* 靜態資料，描述永遠可供讀取的資料
* 變動性資料，其中描述讀取後已無法再使用的資料

為避免重複讀取相同的資料，您的邏輯應用程式必須記住先前已讀取的資料，其方式是在用戶端或伺服器、服務或系統端維護狀態。

* 可處理用戶端狀態的邏輯應用程式會使用觸發程式來維護狀態。

  例如，從電子郵件收件匣讀取新訊息的觸發程式，會要求觸發程式記住最近讀取的訊息。 如此一來，只有在下一個未讀取的訊息抵達時，觸發程式才會啟動邏輯應用程式。

* 適用于伺服器、服務或系統端狀態的邏輯應用程式會使用伺服器、服務或系統端上的屬性值或設定。

  例如，以查詢為基礎的觸發程式，它會從資料庫讀取資料列時，必須有 `isRead` 設定為的資料行 `FALSE` 。 每次觸發程式讀取資料列時，邏輯應用程式會將資料行從變更為，藉以更新該資料 `isRead` 列 `FALSE` `TRUE` 。

  這種伺服器端方法的運作方式類似于具有佇列語義的服務匯流排佇列或主題，而當邏輯應用程式處理訊息時，觸發程式可以讀取和鎖定訊息。 當邏輯應用程式完成處理時，觸發程式會從佇列或主題中刪除訊息。

從嚴重損壞修復的觀點來看，當您設定邏輯應用程式的主要和次要實例時，請根據您的邏輯應用程式是否會追蹤用戶端或伺服器端上的狀態，來確定您會考慮這些行為：

* 針對使用用戶端狀態的邏輯應用程式，請確定您的邏輯應用程式不會多次讀取相同的訊息。 在任何特定時間，只有一個位置可以有作用中的邏輯應用程式實例。 請確定替代位置中的邏輯應用程式實例為非作用中或已停用，直到主要實例故障切換至替代位置為止。

  例如，Office 365 Outlook 觸發程式會維護用戶端狀態，並追蹤最近讀取之電子郵件的時間戳記，以避免讀取重複的。

* 對於使用伺服器端狀態的邏輯應用程式，您可以設定邏輯應用程式實例來播放[主動-主動角色](#roles)，其中會以競爭取用者或[主動-被動角色](#roles)的方式運作，其中替代實例會等到主要實例故障切換至替代位置為止。

  例如，從訊息佇列讀取（例如 Azure 服務匯流排佇列）時，會使用伺服器端狀態，因為佇列服務會維護訊息的鎖定，以防止其他用戶端讀取相同的訊息。

  > [!NOTE]
  > 如果您的邏輯應用程式需要以特定順序（例如，從服務匯流排的佇列讀取訊息），您可以使用競爭取用者模式，但只有在結合服務匯流排會話（也稱為[*連續*群組模式](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)）時。 否則，您必須使用主動-被動角色來設定邏輯應用程式實例。

<a name="request-trigger"></a>

### <a name="request-trigger"></a>要求觸發程序

**要求**觸發程式可讓您的邏輯應用程式從其他應用程式、服務和系統呼叫，而且通常用來提供這些功能：

* 可供其他人呼叫的邏輯應用程式直接 REST API

  例如，使用要求觸發程式來啟動邏輯應用程式，讓其他邏輯應用程式可以使用 [**呼叫工作流程-Logic Apps** ] 動作來呼叫觸發程式。

* 適用于邏輯應用程式的[webhook](#webhook-trigger)或回呼機制

* 您可以手動執行使用者作業或常式以呼叫邏輯應用程式的方式，例如，使用執行特定工作的 PowerShell 腳本

從嚴重損壞修復的觀點來看，要求觸發程式是被動接收者，因為邏輯應用程式不會執行任何工作，而且會等到其他服務或系統明確呼叫觸發程式。 身為被動端點，您可以透過下列方式設定主要和次要實例：

* [主動-主動](#roles)：兩個實例會主動處理要求或呼叫。 呼叫端或路由器會平衡或分配這些實例之間的流量。

* [主動-被動](#roles)：只有主要實例是作用中的，而且會處理所有工作，而次要實例則會等到主要體驗中斷或失敗為止。 呼叫端或路由器會決定呼叫次要實例的時機。

作為建議的架構，您可以使用 Azure API 管理作為使用要求觸發程式之邏輯應用程式的 proxy。 API 管理提供[內建的跨區域復原功能，以及跨多個端點路由傳送流量的能力](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)。

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook 觸發程序

*Webhook*觸發程式可讓您的邏輯應用程式將*回呼 URL*傳遞給該服務，以訂閱服務。 然後，您的邏輯應用程式可以接聽並等候特定事件發生在該服務端點。 當事件發生時，服務會使用回呼 URL 呼叫 webhook 觸發程式，然後再執行邏輯應用程式。 啟用時，webhook 觸發程式會訂閱服務。 停用時，觸發程式會從服務取消訂閱。

從嚴重損壞修復的觀點來看，設定使用 webhook 觸發程式來播放主動-被動角色的主要和次要實例，因為只有一個實例應該會從已訂閱的端點接收事件或訊息。

## <a name="assess-primary-instance-health"></a>評估主要實例健全狀況

為了讓您的嚴重損壞修復策略可行，您的解決方案需要執行下列工作的方法：

* [檢查主要實例的可用性](#check-primary-availability)
* [監視主要實例的健全狀況](#monitor-primary-health)
* [啟動次要實例](#activate-secondary)

本節說明您可以直接使用的一個解決方案，或做為您自己設計的基礎。 以下是此解決方案的高階視覺化總覽：

![建立監視主要位置中健康情況檢查邏輯應用程式的監看式邏輯應用程式](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>檢查主要實例可用性

若要判斷主要實例是否可供使用、執行，以及是否能夠運作，您可以建立與主要實例位於相同位置的「健康情況檢查」邏輯應用程式。 然後，您可以從替代位置呼叫此健康情況檢查應用程式。 如果健康情況檢查應用程式成功回應，則該區域中 Azure Logic Apps 服務的基礎結構可供使用且運作正常。 如果健康情況檢查應用程式無法回應，您可以假設該位置已不再狀況良好。

針對這項工作，請建立基本的健全狀況檢查邏輯應用程式，以執行下列工作：

1. 使用要求觸發程式，接收來自看門狗應用程式的呼叫。

1. 以狀態回應，指出已檢查的邏輯應用程式是否仍然可以使用 [回應] 動作運作。

   > [!IMPORTANT]
   > 健康情況檢查邏輯應用程式必須使用回應動作，應用程式才會以同步方式回應，而不是以非同步方式回應。

1. （選擇性）若要進一步判斷主要位置是否狀況良好，您可以在此位置中納入與目標邏輯應用程式互動之任何其他服務的健全狀況。 只要展開健全狀況檢查邏輯應用程式，也可以評估這些其他服務的健康情況。

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>建立看門狗邏輯應用程式

若要監視主要實例的健全狀況，並呼叫健全狀況檢查邏輯應用程式，請在*替代位置*中建立「監看器」邏輯應用程式。 例如，您可以設定看門狗邏輯應用程式，如此一來，如果呼叫健全狀況檢查邏輯失敗，看門者就可以傳送警示給您的作業小組，讓他們可以調查失敗，以及主要實例沒有回應的原因。

> [!IMPORTANT]
> 請確定您的看門狗邏輯應用程式位於與*主要位置不同的位置*。 如果主要位置中的 Logic Apps 服務發生問題，您的看門狗邏輯應用程式可能無法執行。

針對此工作，在次要位置中，建立可執行下列工作的看門狗邏輯應用程式：

1. 使用 [週期] 觸發程式，根據固定或排程的週期執行。

   您可以將「週期」設定為低於「復原時間目標」（RTO）容錯層級的值。

1. 使用 HTTP 動作呼叫主要位置中的健康情況檢查邏輯應用程式，例如：

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>啟用您的次要實例

若要自動啟動次要實例，您可以建立一個邏輯應用程式來呼叫管理 API （例如[Azure Resource Manager 連接器](https://docs.microsoft.com/connectors/arm/)），以在次要位置啟動適當的邏輯應用程式。 您可以擴充您的看門狗應用程式，以在發生特定數目的失敗後呼叫此啟用邏輯應用程式。

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>收集診斷資料

您可以設定邏輯應用程式執行的記錄，並將產生的診斷資料傳送至服務，例如 Azure 儲存體、Azure 事件中樞和 Azure Log Analytics，以進行進一步的處理和處理。

* 如果您想要將此資料與 Azure Log Analytics 搭配使用，您可以設定邏輯應用程式的**診斷設定**，並將資料傳送至多個 Log analytics 工作區，以將資料提供給主要和次要位置。 如需詳細資訊，請參閱[設定 Azure 監視器記錄和收集 Azure Logic Apps 的診斷資料](../logic-apps/monitor-logic-apps-log-analytics.md)。

* 如果您想要將資料傳送至 Azure 儲存體或 Azure 事件中樞，可以藉由設定異地複寫，讓主要和次要位置的資料都可供使用。 如需詳細資訊，請參閱下列文章：<p>

  * [Azure Blob 儲存體嚴重損壞修復和帳戶容錯移轉](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure 事件中樞異地嚴重損壞修復](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>後續步驟

* [Azure 的復原功能總覽](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [特定 Azure 服務的復原檢查清單](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Azure 中的復原資料管理](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Azure 應用程式的備份和嚴重損壞修復](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [從全區域服務中斷復原](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [適用于 Azure 服務的 Microsoft 服務等級協定（Sla）](https://azure.microsoft.com/support/legal/sla/)
