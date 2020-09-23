---
title: 區塊 blob 的時間點還原
titleSuffix: Azure Storage
description: 區塊 blob 的時間點還原可讓您將儲存體帳戶還原至特定時間點的先前狀態，以提供防止意外刪除或損毀的保護。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 7fbebf21b79d2a533de0a872dfe6a10bc8f8e7e5
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987042"
---
# <a name="point-in-time-restore-for-block-blobs"></a>區塊 blob 的時間點還原

時間點還原可讓您將區塊 blob 資料還原至先前的狀態，以提供防止意外刪除或損毀的保護。 時間點還原適用于使用者或應用程式不小心刪除資料或應用程式錯誤損毀資料的案例。 時間點還原也可讓測試案例必須將資料集還原為已知狀態，再執行進一步的測試。

僅限一般用途 v2 儲存體帳戶支援還原時間點。 只有經常性存取和非經常性存取層中的資料可以使用時間點還原來還原。

若要瞭解如何啟用儲存體帳戶的時間點還原，請參閱 [在區塊 blob 資料上執行時間點還原](point-in-time-restore-manage.md)。

## <a name="how-point-in-time-restore-works"></a>時間點還原的運作方式

若要啟用時間點還原，您必須建立儲存體帳戶的管理原則，並指定保留期限。 在保留期限內，您可以從目前狀態將區塊 blob 還原至先前時間點的狀態。

若要起始時間點還原，請呼叫 [還原 Blob 範圍](/rest/api/storagerp/storageaccounts/restoreblobranges) 作業，並指定 UTC 時間的還原點。 您可以指定要還原之容器和 blob 名稱的字典範圍，或略過範圍以還原儲存體帳戶中的所有容器。 每個還原作業最多可支援10個字典的範圍。

Azure 儲存體會分析在要求的還原點（以 UTC 時間指定）和目前的時間之間，對指定的 blob 進行的所有變更。 還原作業是不可部分完成的，因此它會完全成功還原所有變更，否則會失敗。 如果有任何無法還原的 blob，則作業會失敗，且受影響容器的讀取和寫入作業會繼續。

一次只能在一個儲存體帳戶上執行一個還原作業。 還原作業在進行中時無法取消，但可以執行第二個還原作業來復原第一項作業。

**還原 Blob 範圍**作業會傳回可唯一識別作業的還原識別碼。 若要檢查時間點還原的狀態，請使用「**還原 Blob 範圍**」作業所傳回的還原識別碼，呼叫「**取得還原狀態**」作業。

請記住下列關於還原作業的限制：

- 已透過 [Put 區塊](/rest/api/storageservices/put-block) 或 [PUT 區塊從 URL](/rest/api/storageservices/put-block-from-url)上傳，但未透過 [put block List](/rest/api/storageservices/put-block-list)認可的區塊不是 blob 的一部分，因此不會在還原作業中還原。
- 無法還原具有作用中租用的 blob。 如果包含作用中租用的 blob 包含在要還原的 blob 範圍內，則還原作業會以不可部分完成的方式失敗。
- 在還原作業中，不會建立或刪除快照集。 只有基底 blob 會還原為先前的狀態。
- 如果在目前的時間和還原點之間的期間內，將 blob 移至經常性存取層與非經常性存取層之間，則會將 blob 還原至先前的層級。 不過，已移至封存層的 blob 將不會還原。

> [!IMPORTANT]
> 當您執行還原作業時，Azure 儲存體會封鎖作業期間正在還原之範圍中 blob 的資料作業。 主要位置會封鎖讀取、寫入和刪除作業。 基於這個理由，在進行還原作業時，在 Azure 入口網站中列出容器的作業可能不會如預期般執行。
>
> 如果儲存體帳戶是異地複寫的，則在還原作業期間，次要位置的讀取作業可能會繼續進行。

> [!CAUTION]
> 時間點還原只支援在區塊 blob 上還原作業。 無法還原容器上的作業。 如果您藉由呼叫「 [刪除容器](/rest/api/storageservices/delete-container) 」作業來刪除儲存體帳戶中的容器，則無法使用還原作業來還原該容器。 如果您想要還原，請刪除個別的 blob，而不是刪除容器。

### <a name="prerequisites-for-point-in-time-restore"></a>時間點還原的必要條件

還原時間點需要先啟用下列 Azure 儲存體功能，才能啟用時間點還原：

- [虛刪除](soft-delete-overview.md)
- [變更摘要](storage-blob-change-feed.md)
- [Blob 版本設定](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>時間點還原的保留期間

當您針對儲存體帳戶啟用時間點還原時，會指定保留期限。 您儲存體帳戶中的區塊 blob 可在保留期間內還原。

當您啟用時間點還原時，就會開始保留期限。 請記住，您無法將 blob 還原至保留期間開始之前的狀態。 例如，如果您在5月1日啟用時間點還原，保留30天，則在5月15日之前，您最多可以還原15天。 在6月1日，您可以從1到30天還原資料。

時間點還原的保留期限必須至少為一天，小於針對虛刪除指定的保留週期。 例如，如果虛刪除保留期限設定為7天，則時間點還原保留期限可能介於1到6天之間。

### <a name="permissions-for-point-in-time-restore"></a>時間點還原的許可權

若要起始還原作業，用戶端必須具有儲存體帳戶中所有容器的寫入權限。 若要使用 Azure Active Directory (Azure AD) 授與授權還原作業的許可權，請將 **儲存體帳戶參與者** 角色指派給儲存體帳戶、資源群組或訂用帳戶層級的安全性主體。

## <a name="limitations-and-known-issues"></a>限制與已知問題

區塊 blob 的時間點還原具有下列限制和已知問題：

- 只有標準一般用途 v2 儲存體帳戶中的區塊 blob 可以還原為時間點還原作業的一部分。 附加 blob、分頁 blob 和 premium 區塊 blob 不會還原。 如果您已在保留期限內刪除容器，則該容器將不會隨著時間點還原作業還原。 若要瞭解如何刪除容器的保護，請參閱容器的虛 [刪除 (預覽) ](soft-delete-container-overview.md)。
- 只有經常性存取層或非經常性存取層中的區塊 blob，才能在時間點還原作業中還原。 不支援還原封存層中的區塊 blob。 例如，如果經常性存取層的 Blob 在兩天前已移至封存層，且還原作業還原至三天前的某個時間點，則 Blob 不會還原至經常性存取層。 若要還原封存的 blob，請先將它移出封存層。
- 如果要還原之範圍中的區塊 blob 有作用中的租用，則時間點還原作業將會失敗。 在起始還原作業之前，請先中斷任何使用中的租用。
- 不支援還原 Azure Data Lake Storage Gen2 的平面和階層命名空間。

> [!IMPORTANT]
> 如果您將區塊 blob 還原到早于2020年9月22日的某個時間點，則時間點還原的預覽限制將會生效。 Microsoft 建議您選擇等於或晚于2020年9月22日的還原點，以利用正式推出的時間點還原功能。

## <a name="pricing-and-billing"></a>價格和計費

時間點還原的計費取決於處理以執行還原作業的資料量。 處理的資料量取決於還原點與目前時間之間發生的變更數目。 例如，假設變更在儲存體帳戶中封鎖 blob 資料的變動率相當頻繁，則在1天內返回的還原作業會在10天內回復的還原成本 1/10。

若要預估還原作業的成本，請檢查變更摘要記錄檔，以估計還原期間修改過的資料量。 例如，如果變更摘要的保留期限為30天，而變更摘要的大小為 10 MB，則還原至較早10天的時間點，將會花費大約為該區域中 LRS 帳戶所列出的一到一第三個價格。 還原至較早27天的點，將會花費大約九-十分之一的價格。

如需時間點還原價格的詳細資訊，請參閱 [區塊 blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="next-steps"></a>下一步

- [在區塊 blob 資料上執行時間點還原](point-in-time-restore-manage.md)
- [Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)
- [啟用 Blob 的虛刪除](soft-delete-enable.md)
- [啟用和管理 Blob 版本設定](versioning-enable.md)
