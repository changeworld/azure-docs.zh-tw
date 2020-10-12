---
title: 防止意外刪除 - Azure 檔案共用
description: 了解 Azure 檔案共用的虛刪除，以及如何使用其進行資料復原，並防止意外刪除。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 11940a43438b72eb8a2e9391d56806744c4c27fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86527807"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>防止意外刪除 Azure 檔案共用

Azure 儲存體現在提供檔案共用的虛刪除 (預覽) 。 虛刪除可讓您在應用程式或其他儲存體帳戶使用者錯誤地刪除檔案共用時，復原檔案共用。

## <a name="how-soft-delete-preview-works"></a>虛刪除 (預覽) 的運作方式

啟用 Azure 檔案共用的虛刪除時，如果檔案共用已刪除，則會轉換為虛刪除狀態，而不是永久清除。 您可以設定虛刪除的資料在永久刪除之前可復原的時間量，並在此保留期間內隨時取消刪除該共用。 在刪除之後，共用和所有內容（包括快照集）將會還原至刪除之前的狀態。 虛刪除只適用于檔案共用層級-已刪除的個別檔案仍會永久清除。

您可以在新的或現有的檔案共用上啟用虛刪除。 虛刪除也具有回溯相容性，如此一來，您不需要對應用程式進行任何變更，即可使用虛刪除的保護。 

若要在其到期時間之前永久刪除虛刪除狀態中的檔案共用，您必須取消刪除該共用、停用虛刪除，然後再次刪除該共用。 然後，您應該重新啟用虛刪除，因為該儲存體帳戶中的任何其他檔案共用，都很容易在虛刪除關閉時遭到意外刪除。

對於虛刪除的進階檔案共用，在完全刪除共用的情況下，檔案共用配額 (已佈建的檔案共用大小) 會用於計算儲存體帳戶配額總計，直到虛刪除的共用到期日為止。

## <a name="availability"></a>可用性

Azure 檔案共用的虛刪除 (預覽版) 適用于所有儲存層、所有的儲存體帳戶類型，以及 Azure 檔案儲存體的每個區域中都有提供。

## <a name="configuration-settings"></a>組態設定

### <a name="enabling-or-disabling-soft-delete"></a>啟用或停用虛刪除

檔案共用的虛刪除是在儲存體帳戶層級啟用的，因此，虛刪除設定會套用至儲存體帳戶內的所有檔案共用。 您可以隨時啟用或停用虛刪除。 當您建立新的儲存體帳戶時，預設會停用檔案共用的虛刪除。 針對現有的儲存體帳戶，虛刪除也預設為停用。 如果您已針對 Azure 檔案共用設定 [azure 檔案共用備份](../../backup/azure-file-share-backup-overview.md) ，則會自動在該共用的儲存體帳戶上啟用 azure 檔案共用的虛刪除。

如果您啟用檔案共用的虛刪除，請刪除一些檔案共用，然後停用虛刪除，如果已在該期間內儲存共用，您仍然可以存取及復原這些檔案共用。 當您啟用虛刪除時，也需要設定保留期限。

### <a name="retention-period"></a>保留期限

保留期限是虛刪除的檔案共用儲存及可供復原的時間量。 對於明確刪除的檔案共用，保留期限時鐘會在資料刪除時啟動。 您目前可以指定介於1到365天的保留期限。 您可以隨時變更虛刪除保留期限。 更新的保留期間僅適用於在保留期間已更新之後刪除的共用。 保留期間更新之前所刪除的共用，將會在該資料刪除時所設定的保留期間結束後到期。

## <a name="pricing-and-billing"></a>價格和計費

標準和進階檔案共用都會在虛刪除時以已使用的容量計費，而非以佈建的容量計費。 此外，進階檔案共用會以虛刪除狀態中的快照集費率來計費。 標準檔案共用在虛刪除狀態下，會以一般費率計費。 對於在設定的保留期間之後永久刪除的資料，您將不需要付費。

如需 Azure 檔案儲存體一般價格的詳細資訊，請參閱 [Azure 檔案儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

在您首次啟用虛刪除時，建議您使用較小的保留期間，以清楚了解此功能對您的帳單將有何影響。

## <a name="next-steps"></a>後續步驟

若要瞭解如何啟用和使用虛刪除，請繼續 [啟用虛刪除](storage-files-enable-soft-delete.md)。
