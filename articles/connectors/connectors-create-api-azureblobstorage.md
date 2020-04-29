---
title: 連線至 Azure Blob 儲存體
description: 使用 Azure Logic Apps 來建立和管理 Azure 儲存體帳戶中的 blob
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247354"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 在 Azure Blob 儲存體中建立和管理 blob

本文說明如何使用 Azure Blob 儲存體連接器，從邏輯應用程式內存取和管理 Azure 儲存體帳戶中儲存為 Blob 的檔案。 這樣一來，您就可以建立邏輯應用程式，來自動執行用於管理檔案的工作和工作流程。 例如，您可以建置邏輯應用程式，以在儲存體帳戶中建立、取得、更新和刪除檔案。

假設您有一個會在 Azure 網站上進行更新的工具。 並將它作為邏輯應用程式的觸發程序。 當此事件發生時，您可以讓邏輯應用程式更新 Blob 儲存體容器中的某些檔案，而這是邏輯應用程式中的其中一個動作。

如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需連接器專屬的技術資訊，請參閱 [Azure Blob 儲存體連接器參考](https://docs.microsoft.com/connectors/azureblobconnector/)。

> [!IMPORTANT]
> 如果邏輯應用程式位於相同的區域，則無法直接存取位於防火牆後面的儲存體帳戶。 因應措施是，您可以將邏輯應用程式和儲存體帳戶放在不同的區域中。 如需啟用從 Azure Logic Apps 存取防火牆後方之儲存體帳戶的詳細資訊，請參閱本主題稍後的[存取防火牆後方的儲存體帳戶](#storage-firewalls)一節。

<a name="blob-storage-limits"></a>

## <a name="limits"></a>限制

* 根據預設，Azure Blob 儲存體動作可以讀取或寫入*50 MB 或更小*的檔案。 若要處理大於 50 MB 但最多 1024 MB 的檔案，Azure Blob 儲存體動作支援[訊息區塊](../logic-apps/logic-apps-handle-large-messages.md)化。 「**取得 blob 內容**」動作會隱含地使用區塊化。

* Azure Blob 儲存體觸發程式不支援區塊化。 當要求檔案內容時，觸發程式只會選取 50 MB 或更小的檔案。 若要取得大於 50 MB 的檔案，請依照下列模式：

  * 使用會傳回檔案屬性的 Azure Blob 儲存體觸發程式，例如**新增或修改 Blob 時（僅限屬性）**。

  * 遵循具有 [Azure Blob 儲存體**取得 Blob 內容**] 動作的觸發程式，它會讀取完整的檔案，並隱含地使用區塊化。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [Azure 儲存體帳戶和儲存體容器](../storage/blobs/storage-quickstart-blobs-portal.md)

* 需要從中存取 Azure Blob 儲存體帳戶的邏輯應用程式。 若要使用 Azure Blob 儲存體觸發程序來啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>新增 Blob 儲存體觸發程序

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

這個範例會示範如何在儲存體容器中新增或更新 blob 的屬性時，使用**新增或修改 blob 時（僅限屬性）** 觸發程式來啟動邏輯應用程式工作流程。

1. 在[Azure 入口網站](https://portal.azure.com)或 Visual Studio 中，建立空白邏輯應用程式，以開啟邏輯應用程式設計工具。 這個範例會使用 Azure 入口網站。

2. 在搜尋方塊中，輸入「azure blob」作為篩選條件。 從觸發程序清單中，選取您想要的觸發程序。

   此範例使用此觸發**程式：新增或修改 Blob 時（僅限屬性）**

   ![選取 Azure Blob 儲存體觸發程式](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. 如果系統提示您輸入連線詳細資料，請[立即建立 Blob 儲存體連線](#create-connection)。 或者，如果連線已存在，請提供觸發程序的必要資訊。

   在此範例中，請選取您想要監視的容器和資料夾。

   1. 在 [容器]**** 方塊中，選取 [資料夾] 圖示。

   2. 在 [資料夾] 清單中，選擇右角括弧（ **>** ），然後流覽直到您找到並選取您想要的資料夾。

      ![選取要搭配觸發程式使用的儲存體資料夾](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 選取您想要讓觸發程序檢查資料夾是否有變更的間隔和頻率。

4. 當您完成時，請在設計工具的工具列上，選擇 [儲存]****。

5. 現在，繼續針對您想要使用觸發程序結果來執行的工作，於邏輯應用程式中新增一或多個動作。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>新增 Blob 儲存體動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動。

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

2. 在邏輯應用程式設計工具中，于觸發程式或動作底下，選擇 [**新增步驟**]。

   ![將新步驟新增至邏輯應用程式工作流程](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選擇顯示的加號（**+**），然後選取 [**新增動作**]。

3. 在搜尋方塊中，輸入「azure blob」作為篩選條件。 從 [動作] 清單中，選取您想要的動作。

   此範例會使用此動作：**取得 blob 內容**

   ![選取 Azure Blob 儲存體動作](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. 如果系統提示您輸入連線詳細資料，請[立即建立 Azure Blob 儲存體連線](#create-connection)。
或者，如果連線已存在，請提供動作的必要資訊。

   針對此範例，請選取您想要的檔案。

   1. 從 [Blob]**** 方塊中，選取 [資料夾] 圖示。
  
      ![選取要搭配動作使用的儲存體資料夾](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. 根據 blob 的**識別碼**，尋找並選取您想要的檔案。 您可以在先前所述的 blob 儲存體觸發程式所傳回的 blob 中繼資料中找到此**識別碼**。

5. 當您完成時，請在設計工具的工具列上，選擇 [儲存]****。
若要測試邏輯應用程式，請確定所選取的資料夾包含 Blob。

此範例只會取得 Blob 的內容。 若要檢視內容，請新增另一個動作，以使用另一個連接器建立具有 Blob 的檔案。 例如，新增 OneDrive 動作來根據 Blob 內容建立檔案。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>連線至儲存體帳戶

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 當系統提示您建立連線時，請提供下列資訊：

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **連線名稱** | 是 | <*連接名稱*> | 要為連線建立的名稱 |
   | **儲存體帳戶** | 是 | <*儲存體-帳戶*> | 從清單中選取您的儲存體帳戶。 |
   ||||

   例如：

   ![建立 Azure Blob 儲存體帳戶連線](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. 當您準備好時，請選取 [**建立**]

1. 建立連線之後，請繼續 [[新增 blob 儲存體觸發](#add-trigger)程式] 或 [[新增 blob 儲存體] 動作](#add-action)。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，例如連接器的 Swagger 檔案所描述的觸發程式、動作和限制，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/azureblobconnector/)。

> [!NOTE]
> 對於[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式，此連接器的 ise 標記版本會使用[ISE 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>存取防火牆後方的儲存體帳戶

您可以使用[防火牆和防火牆規則](../storage/common/storage-network-security.md)來限制存取，以將網路安全性新增至 Azure 儲存體帳戶。 不過，此設定會為 Azure 和其他需要存取儲存體帳戶的 Microsoft 服務建立挑戰。 資料中心內的本機通訊會將內部 IP 位址抽象化，因此您無法設定具有 IP 限制的防火牆規則。 如需詳細資訊，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../storage/common/storage-network-security.md)。

以下提供各種選項，可讓您使用 Azure Blob 儲存體連接器或其他解決方案，從 Azure Logic Apps 存取防火牆後方的儲存體帳戶：

* Azure 儲存體 Blob 連接器

  * [存取其他區域中的儲存體帳戶](#access-other-regions)
  * [透過受信任的虛擬網路存取儲存體帳戶](#access-trusted-virtual-network)

* 其他解決方案

  * [以受管理身分識別的信任服務存取儲存體帳戶](#access-trusted-service)
  * [透過 Azure API 管理來存取儲存體帳戶](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>存取相同區域中的儲存體帳戶時發生問題

當邏輯應用程式位於相同區域時，無法直接存取位於防火牆後方的儲存體帳戶。 因應措施是將您的邏輯應用程式放在與儲存體帳戶不同的區域中，並在[您的區域中授與受控連接器的輸出 IP 位址](../logic-apps/logic-apps-limits-and-config.md#outbound)存取權。

> [!NOTE]
> 此解決方案不適用於 Azure 表格儲存體連接器和 Azure 佇列儲存體連接器。 相反地，若要存取您的表格儲存體或佇列儲存體，請使用內建的 HTTP 觸發程式和動作。

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>透過受信任的虛擬網路存取儲存體帳戶

您可以將儲存體帳戶放在您管理的 Azure 虛擬網路中，然後將該虛擬網路新增至受信任的虛擬網路清單。 若要讓您的邏輯應用程式透過[受信任的虛擬網路](../virtual-network/virtual-networks-overview.md)存取儲存體帳戶，您必須將該邏輯應用程式部署到[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，這可連線到虛擬網路中的資源。 接著，您可以將該 ISE 中的子網新增至受信任的清單。 Azure 儲存體連接器（例如 Blob 儲存體連接器）可以直接存取儲存體容器。 此設定與使用 ISE 中服務端點的體驗相同。

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>以受管理身分識別的信任服務存取儲存體帳戶

若要透過防火牆授與 Microsoft 信任服務對儲存體帳戶的存取權，您可以針對這些服務在該儲存體帳戶上設定例外狀況。 此解決方案允許支援[受控識別進行驗證的](../active-directory/managed-identities-azure-resources/overview.md)Azure 服務，將防火牆後方的儲存體帳戶存取為受信任的服務。 具體來說，若要讓全域多租使用者 Azure 中的邏輯應用程式存取這些儲存體帳戶，您必須先在邏輯應用程式上[啟用受控識別支援](../logic-apps/create-managed-service-identity.md)。 然後，您可以在邏輯應用程式中使用 HTTP 動作或觸發程式，並[將其驗證類型設定為使用邏輯應用程式的受控識別](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 在此案例中，您只能*使用 HTTP*動作或觸發程式。

若要設定例外狀況和受控識別支援，請遵循下列一般步驟：

1. 在儲存體帳戶的 [**設定**] 底下，選取 [**防火牆和虛擬網路**]。 在 [**允許存取來源**] 底下，選取 [**選取的網路**] 選項，以顯示相關的設定。

1. 在 [**例外**狀況] 底下，選取 [**允許信任的 Microsoft 服務存取此儲存體帳戶**]，然後選取 [**儲存**]。

   ![選取允許 Microsoft 信任服務的例外狀況](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. 在邏輯應用程式的設定中，[啟用受控識別的支援](../logic-apps/create-managed-service-identity.md)。

1. 在邏輯應用程式的工作流程中，新增並設定 HTTP 動作或觸發程式，以存取儲存體帳戶或實體。

   > [!IMPORTANT]
   > 針對連出 HTTP 動作或 Azure 儲存體帳戶的觸發程式呼叫，請確定要求標頭包含`x-ms-version`您想要在儲存體帳戶上執行之作業的屬性和 API 版本。 如需詳細資訊，請參閱[使用受管理的身分識別驗證存取](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)和[Azure 儲存體服務的版本控制](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)。

1. 在該動作上，選取要用於驗證的[受控識別](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>透過 Azure API 管理來存取儲存體帳戶

如果您使用專用層進行[Api 管理](../api-management/api-management-key-concepts.md)，您可以使用 api 管理來處理儲存體 API，並允許後者的 IP 位址通過防火牆。 基本上，將 API 管理所使用的 Azure 虛擬網路新增至儲存體帳戶的防火牆設定。 接著，您可以使用 API 管理動作或 HTTP 動作來呼叫 Azure 儲存體 Api。 不過，如果您選擇此選項，就必須自行處理驗證程式。 如需詳細資訊，請參閱[簡單的企業整合架構](https://aka.ms/aisarch)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
