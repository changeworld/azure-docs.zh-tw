---
title: 存取內部部署的資料來源
description: 藉由建立 Azure 內部部署資料閘道資源，從 Azure Logic Apps 連接到內部部署資料來源
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: b216fa668483ef6fc30c1054dd4f65361ad17934
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065950"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>從 Azure Logic Apps 連線到內部部署資料來源

在您從邏輯應用程式存取內部部署資料來源之前，您必須在[本機電腦上安裝內部*部署資料閘道*](../logic-apps/logic-apps-gateway-install.md)之後，建立 Azure 資源。 然後，您的邏輯應用程式會在可供 Azure Logic Apps 的[內部部署連接器](../connectors/apis-list.md#on-premises-connectors)所提供的觸發程式和動作中使用此 Azure 閘道資源。

本文說明如何[在您的本機電腦上](../logic-apps/logic-apps-gateway-install.md)，為先前安裝的閘道建立 Azure 閘道資源。 如需閘道的詳細資訊，請參閱[閘道的運作方式](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)。

> [!TIP]
> 若要連線至 Azure 虛擬網路，請考慮建立[*整合服務環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 

如需如何使用閘道與其他服務的資訊，請參閱下列文章：

* [Microsoft Power Automate 內部部署的資料閘道](/power-automate/gateway-reference)
* [Microsoft Power BI 內部部署資料閘道](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 內部部署的資料閘道](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支援的資料來源

在 Azure Logic Apps 中，內部部署資料閘道支援這些資料來源的內部[部署連接器](../connectors/apis-list.md#on-premises-connectors)：

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

Azure Logic Apps 支援透過資料閘道進行的讀取和寫入作業。 不過，這些作業的[承載大小有限制](/data-integration/gateway/service-gateway-onprem#considerations)。 雖然閘道本身不會產生額外成本，但[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md)適用于 Azure Logic Apps 中的這些連接器和其他作業。

## <a name="prerequisites"></a>先決條件

* 您已在[本機電腦上安裝內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

* 您使用的是安裝該資料閘道時所使用的[相同 Azure 帳戶和訂用帳戶](../logic-apps/logic-apps-gateway-install.md#requirements)。 此 Azure 帳戶必須屬於單一[Azure Active Directory （Azure AD）租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)。

* 您的閘道安裝尚未註冊並由另一個 Azure 閘道資源所宣告。

  當您在 Azure 入口網站中建立閘道資源時，您會選取閘道安裝，這會連結到您的閘道資源，而只是該閘道資源。 在 Azure Logic Apps 中，內部部署觸發程序與動作會使用閘道資源來連線到內部部署資料來源。 在這些觸發程式和動作中，您可以選取您的 Azure 訂用帳戶和您想要使用的相關聯網關資源。 每個閘道資源只會連結至一個閘道安裝，而只會連結到一個 Azure 帳戶。

  > [!NOTE]
  > 只有閘道系統管理員可以在 Azure 入口網站中建立閘道資源。 目前不支援服務主體。 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>建立 Azure 閘道資源

在本機電腦上安裝閘道之後，請為您的閘道建立 Azure 資源。

1. 使用與用來安裝閘道相同的 Azure 帳戶登入[Azure 入口網站](https://portal.azure.com)。

1. 在 [Azure 入口網站搜尋] 方塊中，輸入「內部部署資料閘道」，然後選取 [內部**部署資料閘道**]。

   ![尋找 [內部部署資料閘道]](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. 在 [**內部部署資料閘道**] 底下，選取 [**新增**]。

   ![為數據閘道新增 Azure 資源](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. 在 [**建立連線閘道**] 底下，為您的閘道資源提供此資訊。 當您完成時，選取 [建立]。

   | 屬性 | 描述 |
   |----------|-------------|
   | **資源名稱** | 提供閘道資源的名稱，其中只包含字母、數位、連字號（ `-` ）、底線（ `_` ）、括弧（ `(` ， `)` ）或句點（ `.` ）。 |
   | **訂用帳戶** | 為用於閘道安裝的 Azure 帳戶選取 Azure 訂用帳戶。 預設的訂用帳戶會由您用來登入的 Azure 帳戶來決定。 |
   | **資源群組** | 您想要使用的[Azure 資源群組](../azure-resource-manager/management/overview.md) |
   | **位置** | [閘道安裝](../logic-apps/logic-apps-gateway-install.md)期間為閘道雲端服務選取的相同區域或位置。 否則，您的閘道安裝將不會出現在 [**安裝名稱**] 清單中。 您的邏輯應用程式位置可能與閘道資源位置不同。 |
   | **安裝名稱** | 選取只有在符合下列條件時，才會出現在清單中的閘道安裝： <p><p>-閘道安裝會使用與您想要建立的閘道資源相同的區域。 <br>-閘道安裝未連結至另一個 Azure 閘道資源。 <br>-閘道安裝會連結到您用來建立閘道資源的相同 Azure 帳戶。 <br>-您的 Azure 帳戶屬於單一[Azure Active Directory （Azure AD）租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)，而且是用來安裝閘道的相同帳戶。 <p><p>如需詳細資訊，請參閱[常見問題](#faq)一節。 |
   |||

   以下範例顯示的閘道安裝與閘道資源位於相同的區域，並連結至相同的 Azure 帳戶：

   ![提供建立資料閘道資源的詳細資訊](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>連線至內部部署資料

在您建立閘道資源並且讓 Azure 訂用帳戶與此資源相關聯之後，即可使用閘道，建立邏輯應用程式與內部部署資料來源之間的連線。

1. 在 Azure 入口網站中，於邏輯應用程式設計工具中建立或開啟邏輯應用程式。

1. 新增可支援內部部署連線的連接器，例如 **SQL Server**。

1. 選取 [透過內部部署資料閘道連線]。

1. 在 [**閘道**] 下的 [訂用帳戶 **] 清單中**，選取您想要的 Azure 訂用帳戶。

1. 從顯示所選訂用帳戶中可用閘道資源的 [連線**閘道**] 清單中，選取您想要的閘道資源。 每個閘道資源都會連結到單一閘道安裝。

   > [!NOTE]
   > 閘道清單包含其他區域中的閘道資源，因為您的邏輯應用程式位置可能與閘道資源的位置不同。 

1. 提供唯一的連線名稱和其他必要資訊，這取決於您想要建立的連接。

   唯一的連線名稱可協助您稍後輕鬆地找到該連接，特別是當您建立多個連接時。 如果情況允許，也請在使用者名稱中包含完整網域。

   以下是範例：

   ![建立邏輯應用程式與資料閘道之間的連線](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 當您完成時，選取 [建立]。

您的閘道連線現已可供邏輯應用程式使用。

## <a name="edit-connection"></a>編輯連線

若要更新閘道連線的設定，您可以編輯您的連線。

1. 若只要尋找邏輯應用程式的所有 API 連線，請在邏輯應用程式功能表的 [**開發工具**] 底下，選取 [ **API**連線]。

   ![在邏輯應用程式功能表上，選取 [API 連線]](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 選取您想要的閘道連線，然後選取 [**編輯 API**連線]。

   > [!TIP]
   > 如果您的更新不會生效，請嘗試[停止並重新啟動閘道 Windows 服務帳戶](../logic-apps/logic-apps-gateway-install.md#restart-gateway)以進行閘道安裝。

若要尋找與您的 Azure 訂用帳戶相關聯的所有 API 連線：

* 從 [Azure 入口網站] 功能表中，選取 [**所有服務**] [  >  **Web**  >  **API**連線]。
* 或者，從 [Azure 入口網站] 功能表選取 [**所有資源**]。 將 [**類型**] 篩選準則設定為 [ **API**連線]。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>刪除閘道資源

若要建立不同的閘道資源，請將您的閘道安裝連結至不同的閘道資源，或移除閘道資源，您可以刪除閘道資源，而不會影響閘道安裝。

1. 從 [Azure 入口網站] 功能表中，選取 [**所有資源**]，或搜尋並從任何頁面中選取 [**所有資源**]。 尋找並選取您的閘道資源。

1. 如果尚未選取，請在您的閘道資源功能表上選取 [內部部署資料閘道]****。 在 [閘道資源] 工具列上，選取 [**刪除**]。

   例如:

   ![刪除 Azure 中的閘道資源](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常見問題集

**問**：為什麼我在 Azure 中建立閘道資源時，我的閘道安裝不會出現？ <br/>
**答**：發生此問題的原因如下：

* 您的 Azure 帳戶必須與在本機電腦上連結到閘道安裝的帳戶相同。 請確認您已使用與閘道安裝連結的相同身分識別登入 Azure 入口網站。 此外，請確定您的 Azure 帳戶屬於單一[Azure AD 租使用者或目錄](../active-directory/fundamentals/active-directory-whatis.md#terminology)，並且設定為與閘道安裝期間所使用的相同 Azure AD 租使用者或目錄。

* 您的閘道資源和閘道安裝必須使用相同的區域。 不過，您的邏輯應用程式位置可能與閘道資源位置不同。

* 您的閘道安裝已由另一個閘道資源註冊並宣告。 這些安裝不會出現在 [**安裝名稱**] 清單中。 若要在 Azure 入口網站中檢查閘道註冊，請在您*所有*的 azure 訂用帳戶中尋找所有具有內部**部署資料閘道**類型的 azure 資源。 若要取消閘道安裝與其他閘道資源的連結，請參閱[刪除閘道資源](#change-delete-gateway-resource)。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>接下來的步驟

* [保護邏輯應用程式](./logic-apps-securing-a-logic-app.md)
* [Logic Apps 範例和常見案例](./logic-apps-examples-and-scenarios.md)
