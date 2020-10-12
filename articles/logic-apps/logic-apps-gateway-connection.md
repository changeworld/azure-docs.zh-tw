---
title: 存取內部部署的資料來源
description: 藉由在 Azure 中建立資料閘道資源，從 Azure Logic Apps 連接到內部部署資料來源
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, divswa, logicappspm
ms.topic: article
ms.date: 08/18/2020
ms.openlocfilehash: 2dd086ccc45458299cf6b8a7ad83d023055c96ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661192"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>從 Azure Logic Apps 連線到內部部署資料來源

在[本機電腦上安裝內部*部署資料閘道*](../logic-apps/logic-apps-gateway-install.md) ，而且在您可以從邏輯應用程式存取內部部署資料來源之前，您需要在 Azure 中建立閘道資源，以便進行閘道安裝。 然後，您可以在您想要用於 Azure Logic Apps 中可用的 [內部部署連接器](../connectors/apis-list.md#on-premises-connectors) 的觸發程式和動作中，選取此閘道資源。 Azure Logic Apps 支援透過資料閘道進行的讀取和寫入作業。 不過，這些作業的[承載大小有限制](/data-integration/gateway/service-gateway-onprem#considerations)。

本文說明如何 [在本機電腦上為先前安裝的閘道](../logic-apps/logic-apps-gateway-install.md)建立 Azure 閘道資源。 如需閘道的詳細資訊，請參閱 [閘道的運作方式](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)。

> [!TIP]
> 若要直接存取 Azure 虛擬網路中的內部部署資源，而不需使用閘道，請考慮改為建立 [*整合服務環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 。 

如需如何使用閘道與其他服務的資訊，請參閱下列文章：

* [Microsoft Power Automate 內部部署的資料閘道](/power-automate/gateway-reference)
* [Microsoft Power BI 內部部署資料閘道](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 內部部署的資料閘道](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支援的資料來源

在 Azure Logic Apps 中，內部部署資料閘道支援下列資料來源的內部 [部署連接器](../connectors/apis-list.md#on-premises-connectors) ：

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

您也可以使用 REST 或 SOAP，建立透過 HTTP 或 HTTPS 連接到資料來源的 [自訂連接器](../logic-apps/custom-connector-overview.md) 。 雖然閘道本身不會產生額外的費用，但 [Logic Apps 的定價模式](../logic-apps/logic-apps-pricing.md) 適用于這些連接器以及 Azure Logic Apps 中的其他作業。

## <a name="prerequisites"></a>Prerequisites

* 您已在 [本機電腦上安裝內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。 此閘道安裝必須先存在，您才能建立連結至此安裝的閘道資源。

* 您所擁有的 [Azure 帳戶和訂用帳戶](../logic-apps/logic-apps-gateway-install.md#requirements) 與您用於閘道安裝的帳戶相同。 此 Azure 帳戶必須僅屬於單一 [Azure Active Directory (Azure AD) 租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 您必須使用相同的 Azure 帳戶和訂用帳戶在 Azure 中建立閘道資源，因為只有閘道系統管理員可以在 Azure 中建立閘道資源。 目前不支援服務主體。

  * 當您在 Azure 中建立閘道資源時，請選取要與閘道資源連結的閘道安裝，且僅限該閘道資源。 每個閘道資源只能連結到一個閘道安裝。 您無法選取已與另一個閘道資源相關聯的閘道安裝。
  
  * 您的邏輯應用程式和閘道資源不一定要存在於相同的 Azure 訂用帳戶中。 如果您有訂用帳戶存取權，在可存取內部部署資料來源的觸發程式和動作中，您可以選取其他具有閘道資源的 Azure 訂用帳戶。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>建立 Azure 閘道資源

在本機電腦上安裝閘道之後，請為您的閘道建立 Azure 資源。

1. 以用來安裝閘道的相同 Azure 帳戶登入 [Azure 入口網站](https://portal.azure.com) 。

1. 在 Azure 入口網站搜尋方塊中，輸入「內部部署資料閘道」，然後選取 [內部 **部署**資料閘道]。

   ![尋找 [內部部署資料閘道]](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. 在 [ **內部部署資料閘道**] 下，選取 [ **新增**]。

   ![新增適用于資料閘道的 Azure 資源](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. 在 [ **建立連線閘道**] 下，為您的閘道資源提供此資訊。 當您完成時，選取 [建立]。

   | 屬性 | 描述 |
   |----------|-------------|
   | **資源名稱** | 提供閘道資源的名稱，其中只包含字母、數位、連字號 (`-`) 、底線 (`_`) 、括弧 (`(` 、 `)`) 或句號 (`.`) 。 |
   | **訂用帳戶** | 針對用於閘道安裝的 Azure 帳戶選取 Azure 訂用帳戶。 預設的訂用帳戶會由您用來登入的 Azure 帳戶來決定。 |
   | **資源群組** | 您要使用的[Azure 資源群組](../azure-resource-manager/management/overview.md) |
   | **位置** | [閘道安裝](../logic-apps/logic-apps-gateway-install.md)期間為閘道雲端服務所選取的相同區域或位置。 否則，您的閘道安裝將不會出現在 [ **安裝名稱** ] 清單中。 您的邏輯應用程式位置可能會與您的閘道資源位置不同。 |
   | **安裝名稱** | 選取只有在符合下列條件時，才會出現在清單中的閘道安裝： <p><p>-閘道安裝會使用與您想要建立的閘道資源相同的區域。 <br>-閘道安裝未連結至其他 Azure 閘道資源。 <br>-閘道安裝會連結到您用來建立閘道資源的相同 Azure 帳戶。 <br>-您的 Azure 帳戶屬於單一 [Azure Active Directory (Azure AD) 租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology) ，與您用於閘道安裝的帳戶相同。 <p><p>如需詳細資訊，請參閱 [常見問題](#faq) 一節。 |
   |||

   以下範例顯示與閘道資源位於相同區域的閘道安裝，並連結到相同的 Azure 帳戶：

   ![提供詳細資料以建立資料閘道資源](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>連線至內部部署資料

在您建立閘道資源並且讓 Azure 訂用帳戶與此資源相關聯之後，即可使用閘道，建立邏輯應用程式與內部部署資料來源之間的連線。

1. 在 Azure 入口網站中，於邏輯應用程式設計工具中建立或開啟邏輯應用程式。

1. 新增可支援內部部署連線的連接器，例如 **SQL Server**。

1. 選取 [透過內部部署資料閘道連線]。

1. 在 [ **閘道**] 下，從 [訂用帳戶] 清單中選取您想要的閘道資源 **所在的 Azure** 訂用帳戶

   如果您有訂用帳戶存取權，您可以從不同的 Azure 訂用帳戶中選取每個與不同閘道資源相關聯的 Azure 訂用帳戶。 您的邏輯應用程式和閘道資源不一定要存在於相同的 Azure 訂用帳戶中。

1. 從 [連線 **閘道** ] 清單中，顯示所選訂用帳戶中的可用閘道資源，然後選取您想要的閘道資源。 每個閘道資源都會連結至單一閘道安裝。

   > [!NOTE]
   > 閘道清單包含其他區域中的閘道資源，因為您的邏輯應用程式位置可能與閘道資源的位置不同。 

1. 提供唯一的連線名稱和其他必要資訊，而這取決於您要建立的連接。

   唯一的連線名稱可協助您稍後輕鬆找到該連接，特別是當您建立多個連接時。 如果情況允許，也請在使用者名稱中包含完整網域。

   範例如下：

   ![建立邏輯應用程式與資料閘道之間的連線](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 當您完成時，選取 [建立]。

您的閘道連線現已可供邏輯應用程式使用。

## <a name="edit-connection"></a>編輯連線

若要更新閘道連線的設定，您可以編輯連線。

1. 若要尋找邏輯應用程式的所有 API 連線，請在邏輯應用程式的功能表上，選取 [ **開發工具**] 底下的 [ **api 連接**]。

   ![在邏輯應用程式功能表上，選取 [API 連接]](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 選取您要的閘道連線，然後選取 [ **編輯 API**連線]。

   > [!TIP]
   > 如果您的更新未生效，請嘗試 [停止並重新啟動閘道 Windows 服務帳戶](../logic-apps/logic-apps-gateway-install.md#restart-gateway) 以進行閘道安裝。

若要尋找與您的 Azure 訂用帳戶相關聯的所有 API 連線：

* 從 Azure 入口網站] 功能表中，選取 [**所有服務**]  >  **Web**  >  **API 連接**。
* 或者，從 [Azure 入口網站] 功能表選取 [ **所有資源**]。 將 [ **類型** 篩選] 設定為 [ **API 連接**]。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>刪除閘道資源

若要建立不同的閘道資源，請將您的閘道安裝連結至不同的閘道資源，或移除閘道資源，您可以刪除閘道資源，而不會影響閘道安裝。

1. 從 [Azure 入口網站] 功能表中，選取 [ **所有資源**]，或從任何頁面搜尋並選取 **所有資源** 。 尋找並選取您的閘道資源。

1. 如果尚未選取，請在您的閘道資源功能表上選取 [內部部署資料閘道]****。 在 [閘道資源] 工具列上，選取 [ **刪除**]。

   例如：

   ![刪除 Azure 中的閘道資源](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常見問題集

**問**：當我在 Azure 中建立閘道資源時，為什麼看不到閘道安裝？ <br/>
**答**：發生此問題的原因如下：

* 您的 Azure 帳戶和您在本機電腦上用來安裝閘道的帳戶不同。 確認您已使用用於閘道安裝的相同身分識別登入 Azure 入口網站。 只有閘道系統管理員可以在 Azure 中建立閘道資源。 目前不支援服務主體。

* 您的 Azure 帳戶不只屬於單一 [Azure AD 租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 請確認您使用的是您在閘道安裝期間所使用的相同 Azure AD 租使用者或目錄。

* 您的閘道資源和閘道安裝不存在於相同的區域中。 不過，您的邏輯應用程式位置可能會與您的閘道資源位置不同。

* 您的閘道安裝已經與另一個閘道資源相關聯。 每個閘道資源只能連結到一個閘道安裝，只能連結到一個 Azure 帳戶和訂用帳戶。 因此，您無法選取已與另一個閘道資源相關聯的閘道安裝。 這些安裝不會出現在 [ **安裝名稱** ] 清單中。

  若要在 Azure 入口網站中檢查閘道註冊，請在*所有*azure 訂用帳戶中尋找所有具有內部**部署資料閘道**資源類型的 Azure 資源。 若要取消閘道安裝與其他閘道資源的連結，請參閱 [刪除閘道資源](#change-delete-gateway-resource)。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>接下來的步驟

* [保護邏輯應用程式](./logic-apps-securing-a-logic-app.md)
* [Logic Apps 範例和常見案例](./logic-apps-examples-and-scenarios.md)
