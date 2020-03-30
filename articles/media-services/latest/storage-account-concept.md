---
title: Azure 儲存體帳戶
titleSuffix: Azure Media Services
description: 瞭解如何創建要與 Azure 媒體服務一起使用的 Azure 存儲帳戶。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499831"
---
# <a name="azure-storage-accounts"></a>Azure 儲存體帳戶

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。

媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於同一位置的存儲帳戶，以避免額外的延遲和資料出口成本。

您必須有一個**主**存儲帳戶，並且可以具有與媒體服務帳戶關聯的任意數量的**輔助**存儲帳戶。 媒體服務支援**一般用途 v2** (GPv2) 或**一般用途 v1** (GPv1) 帳戶。 僅 Blob 帳戶不允許作為**主**帳戶。

我們建議您使用 GPv2，以便利用最新的功能和性能。 要瞭解有關存儲帳戶的詳細資訊，請參閱[Azure 存儲帳戶概述](../../storage/common/storage-account-overview.md)。

> [!NOTE]
> 僅支援熱訪問層與 Azure 媒體服務一起使用，儘管其他訪問層可用於降低未主動使用的內容的存儲成本。

您可以為存儲帳戶選擇不同的 SKU。 如需詳細資訊，請參閱[儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。 如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 但是，在選擇用於生產的 SKU 時，應考慮`--sku Standard_RAGRS`，它為業務連續性提供地理複製。

## <a name="assets-in-a-storage-account"></a>儲存體帳戶中的資產

在媒體服務 v3 中，存儲 API 用於將檔上載到資產中。 有關詳細資訊，請參閱[Azure 媒體服務 v3 中的資產](assets-concept.md)。

> [!Note]
> 不要嘗試更改媒體服務 SDK 生成的 Blob 容器的內容，而無需使用媒體服務 API。

## <a name="storage-side-encryption"></a>儲存端加密

為了保護您的靜態資產，資產應通過存儲端加密進行加密。 下表顯示儲存端加密在媒體服務 v3 中的運作方式：

|加密選項|描述|媒體服務 v3|
|---|---|---|
|媒體服務存儲加密| AES-256 加密，由媒體服務管理金鑰。 |不支援。<sup>（1）</sup>|
|[靜態資料的存儲服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 存儲提供的伺服器端加密，由 Azure 或客戶管理金鑰。|支援。|
|[存儲用戶端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 存儲提供的用戶端加密，金鑰由金鑰保存庫中的客戶管理。|不支援。|

<sup>1</sup>在媒體服務 v3 中，僅當使用 Media Services v2 創建資產時，存儲加密 （AES-256 加密） 僅支援向後相容性，這意味著 v3 與現有存儲加密資產一起工作，但不允許創建新的存儲加密資產。

## <a name="storage-account-errors"></a>存儲帳戶錯誤

媒體服務帳戶的「中斷連線」狀態，代表帳戶無法繼續存取一個或多個附加的儲存體帳戶，因為儲存體存取金鑰已變更。 Media Services 需要最新的儲存體存取金鑰，才能在帳戶中執行多項工作。

以下是可能會導致媒體服務帳戶無法存取附加儲存體帳戶的主要情況。

|問題|解決方法|
|---|---|
|媒體服務帳戶或附加儲存體帳戶已移轉至個別的訂用帳戶中。 |遷移存儲帳戶或媒體服務帳戶，以便它們都在同一訂閱中。 |
|媒體服務帳戶使用的是不同訂用帳戶中的附加儲存體帳戶，因為其是受到支援的早期媒體服務帳戶。 所有早期媒體服務帳戶都轉換為基於 Azure 資源管理器的現代帳戶，並將具有斷開連接狀態。 |遷移存儲帳戶或媒體服務帳戶，以便它們都在同一訂閱中。|

## <a name="azure-storage-firewall"></a>Azure 存儲防火牆

Azure 媒體服務不支援啟用 Azure 存儲防火牆或[專用終結點](https://docs.microsoft.com/azure/storage/common/storage-network-security)的存儲帳戶。

## <a name="next-steps"></a>後續步驟

若要深入了解如何將儲存體帳戶附加到媒體服務帳戶，請參閱[建立帳戶](create-account-cli-quickstart.md)。
