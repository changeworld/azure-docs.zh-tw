---
title: 從 Azure VM 啟用 Azure 自動化變更追蹤和清查
description: 本文說明如何從 Azure VM 啟用變更追蹤和清查。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5ff6e0ffd4040393a040dc67a511aab47887f6e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186260"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Azure VM 的 [啟用變更追蹤和清查]

本文說明如何使用 Azure VM 啟用其他機器上的[變更追蹤和清查](change-tracking.md)功能。 若要大規模啟用 Azure VM，您必須使用「變更追蹤和清查」來啟用現有的 VM。 

> [!NOTE]
> 啟用「變更追蹤和清查」時，僅支援特定區域連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](./index.yml)，以管理電腦。
* [虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

1. 在 [Azure 入口網站](https://portal.azure.com)中選取 [虛擬機器]，或從首頁搜尋並選取 [虛擬機器]。

2. 選取要啟用變更追蹤和清查的 VM。 VM 可能存在於任何區域中，無論您的自動化帳戶位於何處。

3. 在 VM 頁面上，選取 [組態管理] 底下的 [清查] 或 [變更追蹤]。

4. 您必須具有 `Microsoft.OperationalInsights/workspaces/read` 權限，才能判斷是否已為 VM 啟用工作區。 若要了解所需的其他權限，請參閱[功能設定權限](automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何同時啟用多個機器，請參閱[從自動化帳戶啟用變更追蹤和清查](automation-enable-changes-from-auto-acct.md)。

5. 選擇 Log Analytics 工作區與自動化帳戶，然後按一下 [啟用]，為 VM 以啟用變更追蹤和清查。 最多需要 15 分鐘才能完成設定。 

## <a name="next-steps"></a>後續步驟

* 如需使用此功能的詳細資訊，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要對此功能的一般問題進行疑難排解，請參閱[對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。
