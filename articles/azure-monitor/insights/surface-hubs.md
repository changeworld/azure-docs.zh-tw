---
title: 使用 Azure 監視器監視 Surface Hub |Microsoft Docs
description: 使用 Surface Hub 解決方案來追蹤您的 Surface Hub 健康狀態，並了解其使用狀況。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 7e3b0f92770b48ef5163846e67940efe80fb669a
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085343"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>使用 Azure 監視器監視 Surface Hub 以追蹤其健康情況

![Surface Hub 符號](./media/surface-hubs/surface-hub-symbol.png)

本文描述如何使用 Azure 監視器中的 Surface Hub 解決方案來監視 Microsoft Surface Hub 裝置。 該解決方案可幫助您追蹤您的 Surface Hub 健康情況，以及了解其使用狀況。

每個 Surface Hub 都已安裝 Microsoft Monitoring Agent。 透過代理程式，您可以從 Surface Hub 傳送資料到 Azure 監視器中的 Log Analytics 工作區。 系統會從您的 Surface Hub 讀取記錄檔，然後傳送給 Azure 監視器。 伺服器處於離線狀態、無法同步處理行事曆、裝置帳戶無法登入 Skype，諸如此類的問題會顯示在 Azure 監視器的 Surface Hub 儀表板中。 使用儀表板中的資料，您可以識別未執行或發生其他問題的裝置，然後可能會套用修正程式來處理偵測到的問題。

## <a name="install-and-configure-the-solution"></a>安裝和設定解決方案
請使用下列資訊來安裝和設定方案。 為了在 Azure 監視器中管理您的 Surface Hub，您會需要下列項目：

* 可支援您需要監視之裝置數目的 [Log Analytics 訂用帳戶](https://azure.microsoft.com/pricing/details/log-analytics/)層級。 Log Analytics 的定價取決於已註冊的裝置數目，以及所要處理的資料量。 規劃使用 Surface Hub 時請將這一點列入考慮。

接下來，您要新增現有的 Log Analytics 工作區，或建立新的工作區。 如需使用任一種方法的詳細指示，請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../learn/quick-create-workspace.md)。 設定 Log Analytics 工作區之後，有兩種方式可以註冊 Surface Hub 裝置：

* 自動透過 Intune 註冊
* 在 Surface Hub 裝置上手動透過 [設定]**** 註冊

## <a name="set-up-monitoring"></a>設定監視
您可以使用 Azure 監視器來監視 Surface Hub 的健康情況和活動。 您可以使用 Intune 註冊 Surface Hub，或使用 Surface Hub 上的 [設定]**** 在本機註冊。

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>透過 Intune 將 Surface Hub 連線到 Azure 監視器
您需要 Log Analytics 工作區的工作區識別碼和工作區金鑰，這個 Log Analytics 工作區將管理 Surface Hub。 您可以在 Azure 入口網站中的工作區設定中取得這兩個項目。

Intune 是一個 Microsoft 產品，可讓您集中管理套用到一或多個裝置的 Log Analytics 工作區組態設定。 請依照下列步驟透過 Intune 設定您的裝置：

1. 登入 Intune。
2. 流覽至 [**設定**  >  **連接的來源**]。
3. 以 Surface Hub 範本為基礎建立或編輯原則。
4. 瀏覽至原則的 Azure Operational Insight 區段，將 Log Analytics 工作區識別碼** 和工作區金鑰** 新增至原則。
5. 儲存原則。
6. 將原則與適當的裝置群組關聯。

   ![Intune 原則](./media/surface-hubs/intune.png)

Intune 接著會將 Log Analytics 設定與目標群組中的裝置同步處理，從而將它們註冊到 Log Analytics 工作區。

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>使用設定應用程式將 Surface Hub 連線到 Azure 監視器
您需要 Log Analytics 工作區的工作區識別碼和工作區金鑰，這個 Log Analytics 工作區將管理 Surface Hub。 您可以從 Azure 入口網站中 Log Analytics 工作區的設定取得這兩個項目。

如果您不使用 Intune 來管理您的環境，可以透過每個 Surface Hub 上的 [設定]**** 以手動方式註冊裝置︰

1. 從您的 Surface Hub 開啟 [設定]****。
2. 出現提示時，輸入裝置的系統管理員認證。
3. 按一下 [這個裝置]****，再按一下 [監視]**** 下的 [設定 Log Analytics 設定]****。
4. 選取 [啟用監視]****。
5. 在 [Log Analytics 設定] 對話方塊中，輸入 Log Analytics **工作區識別碼**和**工作區金鑰**。  
   ![螢幕擷取畫面顯示已選取 [啟用監視] 和 [工作區識別碼] 和 [工作區金鑰] 文字方塊的 Microsoft Operations Manager Suite 設定。](./media/surface-hubs/settings.png)
6. 按一下 **[確定]** 完成設定。

隨即會出現確認訊息，告知設定是否已成功套用至裝置。 如果是，會顯示指出代理程式以成功連線到 Azure 監視器的訊息。 裝置接著會開始將資料傳送至 Azure 監視器，您可以在其中檢視並加以處理。

## <a name="monitor-surface-hubs"></a>監視 Surface Hub
使用 Azure 監視器監視 Surface Hub 的步驟，與監視任何其他已註冊裝置的步驟極為類似。

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

當您按一下 [Surface Hub] 圖格時，會顯示您裝置的健全狀況。

   ![Surface Hub 的儀表板](./media/surface-hubs/surface-hub-dashboard.png)

您可以根據現有或自訂的記錄檔搜尋來建立[警示](../platform/alerts-overview.md)。 您可以使用 Azure 監視器從 Surface Hub 所收集的資料，來搜尋有關您為裝置所定義之條件的問題和警示。

## <a name="next-steps"></a>後續步驟
* 使用 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)來檢視 Surface Hub 詳細資料。
* 建立[警示](../platform/alerts-overview.md)在 Surface Hub 發生問題時通知您。
