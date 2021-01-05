---
title: IT 服務管理連接器-使用 BMC 在 Azure 監視器中進行安全匯出
description: 本文說明如何在 Azure 監視器的安全匯出中，將您的 ITSM 產品/服務與 BMC 連接在一起。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 598ce86aef41dd791099b49edccd6a55b3e43cdd
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839403"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>將 BMC Helix 連接到 Azure 監視器

下列各節提供有關如何在 Azure 中連接您的 BMC Helix 產品和安全匯出的詳細資料。

## <a name="prerequisites"></a>必要條件

確定您符合下列必要條件：

* Azure AD 已註冊。
* 您有支援的 BMC Helix (19.08 版或更新版本) 的多雲端服務管理版本。

## <a name="configure-the-bmc-helix-connection"></a>設定 BMC Helix 連接

1. 在 BMC Helix 環境中使用下列程式，以便取得安全匯出的 URI：

   1. 登入 Integration Studio。
   1. 搜尋 **從 Azure 警示流程建立事件** 。
   1. 複製 webhook URL。
   
   ![Integration Studio 中 webhook U R L 的螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. 依照下列版本的指示進行：
   * [針對20.02 版的 Azure 監視器啟用預建整合](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * [針對19.11 版的 Azure 監視器啟用預建整合](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

3. 在 BMC Helix 中設定連線的一部分，請移至您的整合 BMC 實例，並遵循下列指示：

   1. 選取 [ **目錄**]。
   2. 選取 **Azure 警示**。
   3. 選取 [ **連接器**]。
   4. 選取 **[** 設定]。
   5. 選取 [ **新增連接** 設定]。
   6. 填入設定區段的資訊：
      - **名稱**：組成您自己的。
      - **授權類型**： **無**
      - **描述**：組成您自己的。
      - **網站**： **雲端**
      - **實例數目**： **2**，預設值。
      - **檢查**：依預設選取以啟用使用方式。
      - Azure 租使用者識別碼和 Azure 應用程式識別碼取自您稍早定義的應用程式。

![顯示 BMC 設定的螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)
