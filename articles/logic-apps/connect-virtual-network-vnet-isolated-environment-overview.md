---
title: 存取 Azure 虛擬網路
description: 關於整合服務環境（Ise）如何協助邏輯應用程式存取 Azure 虛擬網路（Vnet）的總覽
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 07/05/2020
ms.openlocfilehash: 85f4cc9f9e6e762a85571010840cc697bc6c9888
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963660"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源

有時候，您的邏輯應用程式需要存取受保護的資源，例如虛擬機器（Vm），以及內部或連線到[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的其他系統或服務。 若要設定此存取權，您可以[建立*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。 ISE 是 Logic Apps 服務的實例，會使用專用資源，並與「全域」多租使用者 Logic Apps 服務分開執行。

例如，某些 Azure 虛擬網路會使用私人端點（您可以透過[Azure 私人連結](../private-link/private-link-overview.md)來設定），以提供 azure PaaS 服務（例如 Azure 儲存體、Azure Cosmos DB 或 Azure SQL Database、合作夥伴服務，或 azure 上託管的客戶服務）的存取權。 如果您的邏輯應用程式需要存取使用私人端點的虛擬網路，您必須在 ISE 內建立、部署及執行這些邏輯應用程式。

當您建立 ISE 時，Azure 會在您的 Azure 虛擬網路中「插入」** 或部署 ISE。 接著，您可以針對邏輯應用程式和需要存取的整合帳戶，使用此 ISE 作為其位置。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

本總覽提供有關[您為何想要使用 ISE](#benefits)的詳細資訊、[專用和多租使用者 Logic Apps 服務之間的差異](#difference)，以及如何直接存取 Azure 虛擬網路內或連線的資源。

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>為何要使用 ISE？

在您自己的個別專用執行個體中執行邏輯應用程式，有助於減少其他 Azure 租用戶對您應用程式效能可能造成的影響，也就是 ["noisy neighbors" effect](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) (相鄰干擾效應)。 ISE 也提供下列優點：

* 直接存取您的虛擬網路內或連線的資源

  您在 ISE 中建立並執行的邏輯應用程式，可以使用[在 ise 中執行的特別設計連接器](../connectors/apis-list.md#ise-connectors)。 如果內部部署系統或資料來源的 ISE 連接器存在，您可以直接連接，而不需要使用內部[部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)。 如需詳細資訊，請參閱本主題稍後的[專用與多租](#difference)使用者和內部[部署系統的存取權](#on-premises)。

* 繼續存取外部或未連線至虛擬網路的資源

  當 ISE 特定連接器無法使用時，您在 ISE 中建立並執行的邏輯應用程式仍可使用在多租使用者 Logic Apps 服務中執行的連接器。 如需詳細資訊，請參閱[專用與多租](#difference)使用者。

* 您自己的靜態 IP 位址，這與邏輯應用程式在多租用戶服務中所共用的靜態 IP 位址不同。 您也可以設定公用、靜態且可預測的單一輸出 IP 位址，以便與目的地系統進行通訊。 如此一來，您就不需要在每個 ISE 的目的地系統上設定額外的開放式防火牆。

* 增加執行期間、儲存體保留期、輸送量、HTTP 要求和回應逾時、訊息大小及自訂連接器要求的限制。 如需詳細資訊，請參閱 [Azure Logic Apps 的限制和設定](logic-apps-limits-and-config.md)。

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>專用與多租使用者

當您在 ISE 中建立並執行邏輯應用程式時，您會獲得與多租使用者 Logic Apps 服務相同的使用者體驗和類似的功能。 您可以使用多租使用者 Logic Apps 服務中可用的所有相同內建觸發程式、動作和受控連接器。 某些受管理的連接器會提供額外的 ISE 版本。 ISE 連接器和非 ISE 連接器之間的差異存在於其執行所在的位置，以及當您在 ISE 中工作時，它們在邏輯應用程式設計工具中的標籤。

![ISE 中具有和不含標籤的連接器](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 內建的觸發程式和動作（例如 HTTP）會顯示**核心**標籤，並在與邏輯應用程式相同的 ISE 中執行。

* 顯示**ISE**標籤的受控連接器是特別針對 ise 所設計，而且*一律會在與邏輯應用程式相同的 ISE 中執行*。 例如，以下是一些[提供 ISE 版本的連接器](../connectors/apis-list.md#ise-connectors)：<p>

  * Azure Blob 儲存體、檔案儲存體及表格儲存體
  * Azure 服務匯流排，Azure 佇列，Azure 事件中樞
  * Azure 自動化、Azure Key Vault、Azure 事件方格和 Azure 監視器記錄
  * FTP、SFTP-SSH、檔案系統和 SMTP
  * SAP、IBM MQ、IBM DB2 和 IBM 3270
  * SQL Server、Azure SQL 資料倉儲、Azure Cosmos DB
  * AS2、X12 及 EDIFACT

  在罕見的情況下，如果 ISE 連接器適用于內部部署系統或資料來源，您可以直接連線，而不需要使用內部[部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)。 如需詳細資訊，請參閱本主題稍後的[存取內部部署系統](#on-premises)。

* 未顯示**ise**標籤的受控連接器，會繼續在 ISE 內的邏輯應用程式中使用。 這些連接器*一律會在多租使用者 Logic Apps 服務中執行*，而不是在 ISE 中執行。

* 您在*ise 外*建立的自訂連接器，不論它們是否需要內部[部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)，都能繼續在 ise 內的邏輯應用程式中工作。 不過，您在*ISE 中*建立的自訂連接器將無法與內部部署資料閘道搭配使用。 如需詳細資訊，請參閱內部[部署系統的存取權](#on-premises)。

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>內部部署系統的存取權

在 ISE 中執行的邏輯應用程式可以使用下列專案，直接存取內部部署系統和 Azure 虛擬網路內或連接的資料來源：<p>

* HTTP 觸發程式或動作，會顯示**核心**標籤

* 適用于內部部署系統或資料來源的**ISE**連接器（如果有的話）

  如果 ISE 連接器可供使用，您就可以直接存取系統或資料來源，而不需要[內部部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)。 不過，如果您需要從 ISE 存取 SQL Server 並使用 Windows 驗證，則必須使用連接器的非 ISE 版本和內部部署資料閘道。 連接器的 ISE 版本不支援 Windows 驗證。 如需詳細資訊，請參閱[ISE 連接器](../connectors/apis-list.md#ise-connectors)和[從整合服務環境連接](../connectors/apis-list.md#integration-service-environment)。

* 自訂連接器

  * 您在*ise 外*建立的自訂連接器，不論它們是否需要內部[部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)，都能繼續在 ise 內的邏輯應用程式中工作。

  * 您在*ISE 中*建立的自訂連接器無法與內部部署資料閘道搭配使用。 不過，這些連接器可以直接存取內部部署系統，或連線至裝載您 ISE 之虛擬網路的資料來源。 因此，在存取這些資源時，ISE 內的邏輯應用程式通常不需要資料閘道。

若要存取內部部署系統和沒有 ISE 連接器的資料來源、位於虛擬網路之外，或未連線到您的虛擬網路，您仍然必須使用內部部署資料閘道。 ISE 中的邏輯應用程式可以繼續使用沒有**核心**或**ISE**標籤的連接器。 這些連接器只會在多租使用者 Logic Apps 服務中執行，而不是在您的 ISE 中執行。 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

當您建立 ISE 時，可以選取 [開發人員 SKU] 或 [Premium SKU]。 以下是這些 Sku 之間的差異：

* **開發人員**

  提供較低成本的 ISE，供您用來進行實驗、開發和測試，但不能用於生產或效能測試。 開發人員 SKU 包含內建的觸發程式和動作、標準連接器、企業連接器，以及每月固定價格的單一[免費層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶。 不過，此 SKU 不包含任何服務等級協定（SLA）、相應增加容量的選項，或回收期間的冗余，這表示您可能會遇到延遲或停機時間。

* **高級**

  提供您可用於生產環境的 ISE，包括 SLA 支援、內建的觸發程式和動作、標準連接器、企業連接器、單一[標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶、相應增加容量的選項，以及在回收期間以固定的每月價格進行冗余。

> [!IMPORTANT]
> [SKU] 選項僅適用于 ISE 建立，且稍後無法變更。

如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。 若要了解適用於 ISE 的定價和計費方式，請參閱 [Logic Apps 定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 端點存取

當您建立 ISE 時，您可以選擇使用內部或外部存取端點。 您的選擇會決定 ISE 中的邏輯應用程式上的要求或 webhook 觸發程式是否可以接收來自虛擬網路外部的呼叫。 這些端點也會影響您可以從邏輯應用程式的執行歷程記錄存取輸入和輸出的方式。

> [!IMPORTANT]
> 您只能在 ISE 建立期間選取存取端點，而且稍後無法變更此選項。

* **內部**：私人端點允許呼叫您 ISE 中的邏輯應用程式，您可以在其中從邏輯應用程式的執行歷程記錄，*只從虛擬網路內部*查看和存取輸入和輸出。 請確定您在私人端點和您想要存取其執行歷程記錄的電腦之間具有網路連線能力。 例如，您的用戶端電腦可以存在於 ISE 的虛擬網路內，或位於連線到 ISE 虛擬網路的虛擬網路內部，例如透過對等互連或虛擬私人網路。

* **外部**：公用端點允許呼叫您 ISE 中的邏輯應用程式，您可以從*虛擬網路外部*查看並存取邏輯應用程式的執行歷程記錄中的輸入和輸出。 如果您使用網路安全性群組（Nsg），請確定它們已設定輸入規則，以允許存取執行歷程記錄的輸入和輸出。 如需詳細資訊，請參閱[啟用 ISE 的存取權](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)。

若要判斷您的 ISE 是否使用內部或外部存取端點，請在 ISE 功能表的 [**設定**] 底下，選取 [**屬性**]，然後尋找 [**存取端點**] 屬性：

![尋找 ISE 存取端點](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>定價模式

邏輯應用程式、內建觸發程式、內建動作，以及在您 ISE 中執行的連接器會使用與以耗用量為基礎的定價方案不同的固定價格方案。 如需詳細資訊，請參閱[Logic Apps 計價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>使用 ISE 的整合帳戶

您可以使用整合帳戶搭配整合服務環境 (ISE) 內的邏輯應用程式。 不過，這些整合帳戶必須使用「相同的 ISE」** 作為連結的邏輯應用程式。 ISE 中的 Logic Apps 只能參考位於相同 ISE 中的整合帳戶。 當您建立整合帳戶時，您可以選取您的 ISE，作為您整合帳戶的位置。 若要瞭解如何使用 ISE 進行整合帳戶的定價和計費，請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>後續步驟

* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)