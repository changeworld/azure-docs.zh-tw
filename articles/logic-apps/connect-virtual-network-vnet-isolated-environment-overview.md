---
title: 存取 Azure 虛擬網路
description: 關於整合服務環境（Ise）如何協助邏輯應用程式存取 Azure 虛擬網路（Vnet）的總覽
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127258"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源

有時候，您的邏輯應用程式需要存取受保護的資源，例如[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)內的虛擬機器（vm）和其他系統或服務。 若要設定此存取權，您可以[建立*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。 ISE 是 Logic Apps 服務的隔離實例，會使用專用資源，並與「全域」多租使用者 Logic Apps 服務分開執行。

在您自己個別的獨立實例中執行邏輯應用程式有助於降低其他 Azure 租使用者對您應用程式效能的影響，也稱為「有[雜訊的鄰近](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)程度」效果。 ISE 也提供下列優點：

* 您自己的靜態 IP 位址，與多租使用者服務中的邏輯應用程式所共用的靜態 IP 位址不同。 您也可以設定單一的公用、靜態且可預測的輸出 IP 位址，以便與目的地系統進行通訊。 如此一來，您就不需要在每個 ISE 的目的地系統上設定額外的防火牆。

* 增加執行持續時間、儲存體保留期、輸送量、HTTP 要求和回應超時、訊息大小，以及自訂連接器要求的限制。 如需詳細資訊，請參閱[Azure Logic Apps 的限制和](logic-apps-limits-and-config.md)設定。

> [!NOTE]
> 某些 Azure 虛擬網路會使用私人端點（[Azure 私人連結](../private-link/private-link-overview.md)）來提供 azure PaaS 服務（例如 Azure 儲存體、Azure Cosmos DB 或 Azure SQL Database、合作夥伴服務，或 azure 上託管的客戶服務）的存取權。 如果您的邏輯應用程式需要存取使用私人端點的虛擬網路，您必須在 ISE 內建立、部署及執行這些邏輯應用程式。

當您建立 ISE 時，Azure 會將 ISE*插入*或部署至您的 Azure 虛擬網路。 接著，您可以使用此 ISE 作為邏輯應用程式的位置，以及需要存取的整合帳戶。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

邏輯應用程式可以使用這些專案（在與邏輯應用程式相同的 ISE 中執行），來存取虛擬網路內部或連線的資源：

* **核心**標示的內建觸發程式或動作，例如 HTTP 觸發程式或動作
* 適用于該系統或服務的**ISE**標示連接器
* 自訂連接器

您仍然可以在 ISE 中使用沒有**CORE**或**ISE**標籤的連接器與邏輯應用程式。 這些連接器會改為在多租使用者 Logic Apps 服務中執行。 如需詳細資訊，請參閱下列各節：

* [隔離與多租使用者](#difference)
* [從整合服務環境連接](../connectors/apis-list.md#integration-service-environment)
* [ISE 連接器](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> 邏輯應用程式、內建觸發程式、內建動作，以及在您 ISE 中執行的連接器會使用與以耗用量為基礎的定價方案不同的價格方案。 如需詳細資訊，請參閱[Logic Apps 計價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價詳細資料，請參閱[Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

本總覽說明 ISE 如何讓邏輯應用程式直接存取您的 Azure 虛擬網路，並比較 ISE 與多租使用者 Logic Apps 服務之間的差異。

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>隔離與多租使用者

當您在 ISE 中建立並執行邏輯應用程式時，您會獲得與多租使用者 Logic Apps 服務相同的使用者體驗和類似的功能。 您可以使用多租使用者 Logic Apps 服務中可用的所有相同內建觸發程式、動作和受控連接器。 某些受管理的連接器會提供額外的 ISE 版本。 ISE 連接器和非 ISE 連接器之間的差異存在於其執行所在的位置，以及當您在 ISE 中工作時，它們在邏輯應用程式設計工具中的標籤。

![ISE 中具有和不含標籤的連接器](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 內建的觸發程式和動作會顯示 [**核心**] 標籤。 它們一律會在與邏輯應用程式相同的 ISE 中執行。 顯示**ise**標籤的受控連接器也會在與邏輯應用程式相同的 ISE 中執行。

  例如，以下是一些提供 ISE 版本的連接器：

  * Azure Blob 儲存體、檔案儲存體及表格儲存體
  * Azure 佇列、Azure 服務匯流排、Azure 事件中樞和 IBM MQ
  * FTP 和 SFTP-SSH
  * SQL Server、Azure SQL 資料倉儲、Azure Cosmos DB
  * AS2、X12 及 EDIFACT

* 未顯示任何額外標籤的受控連接器，一律會在多租使用者 Logic Apps 服務中執行，但您仍然可以在 ISE 託管的邏輯應用程式中使用這些連接器。

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>內部部署系統的存取權

若要存取內部部署系統或連線到 Azure 虛擬網路的資料來源，ISE 中的邏輯應用程式可以使用下列專案：

* HTTP 動作

* 適用于該系統的 ISE 標示連接器

  > [!NOTE]
  > 若要在[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中搭配使用 Windows 驗證與 SQL Server 連接器，請使用連接器的非 ISE 版本搭配內部[部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。 ISE 標記的版本不支援 Windows 驗證。

* 自訂連接器

  * 如果您有需要內部部署資料閘道的自訂連接器，而且您在 ISE 外建立了這些連接器，ISE 中的邏輯應用程式也可以使用這些連接器。

  * 在 ISE 中建立的自訂連接器無法與內部部署資料閘道搭配使用。 不過，這些連接器可以直接存取連線到裝載 ISE 之虛擬網路的內部部署資料來源。 因此，ISE 中的邏輯應用程式在與這些資源通訊時，很可能不需要資料閘道。

針對未連線到虛擬網路的內部部署系統，或沒有 ISE 標示的連接器，您必須先[設定內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)，邏輯應用程式才能連接到這些系統。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

當您建立 ISE 時，可以選取 [開發人員 SKU] 或 [Premium SKU]。 以下是這些 Sku 之間的差異：

* **開發人員**

  提供較低成本的 ISE，供您用來進行實驗、開發和測試，但不能用於生產或效能測試。 開發人員 SKU 包含內建的觸發程式和動作、標準連接器、企業連接器，以及每月固定價格的單一[免費層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶。 不過，此 SKU 不包含任何服務等級協定（SLA）、相應增加容量的選項，或回收期間的冗余，這表示您可能會遇到延遲或停機時間。

* **高級**

  提供您可用於生產環境的 ISE，包括 SLA 支援、內建的觸發程式和動作、標準連接器、企業連接器、單一[標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶、相應增加容量的選項，以及在回收期間以固定的每月價格進行冗余。

> [!IMPORTANT]
> [SKU] 選項僅適用于 ISE 建立，且稍後無法變更。

如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。 若要瞭解 Ise 的定價和計費方式，請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 端點存取

當您建立 ISE 時，您可以選擇使用內部或外部存取端點。 您的選擇會決定 ISE 中的邏輯應用程式上的要求或 webhook 觸發程式是否可以接收來自虛擬網路外部的呼叫。

這些端點也會影響您可以在邏輯應用程式的執行歷程記錄中存取輸入和輸出的方式。

* **內部**：允許在您的 ISE 中呼叫邏輯應用程式的私用端點，您可以在其中查看及存取邏輯應用程式的輸入，並*只從虛擬網路內*的執行歷程記錄中輸出

* **External**：允許呼叫您 ISE 中邏輯應用程式的公用端點，您可以在其中*從虛擬網路外部*查看並存取邏輯應用程式的輸入和輸出。 如果您使用網路安全性群組（Nsg），請確定它們已設定輸入規則，以允許存取執行歷程記錄的輸入和輸出。 如需詳細資訊，請參閱[啟用 ISE 的存取權](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)。

> [!IMPORTANT]
> [存取端點] 選項僅適用于 ISE 建立，且稍後無法變更。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>使用 ISE 的整合帳戶

您可以使用整合帳戶搭配整合服務環境 (ISE) 內的邏輯應用程式。 不過，這些整合帳戶必須使用「相同的 ISE」作為連結的邏輯應用程式。 ISE 中的 Logic Apps 只能參考位於相同 ISE 中的整合帳戶。 當您建立整合帳戶時，您可以選取您的 ISE，作為您整合帳戶的位置。 若要瞭解如何使用 ISE 進行整合帳戶的定價和計費，請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>後續步驟

* [從 Azure Logic Apps 連接到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
