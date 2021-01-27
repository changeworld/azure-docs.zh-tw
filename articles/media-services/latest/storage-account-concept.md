---
title: Azure 儲存體帳戶
titleSuffix: Azure Media Services
description: 瞭解如何建立與 Azure 媒體服務搭配使用的 Azure 儲存體帳戶。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: inhenkel
ms.openlocfilehash: 55a49d48af95c103d2a28d5106af5f3166605514
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882241"
---
# <a name="azure-storage-accounts"></a>Azure 儲存體帳戶

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。

媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出成本。

您只能有一個 **主要** 儲存體帳戶，而與您的媒體服務帳戶相關聯的 **次要** 儲存體帳戶可以有任意數量。 媒體服務支援 **一般用途 v2** (GPv2) 或 **一般用途 v1** (GPv1) 帳戶。 僅限 Blob 帳戶不允許作為 **主要** 帳戶。

建議您使用 GPv2，讓您可以利用最新的功能和效能。 若要深入瞭解儲存體帳戶，請參閱 [Azure 儲存體帳戶總覽](../../storage/common/storage-account-overview.md)。

> [!NOTE]
> 雖然您可以使用「經常性存取層」來搭配 Azure 媒體服務，但您可以使用其他存取層來降低未主動使用之內容的儲存成本。

您可以為儲存體帳戶選擇不同的 Sku。 如需詳細資訊，請參閱[儲存體帳戶](/cli/azure/storage/account?view=azure-cli-latest)。 如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 不過，在挑選適用于生產環境的 SKU 時，您應該考慮 `--sku Standard_RAGRS` 為商務持續性提供地理複寫。

## <a name="assets-in-a-storage-account"></a>儲存體帳戶中的資產

在媒體服務 v3 中，儲存體 Api 可用來將檔案上傳至資產。 如需詳細資訊，請參閱 [Azure 媒體服務 v3 中的資產](assets-concept.md)。

> [!Note]
> 請勿在不使用媒體服務 Api 的情況下，嘗試變更媒體服務 SDK 所產生之 blob 容器的內容。

## <a name="storage-side-encryption"></a>儲存端加密

為了保護您的待用資產，資產應以儲存體端加密來加密。 下表顯示儲存端加密在媒體服務 v3 中的運作方式：

|加密選項|描述|媒體服務 v3|
|---|---|---|
|媒體服務儲存體加密| AES-256 加密、媒體服務所管理的金鑰。 |不支援。<sup> (1) </sup>|
|[待用資料的儲存體服務加密](../../storage/common/storage-service-encryption.md)|由 Azure 或客戶管理的 Azure 儲存體、金鑰所提供的伺服器端加密。|支援。|
|[儲存體用戶端加密](../../storage/common/storage-client-side-encryption.md)|Azure 儲存體所提供的用戶端加密，Key Vault 中由客戶管理的金鑰。|不支援。|

<sup>1</sup> 在媒體服務 v3 中，只有當您的資產是以媒體服務 v2 建立時，才支援儲存體加密 (AES-256 加密) ，這表示 v3 適用于現有的儲存體加密資產，但不允許建立新的資產。

## <a name="double-encryption"></a>雙重加密
媒體服務支援雙重加密。  若要深入瞭解雙重加密，請參閱 [Azure 雙重加密](../../security/fundamentals/double-encryption.md)。

## <a name="storage-account-errors"></a>儲存體帳戶錯誤

媒體服務帳戶的「中斷連線」狀態，代表帳戶無法繼續存取一個或多個附加的儲存體帳戶，因為儲存體存取金鑰已變更。 Media Services 需要最新的儲存體存取金鑰，才能在帳戶中執行多項工作。

以下是可能會導致媒體服務帳戶無法存取附加儲存體帳戶的主要情況。

|問題|解決方案|
|---|---|
|媒體服務帳戶或附加儲存體帳戶已移轉至個別的訂用帳戶中。 |將儲存體帳戶 () 或媒體服務帳戶遷移，使其全都位於相同的訂用帳戶中。 |
|媒體服務帳戶使用的是不同訂用帳戶中的附加儲存體帳戶，因為其是受到支援的早期媒體服務帳戶。 所有的早期媒體服務帳戶都會轉換成新式的 Azure 資源管理員型帳戶，且會有中斷連線的狀態。 |遷移儲存體帳戶或媒體服務帳戶，使其全都位於相同的訂用帳戶中。|

## <a name="azure-storage-firewall"></a>Azure 儲存體防火牆

Azure 媒體服務不支援已啟用 Azure 儲存體防火牆或 [私人端點](../../storage/common/storage-network-security.md) 的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

若要深入了解如何將儲存體帳戶附加到媒體服務帳戶，請參閱[建立帳戶](./create-account-howto.md)。