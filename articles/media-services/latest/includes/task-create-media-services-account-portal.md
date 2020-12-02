---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971355"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>建立媒體服務帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下 [+ 建立資源] > [媒體] > [媒體服務]。
1. 在 [建立媒體服務帳戶] 區段中，輸入必要的值。

    | 名稱 | 描述 |
    | ---|---|
    |**帳戶名稱**|輸入新媒體服務帳戶的名稱。 媒體服務帳戶名稱為全部小寫且不含空格的字母或數字，且長度是 3 到 24 個字元。|
    |**訂用帳戶**|如果您有多個訂用帳戶，請從 Azure 訂用帳戶清單中選取您有權存取的訂用帳戶。|
    |**資源群組**|選取新的或現有的資源。 資源群組是共用生命週期、權限及原則的資源集合。 [在此](../../../azure-resource-manager/management/overview.md#resource-groups)深入了解。|
    |**位置**|選取您將用來為媒體服務帳戶儲存媒體和中繼資料記錄的地理區域。 此區域將用於處理和串流媒體。 只有可用的媒體服務區域才會出現在下拉式清單方塊中。 |
    |**儲存體帳戶**|選取儲存體帳戶，為媒體服務帳戶中的媒體內容提供 Blob 儲存體。 您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。 新的儲存體帳戶會建立於相同的區域中。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。<br/><br/>您只能有一個 **主要** 儲存體帳戶，而與您的媒體服務帳戶相關聯的 **次要** 儲存體帳戶可以有任意數量。 您可以使用 Azure 入口網站新增次要儲存體帳戶。 如需詳細資訊，請參閱 [Azure 儲存體帳戶與 Azure 媒體服務帳戶](../storage-account-concept.md)。<br/><br/>媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。|
    |**進階設定**| 您可以選取 [系統管理] 選項按鈕，以使用系統管理的身分識別來建立帳戶。  選取此選項，可讓您使用客戶自控金鑰，或使用自備金鑰 (BYOK) 和媒體服務來啟用信任的儲存體。  如需客戶自控金鑰的詳細資訊，請參閱[自備金鑰 (客戶自控金鑰) 與媒體服務](../concept-use-customer-managed-keys-byok.md)。 此外也會啟用[受控識別](../concept-managed-identities.md)。

1. 選取 **[釘選到儀表板]** 以查看帳戶部署的進度。
1. 按一下表單底部的 [建立]  。
