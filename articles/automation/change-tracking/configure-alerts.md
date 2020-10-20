---
title: 如何建立 Azure 自動化變更追蹤和清查的警示
description: 本文說明如何設定 Azure 警示，以通知有關變更追蹤和清查所偵測到的變更狀態。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 275e57e5dcf173e8d5f30f262641b02698910422
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209530"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>如何建立變更追蹤和清查的警示

Azure 中的警示會主動通知您 runbook 作業的結果、服務健康情況問題，或與您的自動化帳戶相關的其他案例。 Azure 自動化不包含預先設定的警示規則，但您可以根據它產生的資料來建立自己的警示規則。 本文提供根據變更追蹤和清查所識別的變更來建立警示規則的指引。

如果您不熟悉 Azure 監視器警示，請在開始之前參閱 [Microsoft Azure 中的警示總覽](../../azure-monitor/platform/alerts-overview.md) 。 若要深入瞭解使用記錄查詢的警示，請參閱 [Azure 監視器中的記錄警示](../../azure-monitor/platform/alerts-unified-log.md)。

## <a name="create-alert"></a>建立警示

下列範例顯示已在電腦上修改 **c:\windows\system32\drivers\etc\hosts** 檔案。 這個檔案很重要，因為 Windows 會使用此檔案將主機名稱解析為 IP 位址。 這項作業的優先順序高於 DNS，且可能會導致連線問題。 此外也可能會導致流量重新導向至惡意或危險的網站。

![顯示主機檔案變更的圖表](./media/configure-alerts/changes.png)

我們將使用此範例來討論建立變更警示的步驟。

1. 在自動化帳戶的 [ **變更追蹤** ] 頁面上，選取 [ **Log Analytics**]。

2. 在 [記錄] 搜尋中，使用查詢 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 尋找 **hosts** 檔案的內容變更。 此查詢會尋找包含單字的完整路徑名稱之檔案的內容變更 `hosts` 。 您也可以將路徑部分變更為其完整形式以要求特定檔案，例如使用 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`。

3. 查詢傳回結果之後，請在記錄搜尋中選取 [ **新增警示規則** ] 以開啟 [ **警示建立** ] 頁面。 您也可以在 Azure 入口網站中透過 [Azure 監視器] 瀏覽至此頁面。

4. 再次檢查我們的查詢並修改警示邏輯。 在此情況下，即使在環境中的所有電腦上偵測到一項變更，也要觸發警示。

    ![變更查詢以追蹤 hosts 檔案的變更](./media/configure-alerts/change-query.png)

5. 設定警示邏輯之後，請指派動作群組來執行動作，以回應觸發警示。 在此案例中，我們設定要傳送的電子郵件，以及要建立的 IT 服務管理 (ITSM) 票證。

請遵循下列步驟來設定警示，讓您知道更新部署的狀態。 如果您不熟悉 Azure 警示，請參閱 [Azure 警示總覽](../../azure-monitor/platform/alerts-overview.md)。

## <a name="configure-action-groups-for-your-alerts"></a>設定警示的動作群組

設定好警示之後，您就可以設定動作群組，這是可跨多個警示使用的一組動作。 這些動作可能包括電子郵件通知、runbook、webhook，以及其他更多。 若要深入了解動作群組，請參閱[建立及管理動作群組](../../azure-monitor/platform/action-groups.md)。

1. 選取警示，然後選取 [動作群組] 下的 [新建]。

2. 輸入動作群組的完整名稱和簡短名稱。 當使用指定的群組傳送通知時，更新管理會使用簡短名稱。

3. 在 [動作] 中，輸入指定動作的名稱，例如**電子郵件通知**。

4. 針對 [動作類型]，選取適當的類型，例如 [電子郵件/簡訊/推送/語音]。

5. 選取 [編輯詳細資料]。

6. 在窗格中填入動作類型。 例如，如果使用 **電子郵件/SMS/Push/Voice**，請輸入動作名稱，選取 [ **電子郵件** ] 核取方塊，輸入有效的電子郵件地址，然後選取 **[確定]**。

    ![設定電子郵件動作群組](./media/configure-alerts/configure-email-action-group.png)

7. 在 [新增動作群組] 窗格中，選取 **[確定]**。

8. 如需警示電子郵件，您可以自訂電子郵件主旨。 選取 [建立規則] 下的 [自訂動作]，然後選取 [電子郵件主旨]。

9. 當您完成時，選取 [建立警示規則]。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure 監視器中的警示](../../azure-monitor/platform/alerts-overview.md)。

* 深入瞭解 [記錄查詢](../../azure-monitor/log-query/log-query-overview.md) ，以從 log Analytics 工作區取出和分析資料。

* [使用 Azure 監視器記錄來管理使用量和成本](../../azure-monitor/platform/manage-cost-storage.md)說明如何藉由變更您的資料保留期限來控制成本，以及如何分析和警示您的資料使用量。