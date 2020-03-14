---
title: Azure 中的資料保護串流分析
description: 本文說明如何加密 Azure 串流分析作業所使用的私用資料。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299391"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 中的資料保護串流分析 

Azure 串流分析是完全受控的平臺即服務，可讓您建立即時分析管線。 所有繁重的工作，例如叢集布建、調整節點以配合您的使用，以及管理內部檢查點，都是在幕後進行管理。

## <a name="encrypt-your-data"></a>加密您的資料

串流分析會自動在其基礎結構中採用最佳的加密標準，以加密和保護您的資料。 您可以直接信任串流分析以安全地儲存您的所有資料，讓您不必擔心如何管理基礎結構。

如果您想要使用客戶管理的金鑰（CMK）來加密資料，您可以使用自己的儲存體帳戶（一般用途 V1 或 V2）來儲存串流分析執行時間所需的任何私用資料資產。 您的儲存體帳戶可以視需要進行加密。 串流分析基礎結構不會永久儲存任何私人資料資產。 

這項設定必須在建立串流分析作業時設定，而且在整個作業的生命週期中都無法修改。 不建議修改或刪除串流分析所使用的儲存體。 如果您刪除儲存體帳戶，將會永久刪除所有私人資料資產，而這會導致您的作業失敗。 

不可能使用串流分析入口網站來更新或輪替儲存體帳戶的金鑰。 您可以使用 REST Api 來更新金鑰。


## <a name="configure-storage-account-for-private-data"></a>設定私人資料的儲存體帳戶 

使用下列步驟來設定私人資料資產的儲存體帳戶。 此設定是從您的串流分析作業進行，而不是從您的儲存體帳戶進行。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 Azure 入口網站左上角的 [建立資源]。 

1. 從結果清單中選取  **分析** > **串流分析作業** 。 

1. 在 [串流分析作業] 頁面中填入必要的詳細資料，例如名稱、區域和尺規。 

1. 選取 [*在我的儲存體帳戶中保護此作業所需的所有私用資料資產*] 核取方塊。

1. 從您的訂用帳戶中選取儲存體帳戶。 請注意，這項設定無法在作業的整個生命週期中修改。 

   ![私用資料儲存體帳戶設定](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>儲存的私用資料資產

串流分析所需保存的任何私用資料都會儲存在您的儲存體帳戶中。 私人資料資產的範例包括： 

* 您已撰寫的查詢及其相關設定  

* 使用者自訂函數 

* 串流分析執行時間所需的檢查點

* 參考資料的快照集 

您的資源的連線詳細資料（由您的串流分析作業所使用）也會一併儲存。 將您的儲存體帳戶加密，以保護您的所有資料。 

為了協助您符合任何受管制產業或環境中的合規性義務，您可以閱讀更多有關[Microsoft 合規性供應](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)專案的資訊。 

## <a name="next-steps"></a>後續步驟

* [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)
* [瞭解 Azure 串流分析的輸入](stream-analytics-add-inputs.md)
* [Azure 串流分析作業中的檢查點和重新執行概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用參考資料在串流分析中進行查閱](stream-analytics-use-reference-data.md)
