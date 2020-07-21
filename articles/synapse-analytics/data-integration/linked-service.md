---
title: 保護連結服務
description: 瞭解如何布建及保護使用受控 Vnet 的連結服務
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7c7cf8ec7297b3c0ef855936becc3c06a5120a38
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496072"
---
# <a name="securing-a-linked-service-with-private-links"></a>使用私人連結保護連結服務 

在本文中，您將瞭解如何使用私人端點來保護 Synapse 中的連結服務。

## <a name="prerequisites"></a>先決條件

* **Azure 訂**用帳戶：如果您沒有 azure 訂用帳戶，請在開始前建立[免費的 azure 帳戶](https://azure.microsoft.com/free/)。
* **Azure 儲存體帳戶**：您可以使用 Azure Data Lake Gen 2 做為*源*資料存放區。 如果您沒有儲存體帳戶，請參閱[建立 Azure 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)以取得建立步驟。 請確定儲存體帳戶具有 Synapse Studio IP 篩選來進行存取，且您只允許**選取的網路**存取儲存體帳戶。 [Blade**防火牆] 和 [虛擬網路**] 底下的設定看起來應該如下圖所示。

![受保護的儲存體帳戶](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>建立具有私人連結的連結服務

在 Azure Synapse 分析中，已連結的服務可讓您定義其他服務的連接資訊。 在本節中，您會將 Azure Synapse 分析和 Azure Data Lake Gen 2 新增為連結服務。

1. 開啟 Azure Synapse Studio 並移至 [**管理**] 索引標籤。
1. 在 [**外部連接**] 底下，選取 [**連結服務**]。
1. 若要加入連結服務，請按一下 [**新增**]。
1. 從清單中選取 [Azure Data Lake Storage Gen2] 磚，然後按一下 [**繼續**]。
1. 請務必啟用**互動式撰寫**。 可能需要約1分鐘的時間才能啟用。 
1. 輸入您的驗證認證。 帳戶金鑰、服務主體和受控識別目前支援的驗證類型。 按一下 [測試連接] 以確認您的認證正確無誤。
1. 選取 [**測試**連線]，它應該會失敗，因為儲存體帳戶不會在建立和核准私用端點的情況下啟用存取權。 在錯誤訊息中，您應該會看到用來建立**私人端點**的連結，讓您可以遵循以移至下一個部分。 如果您遵循該連結，請略過下一個部分。
1. 完成後，請選取 [建立]****。

## <a name="create-a-managed-private-endpoint"></a>建立受控私用端點

如果您在測試上述連接時未按下超連結，請遵循下列路徑。 現在您需要建立受控私用端點，以連線至上面所建立的連結服務。

1. 移至 [**管理**] 索引標籤。
1. 移至 [**受管理的虛擬網路**] 區段。
1. 選取 [受控私人端點] 底下的 [ **+ 新增**]。
1. 從清單中選取 [Azure Data Lake Storage Gen2] 磚，然後選取 [**繼續**]。
1. 輸入您在上面建立的儲存體帳戶名稱。
1. 選取 [建立] 
1. 在等候幾秒鐘之後，您應該會看到建立的私用連結需要核准。

## <a name="approval-of-a-private-link"></a>私人連結的核准
1. 選取您在上面建立的私用端點。 您可以看到超連結，可讓您在儲存體帳戶層級核准私人端點。 *另一個替代方式是直接前往 Azure 入口網站儲存體帳戶，然後進入 [**私人端點連接**] 分頁。*
1. 勾選您在 Studio 中建立的私用端點，然後選取 [**核准**]。
1. 新增描述，然後按一下 **[是]**
1. 回到 [**管理**] 索引標籤的 [**受管理的虛擬網路**] 區段下的 [Synapse Studio]。
1. 這應該需要大約1分鐘的時間，才能為您的私用端點反映出核准。

## <a name="check-the-connection-works"></a>檢查連接是否正常運作
1. 移至 [**管理**] 索引標籤，然後選取您所建立的連結服務。
1. 請確定**互動式撰寫**作用中。
1. 選取 [測試連線]。 您應該會看到連接成功。

您現在已在 Synapse 與您的連結服務之間建立安全且私人的連接！

## <a name="next-steps"></a>後續步驟

若要進一步瞭解 Synapse 分析中的受控私用端點，請參閱[Synapse 受控私用端點](data-integration-data-lake.md)一文中的概念。

如需 Synapse 分析之資料整合的詳細資訊，請參閱將[資料內嵌到 Data Lake 一](data-integration-data-lake.md)文。
