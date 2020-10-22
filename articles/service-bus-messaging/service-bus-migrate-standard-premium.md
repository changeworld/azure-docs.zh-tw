---
title: 遷移 Azure 服務匯流排命名空間-standard 至 premium
description: 允許將現有的 Azure 服務匯流排標準命名空間遷移至 premium 的指南
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "85340749"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>將現有的 Azure 服務匯流排標準命名空間遷移至 premium 層

先前，Azure 服務匯流排只在標準層提供命名空間。 命名空間是針對低輸送量和開發人員環境優化的多租使用者設置。 進階層針對可預測的延遲和以固定價格增加的輸送量，提供每個命名空間的專用資源。 進階層最適合需要額外企業功能的高輸送量和生產環境。

本文說明如何將現有的標準層命名空間遷移至 premium 層。  

>[!WARNING]
> 遷移適用于將服務匯流排標準命名空間升級為高階層。 遷移工具不支援降級。

要注意的一些重點：

- 這項遷移的目的是要進行，這表示現有的傳送者和接收者應用程式 **不需要對程式碼或設定進行任何變更**。 現有的連接字串會自動指向新的 premium 命名空間。
- **Premium**命名空間應該沒有**任何實體**可讓遷移成功。
- 標準命名空間中的所有 **實體** 都會在遷移過程中 **複製** 到 premium 命名空間。
- 在進階層中，遷移支援 **每個訊息單位1000個實體** 。 若要識別您需要多少個訊息單位，請從您目前的標準命名空間上的實體數目開始。
- 您無法直接從 **基本層** 遷移至進階層，但您可以在下一個步驟 **中，從**「基本」層首次遷移至「標準」和「高階」以間接執行。

## <a name="migration-steps"></a>移轉步驟

某些條件與遷移程式相關聯。 請熟悉下列步驟，以降低發生錯誤的可能性。 這些步驟概述了遷移程式，接下來的各節會列出逐步詳細資料。

1. 建立新的 premium 命名空間。
1. 將 standard 和 premium 命名空間與其他命名空間配對。
1. 將 (複製) 實體從標準同步處理至 premium 命名空間。
1. 認可遷移。
1. 使用命名空間的後置遷移名稱，清空標準命名空間中的實體。
1. 刪除標準命名空間。

>[!IMPORTANT]
> 認可遷移之後，請存取舊的標準命名空間，並清空佇列和訂閱。 在訊息已清空之後，可能會傳送至新的 premium 命名空間，以供接收者應用程式處理。 清空佇列和訂閱之後，建議您刪除舊的標準命名空間。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 進行遷移

若要使用 Azure CLI 或 PowerShell 工具將您的服務匯流排標準命名空間遷移至 premium，請遵循下列步驟。

1. 建立新的服務匯流排 premium 命名空間。 您可以參考 [Azure Resource Manager 範本](service-bus-resource-manager-namespace.md) 或 [使用 Azure 入口網站](service-bus-create-namespace-portal.md)。 請務必為**serviceBusSku**參數選取**premium** 。

1. 設定下列環境變數，以簡化遷移命令。

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 遷移後的別名/名稱 (post_migration_dns_name) 將用來存取舊的標準命名空間。 使用此來清空佇列和訂閱，然後刪除命名空間。

1. 使用下列命令，將 standard 和 premium 命名空間配對，並開始同步處理：

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 使用下列命令來檢查遷移的狀態：

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    當您看到下列值時，就會將遷移視為已完成：

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "Succeeded"

    此命令也會顯示遷移設定。 檢查以確定值已正確設定。 此外，請在入口網站中檢查 premium 命名空間，以確保所有佇列和主題都已建立，且符合標準命名空間中的存在。

1. 執行下列完整命令來認可遷移：

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 入口網站遷移

使用 Azure 入口網站的遷移與使用命令進行遷移的邏輯流程相同。 請遵循下列步驟，使用 Azure 入口網站來進行遷移。

1. 在左窗格的 **導覽** 功能表上，選取 [ **遷移至 premium**]。 按一下 [ **開始** ] 按鈕以繼續前往下一個頁面。
    ![遷移登陸頁面][]

1. 完成 **安裝**。
   ![安裝命名空間][]
   1. 建立並指派 premium 命名空間，以將現有的標準命名空間遷移至。
        ![安裝命名空間-建立 premium 命名空間][]
   1. 選擇 **遷移後的名稱**。 完成遷移之後，您將使用此名稱來存取標準命名空間。
        ![設定命名空間-選擇遷移後名稱][]
   1. 選取 **[下一步]** 以繼續。
1. 在 standard 和 premium 命名空間之間同步實體。
    ![設定命名空間-同步實體-開始][]

   1. 選取 [ **開始同步** 處理] 開始同步處理實體。
   1. 在對話方塊中選取 **[是** ]，以確認並開始同步處理。
   1. 等候同步處理完成。 狀態會出現在狀態列上。
        ![設定命名空間-同步處理實體-進度][]
        >[!IMPORTANT]
        > 如果您因為任何原因而需要中止遷移，請參閱本檔的常見問題一節中的中止流程。
   1. 同步完成之後，請選取頁面底部的 **[下一步]** 。

1. 查看摘要頁面上的變更。 選取 [ **完成遷移** ] 以切換命名空間，並完成遷移。
    ![切換命名空間-切換功能表][]  
    完成遷移時，會出現 [確認] 頁面。
    ![切換命名空間-成功][]

## <a name="caveats"></a>警示

Azure 服務匯流排 Premium 層不支援 Azure 服務匯流排標準層提供的某些功能。 因為進階層提供可預測的輸送量和延遲的專用資源，所以這些都是設計的。

以下是 Premium 和其緩和功能不支援的功能清單-

### <a name="express-entities"></a>快速實體

   Premium 不支援將任何訊息資料認可至儲存體的快速實體。 專用資源提供大幅改善的輸送量，同時確保保存資料，就像是任何企業訊息系統預期的一樣。

   在遷移期間，您的標準命名空間中的任何 express 實體都會在 Premium 命名空間中建立為非快速實體。

   如果您利用 Azure Resource Manager (ARM) 範本，請確定您已從部署設定中移除 ' >enableexpress ' 旗標，以便在不發生錯誤的情況下執行自動化工作流程。

### <a name="partitioned-entities"></a>分割的實體

   標準層中支援分割實體，以在多租使用者設定中提供更好的可用性。 在進階層中，每個命名空間都有提供專屬的可用資源，就不再需要此功能。

   在遷移期間，標準命名空間中的任何資料分割實體都會在 Premium 命名空間上建立為非資料分割實體。

   如果您的 ARM 範本將特定佇列或主題的 ' >queuedescription.enablepartitioning ' 設定為 ' true '，則訊息代理程式將會忽略它。

## <a name="faqs"></a>常見問題集

### <a name="what-happens-when-the-migration-is-committed"></a>認可遷移時會發生什麼事？

認可遷移之後，指向標準命名空間的連接字串將會指向 premium 命名空間。

傳送者和接收者應用程式會中斷與標準命名空間的連線，並自動重新連接至 premium 命名空間。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>標準至 premium 遷移完成後該怎麼辦？

標準至 premium 的遷移可確保將主題、訂用帳戶和篩選等實體中繼資料從標準命名空間複製到 premium 命名空間。 認可至標準命名空間的訊息資料不會從標準命名空間複製到 premium 命名空間。

在進行遷移時，標準命名空間可能會有一些傳送和認可的訊息。 從標準命名空間手動清空這些訊息，並將它們手動傳送至 premium 命名空間。 若要手動清空訊息，請使用主控台應用程式或腳本，以使用您在遷移命令中指定的後置遷移 DNS 名稱來清空標準命名空間實體。 將這些訊息傳送至 premium 命名空間，讓接收者可以處理這些訊息。

清空訊息之後，刪除標準命名空間。

>[!IMPORTANT]
> 清空標準命名空間的訊息之後，請刪除標準命名空間。 這點很重要，因為一開始參考標準命名空間的連接字串，現在會參考 premium 命名空間。 您不再需要標準命名空間。 刪除您所遷移的標準命名空間有助於減少後續的混淆。

### <a name="how-much-downtime-do-i-expect"></a>預期的停機時間有多少？

遷移程式旨在減少應用程式的預期停機時間。 使用傳送者和接收者應用程式指向新的 premium 命名空間的連接字串，可減少停機時間。

應用程式遇到的停機時間僅限於更新 DNS 專案以指向 premium 命名空間所花的時間。 停機時間大約是5分鐘。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>是否必須在進行遷移時進行任何設定變更？

否，不需要變更任何程式碼或設定，就可以進行遷移。 傳送者和接收者應用程式用來存取標準命名空間的連接字串會自動對應，作為 premium 命名空間的別名。

### <a name="what-happens-when-i-abort-the-migration"></a>當我中止遷移時，會發生什麼事？

您可以使用 `Abort` 命令或使用 Azure 入口網站來中止遷移。

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 入口網站

![中止流程-中止同步 ][]
 ![ 中止流程-中止完成][]

當遷移程式中止時，它會中止複製實體 (主題、訂用帳戶和篩選) 從標準移至 premium 命名空間的程式，並中斷配對。

連接字串不會更新以指向 premium 命名空間。 您現有的應用程式在開始進行遷移之前，會繼續運作。

不過，它不會刪除高階命名空間上的實體，也不會刪除 premium 命名空間。 如果您決定不要繼續進行遷移，請手動刪除實體。

>[!IMPORTANT]
> 如果您決定中止遷移，請刪除您為遷移所布建的 premium 命名空間，如此就不會向您收取資源的費用。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想要清空訊息。 該怎麼辦？

在進行遷移時，或在認可認可之前，傳送者應用程式可能會傳送訊息並認可至標準命名空間上的儲存體。

在遷移期間，不會將實際的訊息資料/承載從標準複製到 premium 命名空間。 必須手動清空訊息，然後再傳送至 premium 命名空間。

但是，如果您可以在預定的維護/維護期間進行遷移，而不想要手動清空和傳送訊息，請遵循下列步驟：

1. 停止寄件者應用程式。 接收者應用程式將會處理目前在標準命名空間中的訊息，並將佇列清空。
1. 當標準命名空間中的佇列和訂用帳戶是空的之後，請遵循稍早所述的程式，執行從標準至 premium 命名空間的遷移。
1. 完成遷移之後，您可以重新開機傳送者應用程式。
1. 傳送者和接收者現在將會自動與 premium 命名空間連接。

    >[!NOTE]
    > 您不需要停止接收者應用程式來進行遷移。
    >
    > 完成遷移之後，接收者應用程式會中斷與標準命名空間的連線，並自動連接到 premium 命名空間。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [標準與 Premium 訊息之間的差異](./service-bus-premium-messaging.md)。
* 瞭解 [服務匯流排 premium 的高可用性和 Geo-Disaster 復原層面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

[遷移登陸頁面]: ./media/service-bus-standard-premium-migration/1.png
[安裝命名空間]: ./media/service-bus-standard-premium-migration/2.png
[安裝命名空間-建立 premium 命名空間]: ./media/service-bus-standard-premium-migration/3.png
[設定命名空間-選擇遷移後名稱]: ./media/service-bus-standard-premium-migration/4.png
[設定命名空間-同步實體-開始]: ./media/service-bus-standard-premium-migration/5.png
[設定命名空間-同步處理實體-進度]: ./media/service-bus-standard-premium-migration/8.png
[切換命名空間-切換功能表]: ./media/service-bus-standard-premium-migration/9.png
[切換命名空間-成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流程-中止同步處理]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流程-中止完成]: ./media/service-bus-standard-premium-migration/abort3.png
