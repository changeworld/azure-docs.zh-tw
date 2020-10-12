---
title: 從自動化帳戶啟用 Azure 自動化變更追蹤和清查
description: 本文說明如何從自動化帳戶啟用變更追蹤和清查。
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 302e0cc9b77605afd4012e95fc7c0ab8a22fef3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186311"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>自動化帳戶的 [啟用變更追蹤和清查]

此文章說明如何使用自動化帳戶，為您環境中的 VM 啟用[變更追蹤和清查](change-tracking.md)功能。 若要大規模啟用 Azure VM，您必須使用「變更追蹤和清查」來啟用現有的 VM。 

> [!NOTE]
> 啟用「變更追蹤和清查」時，僅支援特定區域連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](./index.yml)，以管理電腦。
* [虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

1. 瀏覽至您的自動化帳戶，然後選取 [組態管理] 下的 [清查] 或 [變更追蹤]。

2. 選擇 Log Analytics 工作區與自動化帳戶，然後按一下 [啟用]，以啟用變更追蹤和清查。 最多需要 15 分鐘才能完成設定。

    ![啟用變更追蹤和清查](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="enable-azure-vms"></a>啟用 Azure VM

1. 從您的自動化帳戶中，選取 [組態管理] 底下的 [清查] 或 [變更追蹤]。

2. 按一下 [+ 加入 Azure VM]，然後從清單中選取一或多個 VM。 無法啟用的虛擬機器會呈現灰色且無法選取。 Azure VM 可以存在於任何區域中，而不論您的自動化帳戶位置為何。 

3. 按一下 [啟用]，以將選取的 VM 新增至此功能的電腦群組儲存搜尋。 如需詳細資訊，請參閱 [限制變更追蹤和清查部署範圍](automation-scope-configurations-change-tracking.md)。

    ![啟用 Azure VM](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>啟用非 Azure VM

您必須手動加入不在 Azure 中的機器。 

1. 從您的自動化帳戶中，選取 [組態管理] 底下的 [清查] 或 [變更追蹤]。

2. 按一下 [加入非 Azure 電腦]。 此動作會開啟新的瀏覽器視窗，並提供[安裝和設定適用於 Windows 之 Log Analytics 代理程式的指示](../azure-monitor/platform/log-analytics-agent.md)，讓該機器可以開始變更追蹤和清查作業。 如果您啟用目前受 Operations Manager 管理的機器，則不需新的代理程式，就能將工作區資訊輸入至現有的代理程式。

## <a name="enable-machines-in-the-workspace"></a>啟用工作區中的機器

您必須將手動安裝的機器或已向您工作區報告的機器新增至 Azure 自動化，才能變更追蹤和清查。 

1. 從您的自動化帳戶中，選取 [組態管理] 底下的 [清查] 或 [變更追蹤]。

2. 選取 [管理機器]。 如果您先前選擇 [在所有可用及未來的機器上啟用] 選項，則 [管理機器] 按鈕可能會呈現灰色

    ![已儲存的搜尋](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. 若要為所有可用的機器啟用變更追蹤和清查，可在 [管理機器] 頁面上選取 [在所有可用的機器上啟用]。 此動作會停用個別新增機器的控制項。 此工作會將向工作區回報之所有機器的名稱新增至電腦群組儲存的搜尋查詢。 選取時，此動作會停用 [管理機器] 按鈕。

4. 若要為所有可用的機器與未來的機器啟用此功能，請選取 [在所有可用及未來的機器上啟用]。 此選項會從工作區刪除已儲存的搜尋和範圍設定，並針對向工作區報告的所有 Azure 和非 Azure 機器開啟此功能。 選取時，此動作會永久停用 [管理機器] 按鈕，因為沒留下任何範圍設定。

5. 如有需要，您可以藉由重新新增初始儲存的搜尋，重新新增範圍設定。 如需詳細資訊，請參閱 [限制變更追蹤和清查部署範圍](automation-scope-configurations-change-tracking.md)。

6. 若要針對一或多部機器新增此功能，請選取 [在選取的機器上啟用]，然後按一下想針對此功能啟用之每部機器旁邊的 [新增]。 此工作會針對此功能，將所選取機器的名稱加入至電腦群組儲存的搜尋查詢。

## <a name="next-steps"></a>後續步驟

* 若要使用此功能，請參閱 [管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要對此功能的一般問題進行疑難排解，請參閱[對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。
