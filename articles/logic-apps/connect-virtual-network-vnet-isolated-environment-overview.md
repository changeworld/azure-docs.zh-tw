---
title: 訪問 Azure 虛擬網路
description: 整合服務環境 （ISE） 如何説明邏輯應用訪問 Azure 虛擬網路 （VNET） 的概述
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127258"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源

有時，邏輯應用需要訪問[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)內的安全資源，如虛擬機器 （VM） 和其他系統或服務。 要設置此訪問，您可以創建[*整合服務環境*（ISE）。](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ISE 是邏輯應用服務的一個孤立實例，它使用專用資源，與"全域"多租戶邏輯應用服務分開運行。

在您自己的獨立實例中運行邏輯應用有助於減少其他 Azure 租戶可能對應用性能的影響，也稱為["嘈雜鄰居"效果](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。 ISE 還提供以下優勢：

* 您自己的靜態 IP 位址，這些位址與多租戶服務中的邏輯應用共用的靜態 IP 位址分開。 您還可以設置單個公共、靜態和可預測的出站 IP 位址，以便與目標系統通信。 這樣，您就不必為每個 ISE 在這些目標系統設置額外的防火牆打開。

* 增加了對運行持續時間、存儲保留、輸送量、HTTP 要求和回應超時、消息大小和自訂連接器請求的限制。 有關詳細資訊，請參閱[Azure 邏輯應用的限制和配置](logic-apps-limits-and-config.md)。

> [!NOTE]
> 某些 Azure 虛擬網路使用專用終結點[（Azure 專用連結](../private-link/private-link-overview.md)） 來提供對 Azure PaaS 服務（如 Azure 存儲、Azure 宇宙資料庫或 Azure SQL 資料庫、合作夥伴服務或在 Azure 上託管的客戶服務）的訪問。 如果邏輯應用需要訪問使用專用終結點的虛擬網路，則必須在 ISE 中創建、部署和運行這些邏輯應用。

創建 ISE 時，Azure*會將*該 ISE 注入或部署到 Azure 虛擬網路。 然後，可以使用此 ISE 作為需要訪問的邏輯應用和集成帳戶的位置。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

邏輯應用可以使用這些專案訪問虛擬網路內部或連接到虛擬網路的資源，這些專案在與邏輯應用相同的 ISE 中運行：

* **CORE**- 標記的內置觸發器或操作，如 HTTP 觸發器或操作
* 該系統或服務的**ISE**標記連接器
* 自訂連接器

您仍可以使用沒有**CORE**或**ISE**標籤的連接器與 ISE 中的邏輯應用一起。 這些連接器在多租戶邏輯應用服務中運行。 有關詳細資訊，請參閱以下部分：

* [隔離與多租戶](#difference)
* [從整合服務環境進行連接](../connectors/apis-list.md#integration-service-environment)
* [ISE 連接器](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> 在 ISE 中運行的邏輯應用、內置觸發器、內置操作和連接器使用不同于基於消耗的定價計畫的定價計畫。 有關詳細資訊，請參閱[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有關定價詳細資訊，請參閱[邏輯應用定價](../logic-apps/logic-apps-pricing.md)。

本概述介紹了有關 ISE 如何使邏輯應用直接存取 Azure 虛擬網路以及比較 ISE 和多租戶邏輯應用服務之間的差異的詳細資訊。

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>隔離與多租戶

在 ISE 中創建和運行邏輯應用時，您將獲得與多租戶邏輯應用服務相同的使用者體驗和類似功能。 您可以使用多租戶邏輯應用服務中提供的所有相同的內置觸發器、操作和託管連接器。 某些託管連接器提供其他 ISE 版本。 ISE 連接器和非 ISE 連接器之間的差異存在於它們運行的位置和在 ISE 中工作時邏輯應用設計器中的標籤。

![ISE 中帶標籤和沒有標籤的連接器](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 內置觸發器和操作顯示**CORE**標籤。 它們始終與邏輯應用在同一 ISE 中運行。 顯示**ISE**標籤的託管連接器也運行在與邏輯應用相同的 ISE 中。

  例如，以下是一些提供 ISE 版本的連接器：

  * Azure Blob 儲存體、檔案儲存體及表格儲存體
  * Azure 佇列、Azure 服務匯流排、Azure 事件中樞和 IBM MQ
  * FTP 和 SFTP-SSH
  * SQL 伺服器、Azure SQL 資料倉儲、Azure 宇宙資料庫
  * AS2、X12 及 EDIFACT

* 不顯示任何其他標籤的託管連接器始終在多租戶邏輯應用服務中運行，但仍可以在 ISE 託管的邏輯應用中使用這些連接器。

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>訪問本地系統

要訪問連接到 Azure 虛擬網路的本地系統或資料來源，ISE 中的邏輯應用可以使用以下項：

* HTTP 動作

* 該系統的 ISE 標記連接器

  > [!NOTE]
  > 要在[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中使用 SQL Server 連接器的 Windows 身份驗證，請使用連接器的非 ISE 版本與[本地資料閘道](../logic-apps/logic-apps-gateway-install.md)。 ISE 標記的版本不支援 Windows 身份驗證。

* 自訂連接器

  * 如果您有需要本地資料閘道的自訂連接器，並且在 ISE 之外創建了這些連接器，則 ISE 中的邏輯應用也可以使用這些連接器。

  * 在 ISE 中創建的自訂連接器與本地資料閘道不起作用。 但是，這些連接器可以直接存取連接到託管 ISE 的虛擬網路的本地資料來源。 因此，在與這些資源通信時，ISE 中的邏輯應用很可能不需要資料閘道。

對於未連接到虛擬網路或沒有 ISE 標記連接器的本地系統，必須先[設置本地資料閘道](../logic-apps/logic-apps-gateway-install.md)，然後才能將邏輯應用連接到這些系統。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

創建 ISE 時，可以選擇開發人員 SKU 或高級 SKU。 以下是這些 SKU 之間的差異：

* **開發 人員**

  提供低成本的 ISE，可用於實驗、開發和測試，但不能用於生產或效能測試。 開發人員 SKU 包括內置觸發器和操作、標準連接器、企業連接器和單個[免費層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帳戶，每月固定價格。 但是，此 SKU 不包括任何服務等級協定 （SLA）、擴展容量的選項或回收過程中的冗余，這意味著您可能會遇到延遲或停機。

* **溢價**

  提供可用於生產的 ISE，包括 SLA 支援、內置觸發器和操作、標準連接器、企業連接器、單個[標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帳戶、擴展容量的選項以及回收過程中的冗余，按固定月價進行。

> [!IMPORTANT]
> SKU 選項僅在 ISE 創建時可用，以後無法更改。

有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps/)。 要瞭解 ISEs 的定價和計費的工作原理，請參閱[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 端點訪問

創建 ISE 時，可以選擇使用內部或外部訪問終結點。 您的選擇確定 ISE 中邏輯應用上的請求或 Webhook 觸發器是否可以從虛擬網路外部接收呼叫。

這些終結點還會影響訪問邏輯應用執行歷程記錄中的輸入和輸出的方式。

* **內部**：允許調用 ISE 中的邏輯應用的專用終結點，您可以在其中*僅從虛擬網路內部*查看和訪問執行歷程記錄中的邏輯應用的輸入和輸出

* **外部**：允許調用 ISE 中的邏輯應用的公共終結點，您可以在其中*從虛擬網路外部*查看和訪問邏輯應用在執行歷程記錄中的輸入和輸出。 如果使用網路安全性群組 （NSG），請確保它們已設置入站規則，以允許訪問執行歷程記錄的輸入和輸出。 有關詳細資訊，請參閱啟用[ISE 的訪問](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)。

> [!IMPORTANT]
> 訪問終結點選項僅在 ISE 創建時可用，以後無法更改。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>使用 ISE 的整合帳戶

您可以使用整合帳戶搭配整合服務環境 (ISE) 內的邏輯應用程式。 不過，這些整合帳戶必須使用「相同的 ISE」** 作為連結的邏輯應用程式。 ISE 中的 Logic Apps 只能參考位於相同 ISE 中的整合帳戶。 當您建立整合帳戶時，您可以選取您的 ISE，作為您整合帳戶的位置。 要瞭解使用 ISE 的集成帳戶的定價和計費的工作原理，請參閱[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>後續步驟

* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
