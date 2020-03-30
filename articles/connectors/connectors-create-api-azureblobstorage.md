---
title: 連線至 Azure Blob 儲存體
description: 使用 Azure 邏輯應用創建和管理 Azure 存儲帳戶中的 Blob
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247354"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用創建和管理 Azure Blob 存儲中的 Blob

本文說明如何使用 Azure Blob 儲存體連接器，從邏輯應用程式內存取和管理 Azure 儲存體帳戶中儲存為 Blob 的檔案。 這樣一來，您就可以建立邏輯應用程式，來自動執行用於管理檔案的工作和工作流程。 例如，您可以建置邏輯應用程式，以在儲存體帳戶中建立、取得、更新和刪除檔案。

假設您有一個會在 Azure 網站上進行更新的工具。 並將它作為邏輯應用程式的觸發程序。 當此事件發生時，您可以讓邏輯應用程式更新 Blob 儲存體容器中的某些檔案，而這是邏輯應用程式中的其中一個動作。

如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 如需連接器專屬的技術資訊，請參閱 [Azure Blob 儲存體連接器參考](https://docs.microsoft.com/connectors/azureblobconnector/)。

> [!IMPORTANT]
> 如果邏輯應用都位於同一區域，則無法直接存取防火牆後面的存儲帳戶。 作為解決方法，您可以將邏輯應用和存儲帳戶放在不同的區域。 有關將 Azure 邏輯應用訪問啟用到防火牆後面的存儲帳戶的詳細資訊，請參閱本主題後面的[防火牆後面的訪問存儲帳戶](#storage-firewalls)部分。

<a name="blob-storage-limits"></a>

## <a name="limits"></a>限制

* 預設情況下，Azure Blob 存儲操作可以讀取或寫入*50 MB 或較小的*檔。 要處理大於 50 MB 但高達 1024 MB 的檔，Azure Blob 存儲操作支援[消息分塊](../logic-apps/logic-apps-handle-large-messages.md)。 **獲取 blob 內容**操作隱式使用分塊。

* Azure Blob 存儲觸發器不支援分塊。 請求檔內容時，觸發器僅選擇 50 MB 或較小的檔。 若要取得大於 50 MB 的檔案，請依照下列模式：

  * 使用返回檔案屬性的 Azure Blob 存儲觸發器，例如**添加或修改 Blob 時（僅限屬性）。**

  * 使用 Azure Blob 存儲**獲取 Blob 內容**操作遵循觸發器，該操作讀取完整檔並隱式使用分塊。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [Azure 儲存體帳戶和儲存體容器](../storage/blobs/storage-quickstart-blobs-portal.md)

* 需要從中存取 Azure Blob 儲存體帳戶的邏輯應用程式。 若要使用 Azure Blob 儲存體觸發程序來啟動邏輯應用程式，您需要[空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>新增 Blob 儲存體觸發程序

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

此示例演示如何在存儲容器中添加或更新 Blob 的屬性時，使用 **"何時添加或修改 Blob時"觸發器啟動**邏輯應用工作流。

1. 在[Azure 門戶](https://portal.azure.com)或視覺化工作室中，創建一個空白邏輯應用，該應用將打開邏輯應用設計器。 這個範例會使用 Azure 入口網站。

2. 在搜尋方塊中，輸入「azure blob」作為篩選條件。 從觸發程序清單中，選取您想要的觸發程序。

   此示例使用此觸發器：**添加或修改 Blob 時（僅限屬性）**

   ![選擇 Azure Blob 存儲觸發器](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. 如果系統提示您輸入連線詳細資料，請[立即建立 Blob 儲存體連線](#create-connection)。 或者，如果連線已存在，請提供觸發程序的必要資訊。

   在此範例中，請選取您想要監視的容器和資料夾。

   1. 在 [容器]**** 方塊中，選取 [資料夾] 圖示。

   2. 在資料夾清單中，選擇直角括弧 （），**>** 然後流覽，直到找到並選擇所需的資料夾。

      ![選擇要與觸發器一起使用的存儲資料夾](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. 選取您想要讓觸發程序檢查資料夾是否有變更的間隔和頻率。

4. 當您完成時，請在設計工具的工具列上，選擇 [儲存]****。

5. 現在，繼續針對您想要使用觸發程序結果來執行的工作，於邏輯應用程式中新增一或多個動作。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>新增 Blob 儲存體動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 在此範例中，邏輯應用程式會使用[循環觸發程序](../connectors/connectors-native-recurrence.md)來啟動。

1. 在 [Azure 入口網站](https://portal.azure.com)或 Visual Studio 的「邏輯應用程式設計工具」中，開啟邏輯應用程式。 這個範例會使用 Azure 入口網站。

2. 在邏輯應用設計器中，在觸發器或操作下，選擇 **"新步驟**"。

   ![向邏輯應用工作流添加新步驟](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   若要在現有步驟之間新增動作，請將滑鼠放在連接箭頭上。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

3. 在搜尋方塊中，輸入「azure blob」作為篩選條件。 從 [動作] 清單中，選取您想要的動作。

   此示例使用此操作：獲取**Blob 內容**

   ![選擇 Azure Blob 存儲操作](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. 如果系統提示您輸入連線詳細資料，請[立即建立 Azure Blob 儲存體連線](#create-connection)。
或者，如果連線已存在，請提供動作的必要資訊。

   針對此範例，請選取您想要的檔案。

   1. 從 [Blob]**** 方塊中，選取 [資料夾] 圖示。
  
      ![選擇要與操作一起使用的存儲資料夾](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. 根據 Blob 的**ID**號查找並選擇所需的檔。 您可以在前面描述的 Blob 存儲觸發器返回的 Blob 中繼資料中找到此**ID**號。

5. 當您完成時，請在設計工具的工具列上，選擇 [儲存]****。
若要測試邏輯應用程式，請確定所選取的資料夾包含 Blob。

此範例只會取得 Blob 的內容。 若要檢視內容，請新增另一個動作，以使用另一個連接器建立具有 Blob 的檔案。 例如，新增 OneDrive 動作來根據 Blob 內容建立檔案。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>連線至儲存體帳戶

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 當系統提示您創建連接時，請提供以下資訊：

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **連接名稱** | 是 | <*連接名稱*> | 要為連線建立的名稱 |
   | **存儲帳戶** | 是 | <*存儲帳戶*> | 從清單中選取您的儲存體帳戶。 |
   ||||

   例如：

   ![創建 Azure Blob 存儲帳戶連接](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. 準備就緒後，選擇 **"創建"**

1. 創建連接後，繼續[添加 Blob 存儲觸發器](#add-trigger)或[添加 Blob 存儲操作](#add-action)。

## <a name="connector-reference"></a>連接器參考

有關此連接器的更多技術詳細資訊，例如連接器的 Swagger 檔所述的觸發器、操作和限制，請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/azureblobconnector/)。

> [!NOTE]
> 對於[整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用，此連接器的 ISE 標記版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>在防火牆後面訪問存儲帳戶

您可以通過限制防火牆[和防火牆規則](../storage/common/storage-network-security.md)的訪問，將網路安全添加到 Azure 存儲帳戶。 但是，此設置會給 Azure 和其他需要訪問存儲帳戶的 Microsoft 服務帶來挑戰。 資料中心中的本地通信會抽象內部 IP 位址，因此無法設置帶有 IP 限制的防火牆規則。 如需詳細資訊，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../storage/common/storage-network-security.md)。

以下是使用 Azure Blob 存儲連接器或其他解決方案從 Azure 邏輯應用訪問防火牆後面的存儲帳戶的各種選項：

* Azure 儲存體 Blob 連接器

  * [訪問其他區域的存儲帳戶](#access-other-regions)
  * [通過受信任的虛擬網路訪問存儲帳戶](#access-trusted-virtual-network)

* 其他解決方案

  * [使用託管標識將存儲帳戶訪問為受信任服務](#access-trusted-service)
  * [通過 Azure API 管理訪問存儲帳戶](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>訪問同一區域中的存儲帳戶時遇到問題

邏輯應用不能直接存取防火牆後面的存儲帳戶，因為它們都位於同一區域。 作為解決方法，將邏輯應用放在與存儲帳戶不同的區域中，並授予對[區域中託管連接器的出站 IP 位址](../logic-apps/logic-apps-limits-and-config.md#outbound)的存取權限。

> [!NOTE]
> 此解決方案不適用於 Azure 表存儲連接器和 Azure 佇列存儲連接器。 相反，要訪問表存儲或佇列存儲，請使用內置的 HTTP 觸發器和操作。

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>通過受信任的虛擬網路訪問存儲帳戶

您可以將存儲帳戶放在您管理的 Azure 虛擬網路中，然後將該虛擬網路添加到受信任的虛擬網路清單中。 要讓邏輯應用通過[受信任的虛擬網路](../virtual-network/virtual-networks-overview.md)訪問存儲帳戶，您需要將該邏輯應用部署到[整合服務環境 （ISE），該環境](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)可以連接到虛擬網路中的資源。 然後，您可以將該 ISE 中的子網添加到受信任的清單中。 Azure 存儲連接器（如 Blob 存儲連接器）可以直接存取存儲容器。 此設置與使用 ISE 中的服務終結點具有相同的體驗。

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>使用託管標識將存儲帳戶訪問為受信任服務

要通過防火牆授予 Microsoft 受信任的服務對存儲帳戶的存取權限，可以在這些服務的存儲帳戶上設置異常。 此解決方案允許支援[託管標識的](../active-directory/managed-identities-azure-resources/overview.md)Azure 服務進行身份驗證，以將防火牆後面的存儲帳戶作為受信任的服務訪問。 具體而言，對於全域多租戶 Azure 中的邏輯應用訪問這些存儲帳戶，首先在邏輯應用上[啟用託管標識支援](../logic-apps/create-managed-service-identity.md)。 然後，在邏輯應用中使用 HTTP 操作或觸發器，並將[其身份驗證類型設置為使用邏輯應用的託管標識](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 對於此方案，*只能使用*HTTP 操作或觸發器。

要設置異常和託管標識支援，請按照以下一般步驟操作：

1. 在存儲帳戶上，在 **"設置"** 下，選擇**防火牆和虛擬網路**。 在 **"允許從 "訪問**"下，選擇 **"選定網路"** 選項，以便顯示相關設置。

1. 在 **"例外**"下，選擇 **"允許受信任的 Microsoft 服務訪問此存儲帳戶**"，然後選擇"**保存**"。

   ![選擇允許 Microsoft 受信任服務的異常](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. 在邏輯應用的設置中，[啟用對託管標識的支援](../logic-apps/create-managed-service-identity.md)。

1. 在邏輯應用的工作流中，添加和設置 HTTP 操作或觸發器以訪問存儲帳戶或實體。

   > [!IMPORTANT]
   > 對於傳出 HTTP 操作或對 Azure 存儲帳戶的觸發器調用，請確保請求標頭`x-ms-version`包含要在存儲帳戶上運行的操作的屬性和 API 版本。 有關詳細資訊，請參閱[使用託管標識進行身份驗證存取權限](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)和[Azure 存儲服務的版本化](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)。

1. 在此操作[中，選擇要用於身份驗證的託管標識](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>通過 Azure API 管理訪問存儲帳戶

如果使用 API[管理的](../api-management/api-management-key-concepts.md)專用層，則可以使用 API 管理和允許後者的 IP 位址通過防火牆在存儲 API 前面。 基本上，將 API 管理使用的 Azure 虛擬網路添加到存儲帳戶的防火牆設置中。 然後，可以使用 API 管理操作或 HTTP 操作調用 Azure 存儲 API。 但是，如果選擇此選項，您必須自己處理身份驗證過程。 如需詳細資訊，請參閱[簡單的企業整合架構](https://aka.ms/aisarch)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
