---
title: Azure 區塊鏈 Service 聯盟
description: 概述 Azure 區塊鏈 service 如何實行聯盟區塊鏈網路。
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247614"
---
# <a name="azure-blockchain-service-consortium"></a>Azure 區塊鏈 Service 聯盟

使用 Azure 區塊鏈 Service 時，您可以建立私用聯盟區塊鏈網路，其中每個區塊鏈網路都可以限制為網路中的特定參與者。 只有私人聯盟區塊鏈網路中的參與者可以查看區塊鏈並與其互動。 Azure 區塊鏈 Service 中的聯盟網路可以包含兩種類型的成員參與者角色：

* 可以接受聯盟管理動作並可參與區塊鏈交易的**系統管理員**許可權參與者。

* 不能採取任何聯盟管理動作，但可以參與區塊鏈交易的**使用者**參與者。

聯盟網路可以混合使用參與者角色，而且每個角色類型可以有任意數目的。 必須至少有一位系統管理員。

下圖顯示具有多個參與者的聯盟網路：

![私用的聯盟網狀圖表](./media/consortium/network-diagram.png)

透過 Azure 區塊鏈 Service 中的聯盟管理，您可以管理聯盟網路中的參與者。 聯盟的管理是以網路的共識模型為基礎。 在目前的預覽版本中，Azure 區塊鏈 Service 提供了適用于聯盟管理的集中式共識模型。 任何具有管理員角色的特殊許可權參與者都可以接受聯盟管理動作，例如新增或移除網路中的參與者。

## <a name="roles"></a>角色

聯盟中的參與者可以是個人或組織，而且可以指派使用者角色或系統管理員角色。 下表列出兩個角色之間的高層級差異：

| 動作 | 使用者角色 | 系統管理員角色
|--------|:----:|:------------:|
| 建立新成員 | 是 | 是 |
| 邀請新成員 | 否 | 是 |
| 設定或變更成員參與者角色 | 否 | 是 |
| 變更成員顯示名稱 | 僅適用于自己的成員 | 僅適用于自己的成員 |
| 移除成員 | 僅適用于自己的成員 | 是 |
| 參與區塊鏈交易 | 是 | 是 |

### <a name="user-role"></a>使用者角色

使用者是沒有系統管理員功能的聯盟參與者。 他們無法參與管理與聯盟相關的成員。 使用者可以變更其成員的顯示名稱，並可從聯盟中移除自己。

### <a name="administrator"></a>系統管理員

系統管理員可以管理聯盟內的成員。 系統管理員可以邀請成員、移除成員，或更新聯盟內的成員角色。
聯盟中至少必須有一位系統管理員。 在離開聯盟之前，最後一個系統管理員必須先將另一個參與者指定為系統管理員角色。

## <a name="managing-members"></a>管理成員

只有系統管理員可以邀請其他參與者加入聯盟。 系統管理員會使用他們的 Azure 訂用帳戶識別碼來邀請參與者。

一旦受邀，參與者就可以在 Azure 區塊鏈 Service 中部署新的成員，以加入區塊鏈聯盟。 若要觀看並加入受邀的聯盟，您必須指定網路系統管理員邀請中所使用的相同 Azure 訂用帳戶識別碼。

系統管理員可以移除聯盟中的任何參與者，包括其他系統管理員。 成員只能從聯盟移除自己。

## <a name="consortium-management-smart-contract"></a>聯盟管理智慧合約

Azure 區塊鏈 Service 中的聯盟管理是透過聯盟管理智慧合約來完成。 當您部署新的區塊鏈成員時，智慧型合約會自動部署至您的節點。

可以在 Azure 入口網站中查看根聯盟管理智慧合約的位址。 **RootContract 位址**位於區塊鏈成員的 [總覽] 區段。

![RootContract 位址](./media/consortium/rootcontract-address.png)

您可以使用聯盟管理[PowerShell 模組](manage-consortium-powershell.md)，Azure 入口網站，或直接透過使用 Azure 區塊鏈服務產生的乙太坊帳戶的智慧合約，與聯盟管理智慧合約互動。

## <a name="ethereum-account"></a>乙太坊帳戶

建立成員時，會建立乙太坊帳戶金鑰。 Azure 區塊鏈 Service 會使用金鑰來建立與聯盟管理相關的交易。 乙太坊帳戶金鑰是由 Azure 區塊鏈 Service 自動管理。

可以在 Azure 入口網站中查看成員帳戶。 成員帳戶位於區塊鏈成員的 [總覽] 區段。

![成員帳戶](./media/consortium/member-account.png)

您可以按一下您的成員帳戶並輸入新密碼，以重設乙太坊帳戶。 系統會重設乙太坊帳戶位址和密碼。  

## <a name="next-steps"></a>後續步驟

您可以透過 PowerShell 存取聯盟管理動作。 如需詳細資訊，請參閱[使用 PowerShell 管理 Azure 區塊鏈 Service 中的聯盟成員](manage-consortium-powershell.md)。
