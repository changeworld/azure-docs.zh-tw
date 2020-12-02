---
title: 保護已連結的服務
description: 瞭解如何使用受控 VNet 布建和保護連結服務
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fb3c7dd7b3d7e8e42e230eedcd0d1b43af104327
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452523"
---
# <a name="secure-a-linked-service-with-private-links"></a>使用私用連結保護連結的服務

在本文中，您將瞭解如何使用私人端點來保護 Synapse 中的連結服務。

## <a name="prerequisites"></a>必要條件

* **Azure 訂** 用帳戶：如果您沒有 azure 訂用帳戶，請在開始前建立 [免費的 azure 帳戶](https://azure.microsoft.com/free/) 。
* **Azure 儲存體帳戶**：您使用 Azure Data Lake Gen 2 做為 *源* 資料存放區。 如果您沒有儲存體帳戶，請參閱 [建立 Azure 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md) 以取得建立帳戶的步驟。 請確定儲存體帳戶具有 Synapse Studio IP 篩選可供存取，而且您只允許 **選取的網路** 存取儲存體帳戶。 分頁 **防火牆和虛擬網路** 下的設定看起來應該如下圖所示。

![受保護的儲存體帳戶](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>建立具有私用連結的連結服務

在 Azure Synapse Analytics 中，連結服務可讓您定義其他服務的連線資訊。 在本節中，您會將 Azure Synapse Analytics 和 Azure Data Lake Gen 2 新增為連結服務。

1. 開啟 Azure Synapse Studio，然後移至 [ **管理** ] 索引標籤。
1. 在 [ **外部連接**] 底下，選取 [已 **連結的服務**]。
1. 若要新增連結服務，請選取 [新增]。
1. 從清單中選取 Azure Data Lake Storage Gen2 圖格，然後選取 [ **繼續**]。
1. 請務必啟用 **互動式撰寫**。 可能需要約 1 分鐘的時間才能啟用。 
1. 輸入您的驗證認證。 帳戶金鑰、服務主體和受控識別目前支援的驗證類型。 選取 [測試連接] 以確認您的認證正確無誤。
1. 選取 [ **測試** 連線]，這應該會失敗，因為儲存體帳戶不會啟用存取權，而不需要建立和核准私人端點。 在錯誤訊息中，您應該會看到用來建立 **私人端點** 的連結，您可以遵循此連結來移至下一個部分。 如果您遵循該連結，請略過下一個部分。
1. 完成後，請選取 [建立]。

## <a name="create-a-managed-private-endpoint"></a>建立受控私人端點

如果您在測試上述連線時未選取至超連結，請遵循下列路徑。 建立受控私人端點，您將連接到上述建立的連結服務。

1. 移至 **管理** 索引標籤。
1. 移至 [ **受管理的虛擬網路** ] 區段。
1. 選取 [受管理的私人端點] 底下的 [ **+ 新增** ]。
1. 從清單中選取 Azure Data Lake Storage Gen2 圖格，然後選取 [ **繼續**]。
1. 輸入先前建立的儲存體帳戶名稱。
1. 選取 [建立] 
1. 在等候幾秒鐘之後，您應該會看到建立的私人連結需要核准。

## <a name="private-link-approval"></a>私人連結核准
1. 選取之前建立的私人端點。 您可以看到超連結，可讓您在儲存體帳戶層級核准私人端點。 *替代方式是直接移至 Azure 入口網站儲存體帳戶，並移至 [ **私人端點** 連線] 分頁。*
1. 勾選您在 Studio 中建立的私人端點，然後選取 [ **核准**]。
1. 新增描述並選取 **[是]**
1. 在 [**管理**] 索引標籤的 [**受管理的虛擬網路**] 區段下，返回至 Synapse Studio。
1. 大約需要1分鐘的時間，才能讓您的私人端點反映核准。

## <a name="check-the-connection-works"></a>檢查連接是否正常運作
1. 移至 [ **管理** ] 索引標籤，然後選取您所建立的連結服務。
1. 請確定 **互動式撰寫** 正在使用中。
1. 選取 [測試連線]。 您應該會看到連接成功。

您現在已建立 Synapse 與連結服務之間的安全和私人連線。

## <a name="next-steps"></a>後續步驟


若要進一步瞭解 Azure Synapse Analytics 中的 Managed 私用端點，請參閱 [受控私人端點](../security/synapse-workspace-managed-private-endpoints.md)。


如需有關 Azure Synapse Analytics 資料整合的詳細資訊，請參閱 [擷取資料到 Data Lake](data-integration-data-lake.md) 文章。
