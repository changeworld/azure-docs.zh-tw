---
title: Azure Migrate 專案疑難排解
description: 協助您針對建立和管理 Azure Migrate 專案的問題進行疑難排解。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: af9254bc536dedc425aac53199665ae399e60a5a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494404"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 專案疑難排解

本文可協助您針對建立和管理 [Azure Migrate](migrate-services-overview.md) 專案時所發生的問題進行疑難排解。

## <a name="how-to-add-new-project"></a>如何新增專案？

您的訂用帳戶中可以有多個 Azure Migrate 專案。 [瞭解如何](./create-manage-projects.md) 建立第一次的專案，或 [新增其他](create-manage-projects.md#create-additional-projects) 專案。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 許可權？

您必須有訂用帳戶中的「參與者」或「擁有者」權限，才能建立 Azure Migrate 專案。

## <a name="cant-find-a-project"></a>找不到專案

尋找現有的 Azure Migrate 專案時，取決於您使用的是目前或舊版的 Azure Migrate。 [請遵循](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理位置

您可以針對 [公用](migrate-support-matrix.md#supported-geographies-public-cloud) 和 [政府](migrate-support-matrix.md#supported-geographies-azure-government)雲端，在支援的地理位置中建立 Azure Migrate 專案。

## <a name="what-are-vm-limits"></a>什麼是 VM 限制？

您最多可以在單一專案中評估35000個 VMware Vm 或最多35000部 Hyper-v Vm。 一個專案可以包含 VMware VM 和 Hyper-V VM，數目上限為評量限制。

## <a name="can-i-upgrade-old-project"></a>我可以升級舊的專案嗎？

無法更新舊版 Azure Migrate 的專案。 您需要 [建立新的專案](./create-manage-projects.md)，並在其中新增工具。

## <a name="cant-create-a-project"></a>無法建立專案

如果您嘗試建立專案並遇到部署錯誤：

- 如果是暫時性錯誤，請嘗試再次建立專案。 在 [ **部署**] 中，按一下 [ **重新部署** ] 以再試一次。
- 檢查您有訂用帳戶中的參與者或擁有者許可權。
- 如果您要在新加入的地理位置進行部署，請稍候片刻，然後再試一次。
- 如果您收到錯誤「要求必須包含使用者識別標頭」，這可能表示您無法存取組織的 Azure Active Directory (Azure AD) 租使用者。 在此案例中：
    - 當您第一次新增至 Azure AD 租使用者時，您會收到加入租使用者的電子郵件邀請。
    - 接受要新增至租使用者的邀請。
    - 如果您看不到電子郵件，請洽詢具有租使用者存取權的使用者，並要求他們 [重新傳送邀請](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) 給您。
    - 收到邀請電子郵件之後，請將它開啟，然後選取連結來接受邀請。 然後，登出 Azure 入口網站並重新登入。  (重新整理瀏覽器將無法運作。 ) 之後，您就可以開始建立遷移專案。

## <a name="how-do-i-delete-a-project"></a>如何? 刪除專案

[遵循這些指示](create-manage-projects.md#delete-a-project) 來刪除專案。 請注意，當您刪除專案時，專案和專案中有關探索到之電腦的中繼資料都會被刪除。

## <a name="added-tools-dont-show"></a>新增的工具未顯示

請確定您已選取正確的專案。 在 Azure Migrate hub >**伺服器** 或 **資料庫** 中，按一下 [**遷移 (專案**] 旁的 [**變更**]，然後按一下螢幕右上角的 [變更) ]。 選擇正確的訂用帳戶和專案名稱，> **確定]**。 頁面應該會以所選項目的新增工具重新整理。

## <a name="next-steps"></a>後續步驟

新增 [評定](how-to-assess.md) 或 [遷移](how-to-migrate.md) 工具以 Azure Migrate 專案。