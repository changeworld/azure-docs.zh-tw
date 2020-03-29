---
title: 在內部訪問資料來源
description: 通過創建 Azure 本地資料閘道資源，連接到 Azure 邏輯應用的本地資料來源
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 29c1aaf18ea45d869d32a8817aeb03faa3b67c32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456566"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>從 Azure Logic Apps 連線到內部部署資料來源

在可以從邏輯應用訪問內部資料來源之前，需要在[本地電腦上安裝*本地資料閘道*](../logic-apps/logic-apps-gateway-install.md)後創建 Azure 資源。 然後，邏輯應用在可用於 Azure 邏輯應用[的本地連接器](../connectors/apis-list.md#on-premises-connectors)提供的觸發器和操作中使用此 Azure 閘道資源。

本文演示如何為[本地電腦上以前安裝的閘道](../logic-apps/logic-apps-gateway-install.md)創建 Azure 閘道資源。 有關閘道的詳細資訊，請參閱[閘道的工作原理](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)。

> [!TIP]
> 若要連線至 Azure 虛擬網路，請考慮建立[*整合服務環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 

如需如何使用閘道與其他服務的資訊，請參閱下列文章：

* [微軟電源自動本地資料閘道](/power-automate/gateway-reference)
* [Microsoft Power BI 內部部署資料閘道](/power-bi/service-gateway-onprem)
* [微軟電源應用本地資料閘道](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支援的資料來源

在 Azure 邏輯應用中，本地資料閘道支援這些資料來源[的本地連接器](../connectors/apis-list.md#on-premises-connectors)：

* BizTalk Server 2016
* 檔案系統
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle 資料庫
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure 邏輯應用支援通過資料閘道執行讀取和寫入操作。 但是，這些操作[的有效載荷大小有限制](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。 儘管閘道本身不會產生額外費用，[但邏輯應用定價模型](../logic-apps/logic-apps-pricing.md)適用于 Azure 邏輯應用中的這些連接器和其他操作。

## <a name="prerequisites"></a>Prerequisites

* 您已經在[本地電腦上安裝了本地資料閘道](../logic-apps/logic-apps-gateway-install.md)。

* 您使用的是安裝資料閘道時使用[的相同 Azure 帳戶和訂閱](../logic-apps/logic-apps-gateway-install.md#requirements)。 此 Azure 帳戶必須屬於單個[Azure 活動目錄 （Azure AD） 租戶或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。

* 閘道安裝尚未由其他 Azure 閘道資源註冊並聲明。

  在 Azure 門戶中創建閘道資源時，請選擇閘道安裝，該安裝連結到閘道資源，並且僅連結到該閘道資源。 在 Azure 邏輯應用中，本地觸發器和操作使用閘道資源連接到本地資料來源。 在這些觸發器和操作中，選擇 Azure 訂閱和要使用的關聯閘道資源。 每個閘道資源僅連結到一個閘道安裝，該安裝僅連結到一個 Azure 帳戶。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>創建 Azure 閘道資源

在本地電腦上安裝閘道後，請為閘道創建 Azure 資源。

1. 使用用於安裝閘道的相同 Azure 帳戶登錄到[Azure 門戶](https://portal.azure.com)。

1. 在 Azure 門戶搜索框中，輸入"本地資料閘道"，然後選擇**本地資料閘道**。

   ![尋找 [內部部署資料閘道]](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **在"本地資料閘道**"下，選擇 **"添加**"。

   ![為數據閘道添加新的 Azure 資源](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. 在 **"創建連接閘道**"下，為閘道資源提供此資訊。 當您完成時，選取 [建立]****。

   | 屬性 | 描述 |
   |----------|-------------|
   | **資源名稱** | 為僅包含字母、數位、連字號`-`（）、底線 （`_`）、括弧`(``)`（） 或句點 （`.`） 的閘道資源提供名稱 。 |
   | **訂閱** | 為用於閘道安裝的 Azure 帳戶選擇 Azure 訂閱。 預設的訂用帳戶會由您用來登入的 Azure 帳戶來決定。 |
   | **資源組** | 要使用的[Azure 資源組](../azure-resource-manager/management/overview.md) |
   | **位置** | 在[閘道安裝](../logic-apps/logic-apps-gateway-install.md)期間為閘道雲服務選擇的同一區域或位置。 否則，閘道安裝將不會顯示在 **"安裝名稱"** 清單中。 邏輯應用位置可能與閘道資源位置不同。 |
   | **安裝名稱** | 選擇閘道安裝，僅當滿足這些條件時，該安裝才會顯示在清單中： <p><p>- 閘道安裝使用與要創建的閘道資源相同的區域。 <br>- 閘道安裝未連結到其他 Azure 閘道資源。 <br>- 閘道安裝連結到用於創建閘道資源的同一 Azure 帳戶。 <br>- Azure 帳戶屬於單個[Azure 活動目錄 （Azure AD） 租戶或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)，與用於閘道安裝的帳戶相同。 <p><p>有關詳細資訊，請參閱[常見問題](#faq)部分。 |
   |||

   下面是一個示例，該示例顯示了與閘道資源位於同一區域並連結到同一 Azure 帳戶的閘道安裝：

   ![提供創建資料閘道資源的詳細資訊](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>連線至內部部署資料

在您建立閘道資源並且讓 Azure 訂用帳戶與此資源相關聯之後，即可使用閘道，建立邏輯應用程式與內部部署資料來源之間的連線。

1. 在 Azure 入口網站中，於邏輯應用程式設計工具中建立或開啟邏輯應用程式。

1. 新增可支援內部部署連線的連接器，例如 **SQL Server**。

1. 選取 [透過內部部署資料閘道連線]****。

1. 在 **"訂閱****"** 清單中的閘道下，選擇具有所需閘道資源的 Azure 訂閱。

1. 在顯示所選訂閱中的可用閘道資源的 **"連接閘道**"清單中，選擇所需的閘道資源。 每個閘道資源都連結到單個閘道安裝。

   > [!NOTE]
   > 閘道清單包括其他地區的閘道資源，因為邏輯應用的位置可能與閘道資源的位置不同。 

1. 提供唯一的連接名稱和其他必需資訊，具體取決於要創建的連接。

   唯一的連接名稱可説明您以後輕鬆找到該連接，尤其是在創建多個連接時。 如果情況允許，也請在使用者名稱中包含完整網域。

   範例如下：

   ![建立邏輯應用程式與資料閘道之間的連線](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 當您完成時，選取 [建立]****。

您的閘道連線現已可供邏輯應用程式使用。

## <a name="edit-connection"></a>編輯連線

要更新閘道連接的設置，可以編輯連接。

1. 要查找邏輯應用的所有 API 連接，請在邏輯應用的功能表下，在**開發工具**下，選擇**API 連接**。

   ![在邏輯應用功能表上，選擇"API 連接"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 選擇所需的閘道連接，然後選擇 **"編輯 API 連接**"。

   > [!TIP]
   > 如果更新無效，請嘗試[停止並重新啟動閘道安裝的閘道 Windows 服務帳戶](../logic-apps/logic-apps-gateway-install.md#restart-gateway)。

若要尋找與您的 Azure 訂用帳戶相關聯的所有 API 連線：

* 在 Azure 門戶功能表中，選擇**所有服務** > **Web** > **API 連接**。
* 或者，從 Azure 門戶功能表中，選擇 **"所有資源**"。 將**類型**篩選器設置為**API 連接**。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>刪除閘道資源

要創建其他閘道資源、將閘道安裝連結到其他閘道資源或刪除閘道資源，可以刪除閘道資源，而不會影響閘道安裝。

1. 在 Azure 門戶功能表中，選擇 **"所有資源**"，或從任何頁面搜索並選擇 **"所有資源**"。 尋找並選取您的閘道資源。

1. 如果尚未選取，請在您的閘道資源功能表上選取 [內部部署資料閘道]****。 在閘道資源工具列上，選擇 **"刪除**"。

   例如：

   ![刪除 Azure 中的閘道資源](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常見問題集

**問**：為什麼在 Azure 中創建閘道資源時，會出現閘道安裝？ <br/>
**答**：發生此問題的原因如下：

* Azure 帳戶必須與連結到本地電腦上的閘道安裝的帳戶相同。 檢查您是否使用連結到閘道安裝的相同標識登錄到 Azure 門戶。 此外，請確保 Azure 帳戶屬於單個 Azure [AD 租戶或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)，並設置為閘道安裝期間使用的相同 Azure AD 租戶或目錄。

* 閘道資源和閘道安裝必須使用相同的區域。 但是，邏輯應用位置可能與閘道資源位置不同。

* 您的閘道安裝已註冊並由其他閘道資源聲明。 這些安裝不會顯示在 **"安裝名稱"** 清單中。 要查看 Azure 門戶中的閘道註冊，請查找*所有*Azure 訂閱中具有**本地資料閘道**類型的所有 Azure 資源。 要取消將閘道安裝與其他閘道資源連結，請參閱[刪除閘道資源](#change-delete-gateway-resource)。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>後續步驟

* [保護邏輯應用程式](./logic-apps-securing-a-logic-app.md)
* [Logic Apps 範例和常見案例](./logic-apps-examples-and-scenarios.md)
