---
title: Azure 串流分析中的資料保護
description: 本文說明如何加密 Azure 串流分析作業所使用的私用資料。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4436289d544de057acef132117346ac53c20b5a7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576485"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure 串流分析中的資料保護 

Azure 串流分析是完全受控的平臺即服務，可讓您建立即時分析管線。 所有繁重的工作（例如叢集布建、調整節點以容納您的使用量，以及管理內部檢查點）都會在幕後進行管理。

## <a name="private-data-assets-that-are-stored"></a>儲存的私用資料資產

Azure 串流分析會保存下列中繼資料和資料，以便執行： 

* 查詢定義及其相關設定  

* 使用者定義函數或匯總  

* 串流分析執行時間所需的檢查點

* 參考資料的快照集 

* 串流分析作業所使用資源的連接詳細資料

為了協助您符合任何管制產業或環境中的合規性義務，您可以閱讀更多有關 [Microsoft 合規性供應](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)專案的資訊。 

## <a name="in-region-data-residency"></a>In-Region 資料常駐
Azure 串流分析會儲存客戶資料和以上所述的其他中繼資料。 依預設，Azure 串流分析會將客戶資料儲存在單一區域中，因此這項服務會自動滿足區域資料落地需求（包括 [信任中心](https://azuredatacentermap.azurewebsites.net/)內指定的需求）。
此外，您可以選擇將所有資料資產儲存 (客戶資料，以及在單一區域中的串流分析作業) 相關的其他中繼資料，方法是在您選擇的儲存體帳戶中加密它們。

## <a name="encrypt-your-data"></a>將資料進行加密

串流分析會自動在其基礎結構中採用最佳的加密標準，以加密及保護您的資料。 您可以直接信任串流分析以安全地儲存您的所有資料，讓您不必擔心如何管理基礎結構。

如果您想要使用客戶管理的金鑰來加密您的資料，您可以使用自己的儲存體帳戶 (一般用途 V1 或 V2) 來儲存串流分析執行時間所需的任何私用資料資產。 您可以視需要加密您的儲存體帳戶。 串流分析基礎結構不會永久儲存任何私用資料資產。 

這項設定必須在串流分析作業建立時設定，而且無法在整個作業的生命週期中修改。 不建議修改或刪除串流分析所使用的儲存體。 如果您刪除儲存體帳戶，將會永久刪除所有私用資料資產，這會導致您的作業失敗。 

您無法使用串流分析入口網站來更新或輪替儲存體帳戶的金鑰。 您可以使用 REST Api 來更新金鑰。


### <a name="configure-storage-account-for-private-data"></a>設定私人資料的儲存體帳戶 

加密您的儲存體帳戶，以保護您的所有資料，並明確地選擇私用資料的位置。 

為了協助您符合任何管制產業或環境中的合規性義務，您可以閱讀更多有關 [Microsoft 合規性供應](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)專案的資訊。 

使用下列步驟來設定私人資料資產的儲存體帳戶。 這項設定是從您的串流分析作業所建立，而不是從您的儲存體帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 Azure 入口網站左上角的 [建立資源]  。 

1.  **Analytics**   > 從結果清單中選取分析 **串流分析作業**   。 

1. 在 [串流分析作業] 頁面中填寫必要的詳細資料，例如名稱、區域和調整。 

1. 選取 [ *在我的儲存體帳戶中保護此作業所需的所有私人資料資產*] 的核取方塊。

1. 從您的訂用帳戶中選取儲存體帳戶。 請注意，這項設定無法在作業的整個生命週期中修改。 一旦建立作業之後，您也無法新增此選項。

1. 若要使用連接字串進行驗證，請從 [驗證模式] 下拉式清單中選取 [ **連接字串** ]。 系統會自動從您的訂用帳戶填入儲存體帳戶金鑰。

   ![私用資料儲存體帳戶設定](./media/data-protection/storage-account-create.png)

1. 若要使用受控識別進行驗證 (預覽) ，請從 [驗證模式] 下拉式清單中選取 [ **受控識別** ]。 如果您選擇受控識別，您需要將串流分析作業新增至儲存體帳戶的存取控制清單。 如果您未提供作業存取權，則工作將無法執行任何作業。 如需如何授與存取權的詳細資訊，請參閱 [使用 AZURE RBAC 將受控識別存取權指派給另一個資源](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource)。

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="使用受控識別驗證的私用資料儲存體帳戶設定":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>串流分析儲存的私用資料資產

串流分析所需保存的任何私用資料都會儲存在您的儲存體帳戶中。 私用資料資產的範例包括： 

* 您所撰寫的查詢及其相關設定  

* 使用者自訂函數 

* 串流分析執行時間所需的檢查點

* 參考資料的快照集 

也會儲存您的串流分析作業所使用資源的連線詳細資料。 加密您的儲存體帳戶，以保護您的所有資料。 

為了協助您符合任何管制產業或環境中的合規性義務，您可以閱讀更多有關 [Microsoft 合規性供應](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)專案的資訊。 

## <a name="enables-data-residency"></a>啟用資料存放區 
您可以使用這項功能，視需要提供儲存體帳戶來強制執行任何資料落地需求。

## <a name="known-issues"></a>已知問題
有一個已知問題，就是使用受控識別來驗證任何輸入或輸出時，使用客戶管理金鑰的作業會失敗。 

## <a name="next-steps"></a>後續步驟

* [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)
* [了解 Azure 串流分析的輸入](stream-analytics-add-inputs.md)
* [Azure 串流分析作業中的檢查點和重新執行概念](stream-analytics-concepts-checkpoint-replay.md)
* [使用參考資料在串流分析中進行查閱](stream-analytics-use-reference-data.md)
