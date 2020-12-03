---
title: '目錄許可權 (預覽) '
description: 本文概述如何在 Azure 範疇中設定 (RBAC) 的 Role-Based 存取控制
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: e87c9efc910d08307d40d42e58f8272a01902a41
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552060"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Azure 範疇資料平面中的角色型存取控制

本文說明如何在 Azure 範疇的 [資料平面](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)中執行 Role-Based 存取控制 (RBAC) 。

> [!IMPORTANT]
> 建立範疇帳戶的主體會自動授與所有資料平面許可權，而不論其可能或可能不在哪些資料平面角色。 若要讓任何其他使用者在 Azure 範疇中進行任何動作，他們必須至少在其中一個預先定義的資料平面角色中。

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Azure 範疇的預先定義資料平面角色

Azure 範疇會定義一組預先定義的資料平面角色，可用來控制誰可以存取 Azure 範疇中的內容。 這些角色包括：

* **範疇資料讀取器角色** -擁有範疇入口網站的存取權，而且可以讀取 Azure 範疇中的所有內容（掃描系結除外）
* **範疇資料編者角色** -擁有範疇入口網站的存取權，而且可以讀取 Azure 範疇中的所有內容（掃描系結除外）、可以編輯資產的相關資訊、編輯分類定義和詞彙，以及將分類和詞彙條款套用至資產。
* **範疇資料來源管理員角色** -沒有範疇入口網站的存取權 (使用者也需要在資料讀取器或資料編者角色中) 並且可以管理將資料掃描到 azure 範疇中的所有層面，但不具有與掃描相關的內容在 azure 範疇中的讀取或寫入存取權。

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>瞭解如何使用 Azure 範疇的資料平面角色

建立 Azure 範疇帳戶時，系統會將建立者視為同時位於範疇資料編者和範疇資料來源管理員角色中。 但是帳戶建立者未在角色存放區中指派給這些角色。 Azure 範疇會辨識主體是帳戶的建立者，並根據其身分識別將這些功能延伸至這些帳戶。

所有其他使用者只要放在其中一個角色內，就只能使用 Azure 範疇帳戶。 這表示在建立 Azure 範疇帳戶時，不會有帳戶，但建立者可以存取帳戶或使用其 Api，直到它們放入一或多個先前定義的角色。

請注意，範疇資料來源管理員角色有兩種支援的案例。 第一個案例是針對已經範疇資料讀取器或範疇資料 Curators 的使用者，也需要能夠建立掃描。 這些使用者必須有兩個角色：至少一個範疇資料讀取器或範疇資料編者，並放入範疇資料來源管理員角色。

範疇資料來源管理員的另一個案例是針對需要能夠設定和監視掃描，但不能存取任何目錄資料的程式設計進程（例如服務主體）。

您可以將服務主體放在範疇資料來源管理員角色中，而不是放在其他兩個角色中的任何一個，藉以執行此案例。 主體無法存取範疇入口網站，但這是 o.k。 因為它是程式設計的主體，而且只會透過 Api 進行通訊。

## <a name="putting-users-into-roles"></a>將使用者放入角色

因此，建立 Azure 範疇帳戶之後的第一個商務順序就是將人員指派給這些角色。

角色指派是透過 [Azure 的 RBAC](../role-based-access-control/overview.md)來管理。

只有 Azure 中的兩個內建控制平面角色可以指派使用者角色，這些角色是擁有者或使用者存取系統管理員。 因此，若要讓人員參與 Azure 範疇的這些角色，您必須尋找身為擁有者或使用者存取系統管理員或變成一個 oneself 的人員。

### <a name="an-example-of-assigning-someone-to-a-role"></a>將某人指派給角色的範例

1. 移至 https://portal.azure.com 並流覽至您的 Azure 範疇帳戶
1. 在左側按一下 [存取控制 (IAM) ]
1. 然後遵循[此處](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)提供的一般指示

## <a name="role-definitions-and-actions"></a>角色定義和動作

角色會定義為動作的集合。 如需如何定義角色的詳細資訊，請參閱 [這裡](../role-based-access-control/role-definitions.md) 。 在 [這裡](../role-based-access-control/built-in-roles.md) 查看 Azure 範疇角色的角色定義。

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>新增至 Azure 範疇帳戶中的資料平面角色

如果您想要獲得 Azure 範疇帳戶的存取權，讓您可以使用其 studio 或呼叫其 Api，您必須將其新增至 Azure 範疇資料平面角色。 唯一可達成此目的的人員是 Azure 範疇帳戶上的擁有者或使用者存取系統管理員。 對於大部分的使用者來說，下一個步驟是尋找可協助您找出可存取的適當人員的本機系統管理員。

對於具有公司 [Azure 入口網站](https://portal.azure.com) 存取權的使用者，他們可以查詢他們想要加入的特定 Azure 範疇帳戶、按一下其 [存取控制 (IAM) ] 索引標籤，並查看擁有者或使用者存取系統管理員 (UAAs) 的成員。 但請注意，在某些情況下 Azure Active Directory 群組或服務主體可能會當做擁有者或 UAAs 使用，在此情況下，可能無法直接聯繫這些群組或服務主體。 相反地，您必須找出系統管理員來提供協助。

## <a name="who-should-be-assigned-to-what-role"></a>誰應該指派給哪個角色？

|使用者案例|適當的角色 () |
|-------------|-----------------|
|我只需要找出資產，我不想編輯任何事|範疇資料讀取器角色|
|我需要編輯資產的相關資訊、對其進行分類、讓它們與詞彙專案產生關聯等等。|範疇資料編者角色|
|我需要編輯詞彙，或設定新的分類定義|範疇資料編者角色|
|我的應用程式服務主體需要將資料推送至 Azure 範疇|範疇資料編者角色|
|我需要透過範疇 Studio 設定掃描|範疇資料來源系統管理員角色，以及至少一個範疇資料讀取器角色或範疇資料編者角色|
|我需要啟用服務主體或其他程式設計身分識別，以在 Azure 範疇中設定及監視掃描，而不允許程式設計身分識別存取目錄的資訊 |範疇資料來源管理員角色|
|我需要將使用者放入 Azure 範疇中的角色 | 擁有者或使用者存取系統管理員 |

請前進到下一篇文章，以瞭解如何將安全性主體新增至角色。

## <a name="next-steps"></a>後續步驟

* [快速入門：建立 Azure 範疇帳戶](create-catalog-portal.md)
