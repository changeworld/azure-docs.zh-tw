---
title: 建立 Azure 媒體服務帳戶
description: 本教學課程會逐步引導您完成建立 Azure 媒體服務帳戶的步驟。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93f5e4d659b94bd79345a5e687de14ab6a5e8ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267950"
---
# <a name="create-a-media-services-account"></a>建立媒體服務帳戶

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要在 Azure 中開始加密、編碼、分析、管理和串流處理媒體內容，您需要建立 Media Services 帳戶。 媒體服務帳戶必須與一或多個儲存體帳戶產生關聯。 本文說明建立新 Azure 媒體服務帳戶的步驟。

> [!NOTE]
> 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。

 您可以使用 Azure 入口網站或 CLI 來建立媒體服務帳戶。 選擇您想要使用之方法的索引標籤。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[入口網站](#tab/portal/)

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Azure 入口網站提供快速建立 Azure 媒體服務帳戶的方法。 您可以使用自己的帳戶，來存取讓您在 Azure 中儲存、加密、編碼、管理和串流播放媒體內容的媒體服務。

目前，您可以使用 [Azure 入口網站](https://portal.azure.com/) 來：

* 管理媒體服務 v3 [實況活動](live-events-outputs-concept.md)， 
* view (不管理) v3 [資產](assets-concept.md)， 
* [取得存取 api 的相關資訊](./access-api-howto.md)。 

針對其他所有管理工作 (例如， [轉換和作業](transforms-jobs-concept.md) 和 [內容保護](content-protection-overview.md)) ，請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、 [CLI](https://aka.ms/ams-v3-cli-ref)或其中一個支援的 [sdk](media-services-apis-overview.md#sdks)。
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>使用 Azure 入口網站建立媒體服務帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下 [ **+ 建立資源**  >  **媒體**  >  **媒體服務**]。
1. 在 [ **建立媒體服務帳戶** ] 區段中，輸入必要的值。

    | 名稱 | 描述 |
    | ---|---|
    |**帳戶名稱**|輸入新媒體服務帳戶的名稱。 媒體服務帳戶名稱為全部小寫且不含空格的字母或數字，且長度是 3 到 24 個字元。|
    |**訂用帳戶**|如果您有一個以上的訂用帳戶，請從您有權存取的 Azure 訂用帳戶清單中選取一個訂用帳戶。|
    |**資源群組**|選取新的或現有的資源。 資源群組是共用生命週期、權限及原則的資源集合。 [在此](../../azure-resource-manager/management/overview.md#resource-groups)深入了解。|
    |**位置**|選取將用來儲存媒體服務帳戶之媒體和元資料記錄的地理區域。 此區域將用於處理和串流媒體。 只有可用的媒體服務區域才會出現在下拉式清單方塊中。 |
    |**儲存體帳戶**|選取儲存體帳戶，以從您的媒體服務帳戶提供媒體內容的 blob 儲存體。 您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。 新的儲存體帳戶會建立於相同的區域中。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。<br/><br/>您必須有一個 **主要** 儲存體帳戶，而且您可以有任意數目的 **次要** 儲存體帳戶與您的媒體服務帳戶相關聯。 您可以使用 Azure 入口網站來新增次要儲存體帳戶。 如需詳細資訊，請參閱 [Azure 儲存體具有 Azure 媒體服務帳戶的帳戶](storage-account-concept.md)。<br/><br/>媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。|

1. 選取 **[釘選到儀表板]** 以查看帳戶部署的進度。
1. 按一下表單底部的 [建立] **** 。

    當您建立媒體服務帳戶時， **預設** 串流端點會新增至您的帳戶處於 **已停止** 狀態。 若要開始串流處理您的內容並利用[動態封裝](dynamic-packaging-overview.md)和[動態加密](content-protection-overview.md)，您想要串流內容的串流端點必須處於執行**中狀態。** 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>設定 Azure 訂用帳戶

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>建立儲存體帳戶

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>建立媒體服務帳戶

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>另請參閱

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [將次要儲存體附加至媒體服務帳戶](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>後續步驟

[串流處理檔案](stream-files-dotnet-quickstart.md)
