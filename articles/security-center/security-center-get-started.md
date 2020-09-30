---
title: 升級至 Azure Defender - Azure 資訊安全中心
description: 本快速入門示範如何升級至資訊安全中心的 Azure Defender，以獲得額外的安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b583e3d86ba193a92080f2da9cc0b16d07c5a993
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447162"
---
# <a name="quickstart-setting-up-azure-security-center"></a>快速入門：設定 Azure 資訊安全中心

Azure 資訊安全中心為混合式雲端工作負載提供統一的安全性管理和威脅防護。 免費功能只為 Azure 資源提供有限的安全性，而啟用 Azure Defender 會將這些功能延伸至內部部署與其他雲端。 Azure Defender 可協助您尋找和修正安全性弱點、套用存取和應用程式控制項以封鎖惡意活動、使用分析和情報來偵測威脅，以及在遭受攻擊時迅速回應。 您可以免費試用 Azure Defender。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。

在本文中，您可升級到 Azure Defender 以取得額外的安全性，並且在您的電腦上安裝 Log Analytics 代理程式以監視安全性弱點和威脅。

## <a name="prerequisites"></a>Prerequisites
若要開始使用資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

若要啟用訂用帳戶的 Azure Defender，您必須取得「訂用帳戶擁有者」、「訂用帳戶參與者」或「安全性管理員」角色。


## <a name="open-security-center-for-the-first-time"></a>首次開啟資訊安全中心

1. 登入 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。

1. 從入口網站的功能表中，選取 [資訊安全中心]。 

    資訊安全中心的概觀頁面隨即開啟。

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="資訊安全中心的概觀儀表板" lightbox="./media/security-center-get-started/overview.png":::

[資訊安全中心 - 概觀]  可讓您統一檢視混合式雲端工作負載的安全性狀態、探索並評定工作負載的安全性，以及識別和降低風險。 資訊安全中心會免費自動啟用先前並未由您或另一個訂用帳戶使用者上線的任何 Azure 訂用帳戶。

選取 [訂用帳戶] 功能表項目，即可檢視和篩選訂用帳戶清單。 資訊安全中心會調整顯示內容，以反映所選訂用帳戶的安全性狀態。 

在第一次啟動資訊安全中心的數分鐘內，您可能看到：

- 改善連線資源安全性的**數種建議方法**。
- 資訊安全中心正在評估您的資源庫存，以及每項資源的安全性狀態。

若要充分利用資訊安全中心，您需要完成下列步驟，才能啟用 Azure Defender 並安裝 Log Analytics 代理程式。


## <a name="enable-azure-defender"></a>啟用 Azure Defender

基於資訊安全中心快速入門和教學課程的目的，您必須啟用 Azure Defender。 提供 30 天的免費試用。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。 

1. 從資訊安全中心的側邊欄中，選取 [開始使用]。

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="資訊安全中心的概觀儀表板"::: 

    **升級**索引標籤會列出符合上線資格的訂用帳戶和工作區。

1. 從**選取工作區以啟用 Azure Defender** 清單中，選取要升級的工作區。
   - 如果您選取的訂用帳戶和工作區不符合試用資格，下一個步驟將會執行升級，並開始收費。
   - 如果您選取符合免費試用的工作區，下一步就會開始試用。
1. 選取 [升級] 以啟用 Azure Defender。

## <a name="enable-automatic-data-collection"></a>啟用自動資料收集
資訊安全中心會從您的電腦收集資料，以監視是否有安全性弱點及威脅。 資料是使用 Log Analytics 代理程式收集而得，收集的方式是讀取機器的各種安全性相關組態和事件記錄，並將資料複製到工作區進行分析。 根據預設，資訊安全中心會為您建立新的工作區。

啟用自動佈建時，資訊安全中心會在所有支援的電腦和任何新建立的虛擬機器上安裝 Log Analytics 代理程式。 強烈建議使用自動佈建。

啟用 Log Analytics 代理程式的自動佈建：

1. 在資訊安全中心功能表中，選取 [定價和設定]。
1. 選取相關的訂用帳戶。
1. 在**資料收集**頁面中，將 [自動佈建] 設定為 [開啟]。
1. 選取 [儲存]。

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="資訊安全中心的概觀儀表板":::

>[!TIP]
> 如果需要佈建工作區，代理程式安裝最多可能需要 25 分鐘的時間。

資訊安全中心會利用部署到電腦上的代理程式，提供與系統更新狀態、作業系統安全性組態、端點防護相關的額外建議，以及產生額外的安全性警示。

>[!NOTE]
> 將自動佈建設為**關閉**不會從已佈建代理程式的 Azure VM 中移除 Log Analytics 代理程式。 停用自動佈建會限制對資源的安全性監視。



## <a name="next-steps"></a>後續步驟
在本快速入門中，您已啟用 Azure Defender 並佈建 Log Analytics 代理程式，可取得各項混合式雲端工作負載的整合式安全性管理和威脅防護。 若要深入了解如何使用資訊安全中心，請繼續進行將內部部署和其他雲端中的 Windows 電腦上架的快速入門。

> [!div class="nextstepaction"]
> [快速入門：讓非 Azure 電腦上線](quickstart-onboard-machines.md)

想要最佳化並節省您的雲端費用嗎？

> [!div class="nextstepaction"]
> [使用成本管理開始分析成本](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
