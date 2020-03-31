---
title: 遷移 Azure 服務匯流排命名空間 - 標準到高級
description: 允許將現有 Azure 服務匯流排標準命名空間遷移到高級
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 27e3260b91bebee14ff12188a7dbd6c7cf76355c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385022"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>將現有的 Azure 服務匯流排標準命名空間遷移到高級層

以前，Azure 服務匯流排僅在標準層上提供命名空間。 命名空間是針對低輸送量和開發人員環境優化的多租戶設置。 高級層為每個命名空間提供專用資源，以提供可預測的延遲和以固定價格提高輸送量。 高級層針對需要其他企業功能的高輸送量和生產環境進行了優化。

本文介紹如何將現有標準層命名空間遷移到高級層。  

>[!WARNING]
> 遷移用於將服務匯流排標準命名空間升級到高級層。 遷移工具不支援降級。

需要注意的一些要點：

- 此遷移旨在就地進行，這意味著現有的發送方和接收方應用程式**不需要對代碼或配置進行任何更改**。 現有連接字串將自動指向新的高級命名空間。
- **高級**命名空間中不應有**實體**，以便遷移成功。
- 在遷移過程中，標準命名空間中的所有**實體**都將**複製到**高級命名空間。
- 遷移支援高級層**上每個郵件單元 1，000 個實體**。 要確定所需的消息傳遞單元數，請從當前標準命名空間上的實體數開始。
- 不能直接從**基本層**遷移到**高級層**，但可以通過先從基本層遷移到標準，然後在下一步從標準遷移到高級層來間接遷移。

## <a name="migration-steps"></a>移轉步驟

某些條件與遷移過程相關聯。 熟悉以下步驟，以減少出錯的可能性。 這些步驟概述了遷移過程，後續詳細資訊將列在以下各節中。

1. 創建新的高級命名空間。
1. 將標準和高級命名空間彼此配對。
1. 將（複製）實體從標準同步到高級命名空間。
1. 提交遷移。
1. 通過使用命名空間的遷移後名稱來排空標準命名空間中的實體。
1. 刪除標準命名空間。

>[!IMPORTANT]
> 提交遷移後，訪問舊標準命名空間並耗盡佇列和訂閱。 消息被排空後，它們可能會發送到新的高級命名空間，由接收方應用程式處理。 排空佇列和訂閱後，我們建議您刪除舊的標準命名空間。

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 進行遷移

要使用 Azure CLI 或 PowerShell 工具將服務匯流排標準命名空間遷移到高級，請按照以下步驟操作。

1. 創建新的服務匯流排高級命名空間。 可以引用[Azure 資源管理器範本](service-bus-resource-manager-namespace.md)或使用[Azure 門戶](service-bus-create-namespace-portal.md)。 請務必為**服務 BusSku**參數選擇**高級**。

1. 設置以下環境變數以簡化遷移命令。

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > 遷移後別名/名稱（post_migration_dns_name）將用於訪問遷移後的舊標準命名空間。 使用此選項可排空佇列和訂閱，然後刪除命名空間。

1. 配對標準和高級命名空間，並使用以下命令啟動同步：

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. 使用以下命令檢查遷移的狀態：

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    當您看到以下值時，遷移被視為已完成：

    * 遷移狀態 = "活動"
    * 掛起複製操作計數 = 0
    * 預配狀態 = "成功"

    此命令還顯示遷移配置。 檢查以確保正確設置值。 還要檢查門戶中的高級命名空間，以確保已創建所有佇列和主題，並確保它們與標準命名空間中存在的內容匹配。

1. 通過執行以下完整命令提交遷移：

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>使用 Azure 門戶進行遷移

使用 Azure 門戶進行遷移與使用命令遷移具有相同的邏輯流。 按照以下步驟使用 Azure 門戶進行遷移。

1. 在左側窗格中的 **"導航"** 功能表上，選擇 **"遷移到高級**"。 按一下"**開始"** 按鈕以繼續下一頁。
    ![遷移登錄頁][]

1. 完成**設置**。
   ![設置命名空間][]
   1. 創建並分配高級命名空間以將現有標準命名空間遷移到 。
        ![設置命名空間 - 創建高級命名空間][]
   1. 選擇**遷移後名稱**。 遷移完成後，您將使用此名稱訪問標準命名空間。
        ![設置命名空間 - 選取遷移後名稱][]
   1. 選擇 **"下一步"** 以繼續。
1. 在標準命名空間和高級命名空間之間同步實體。
    ![設置命名空間 - 同步實體 - 啟動][]

   1. 選擇 **"開始同步"** 以開始同步實體。
   1. 在對話方塊中選擇 **"是**"以確認並啟動同步。
   1. 等待同步完成。 狀態列上提供狀態。
        ![設置命名空間 - 同步實體 - 進度][]
        >[!IMPORTANT]
        > 如果您需要出於任何原因中止遷移，請查看本文檔的常見問題部分中的中止流。
   1. 同步完成後，在頁面底部選擇 **"下一步**"。

1. 查看摘要頁上的更改。 選擇 **"完成遷移**"以切換命名空間並完成遷移。
    ![切換命名空間 - 切換功能表][]  
    遷移完成後，將顯示確認頁。
    ![切換命名空間 - 成功][]

## <a name="caveats"></a>警示

Azure 服務匯流排高級層不支援 Azure 服務匯流排標準層提供的某些功能。 這些是設計，因為高級層提供用於可預測的輸送量和延遲的專用資源。

下面是高級版不支援的功能及其緩解措施的清單 -

### <a name="express-entities"></a>快速實體

   高級版不支援不向存儲提交任何消息資料的快速實體。 專用資源提供了顯著的輸送量改進，同時確保資料持久化，正如任何企業郵件系統的預期的那樣。

   在遷移期間，標準命名空間中的任何快速實體都將在高級命名空間上創建為非快遞實體。

   如果使用 Azure 資源管理器 （ARM） 範本，請確保從部署配置中刪除"enableExpress"標誌，以便自動工作流執行時沒有錯誤。

### <a name="partitioned-entities"></a>分區實體

   標準層支援分區實體，以在多租戶設置中提供更好的可用性。 由於高級層中每個命名空間提供專用資源，因此不再需要這樣做。

   在遷移期間，標準命名空間中的任何分區實體都作為非分區實體在高級命名空間上創建。

   如果您的 ARM 範本將"啟用分區"設置為特定佇列或主題的"true"，則代理將忽略它。

## <a name="faqs"></a>常見問題集

### <a name="what-happens-when-the-migration-is-committed"></a>提交遷移時會發生什麼情況？

提交遷移後，指向標準命名空間的連接字串將指向高級命名空間。

發送方和接收方應用程式將斷開與標準命名空間的連接，並自動重新連接到高級命名空間。

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>標準到高級遷移完成後我該怎麼辦？

標準到高級遷移可確保實體中繼資料（如主題、訂閱和篩選器）從標準命名空間複製到高級命名空間。 提交到標準命名空間的消息資料不會從標準命名空間複製到高級命名空間。

標準命名空間可能具有一些在遷移進行期間發送和提交的消息。 手動從標準命名空間排空這些消息，並手動將它們發送到高級命名空間。 要手動排空消息，請使用主控台應用或腳本，使用您在遷移命令中指定的遷移後 DNS 名稱耗盡標準命名空間實體。 將這些消息發送到高級命名空間，以便接收方可以處理它們。

排空消息後，刪除標準命名空間。

>[!IMPORTANT]
> 排空來自標準命名空間的消息後，刪除標準命名空間。 這一點很重要，因為最初引用標準命名空間的連接字串現在引用高級命名空間。 您不再需要標準命名空間了。 刪除您遷移的標準命名空間有助於減少以後的混亂。

### <a name="how-much-downtime-do-i-expect"></a>我預計停機多少？

遷移過程旨在減少應用程式的預期停機時間。 通過使用發送方和接收方應用程式用來指向新的高級命名空間的連接字串，減少了停機時間。

應用程式經歷的停機時間僅限於更新 DNS 條目以指向高級命名空間所需的時間。 停機時間約為 5 分鐘。

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>執行遷移時，我是否要進行任何配置更改？

否，無需執行遷移所需的代碼或配置更改。 發送方和接收方應用程式用於訪問標準命名空間的連接字串將自動映射為高級命名空間的別名。

### <a name="what-happens-when-i-abort-the-migration"></a>中止遷移時會發生什麼情況？

可以通過使用`Abort`命令或使用 Azure 門戶中止遷移。

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure 入口網站

![中止流 -][]
![中止同步中止流 - 中止完成][]

中止遷移過程後，它將中止將實體（主題、訂閱和篩選器）從標準複製到高級命名空間的過程，並中斷配對。

連接字串不會更新以指向高級命名空間。 現有應用程式將繼續工作，就像開始遷移之前一樣。

但是，它不會刪除高級命名空間上的實體或刪除高級命名空間。 如果您決定不繼續遷移，請手動刪除實體。

>[!IMPORTANT]
> 如果決定中止遷移，請刪除已為遷移預配的高級命名空間，以便不向資源收費。

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>我不想把消息排幹。 該怎麼辦？

在遷移進行期間和提交遷移之前，可能有一些消息由寄件者應用程式發送並提交到標準命名空間上的存儲。

在遷移期間，實際的消息資料/有效負載不會從標準複製到高級命名空間。 必須手動排空消息，然後發送到高級命名空間。

但是，如果您在計畫維護/內務管理時段內可以遷移，並且不希望手動排空和發送消息，請按照以下步驟操作：

1. 停止寄件者應用程式。 接收方應用程式將處理當前位於標準命名空間中的消息，並將耗盡佇列。
1. 在標準命名空間中的佇列和訂閱為空後，請按照前面描述的過程執行從標準到高級命名空間的遷移。
1. 遷移完成後，可以重新開機寄件者應用程式。
1. 發送方和接收方現在將自動與高級命名空間連接。

    >[!NOTE]
    > 您不必停止遷移的接收方應用程式。
    >
    > 遷移完成後，接收器應用程式將斷開與標準命名空間的連接，並自動連接到高級命名空間。

## <a name="next-steps"></a>後續步驟

* 詳細瞭解[標準消息和高級消息之間的差異](./service-bus-premium-messaging.md)。
* 瞭解[服務匯流排高級的高可用性和地質災害恢復方面](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)。

[遷移登錄頁]: ./media/service-bus-standard-premium-migration/1.png
[設置命名空間]: ./media/service-bus-standard-premium-migration/2.png
[設置命名空間 - 創建高級命名空間]: ./media/service-bus-standard-premium-migration/3.png
[設置命名空間 - 選取遷移後名稱]: ./media/service-bus-standard-premium-migration/4.png
[設置命名空間 - 同步實體 - 啟動]: ./media/service-bus-standard-premium-migration/5.png
[設置命名空間 - 同步實體 - 進度]: ./media/service-bus-standard-premium-migration/8.png
[切換命名空間 - 切換功能表]: ./media/service-bus-standard-premium-migration/9.png
[切換命名空間 - 成功]: ./media/service-bus-standard-premium-migration/12.png

[中止流 - 中止同步]: ./media/service-bus-standard-premium-migration/abort1.png
[中止流 - 中止完成]: ./media/service-bus-standard-premium-migration/abort3.png
