---
title: 在 Azure 遷移伺服器評估中設置無代理依賴關係分析
description: 在 Azure 遷移伺服器評估中設置無代理依賴項分析。
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455701"
---
# <a name="set-up-agentless-dependency-visualization"></a>設置無代理依賴項視覺化 

本文介紹如何在 Azure 遷移：伺服器評估中設置無代理依賴項分析。 [依賴關係分析](concepts-dependency-visualization.md)可説明您識別和瞭解要評估和遷移到 Azure 的電腦之間的依賴項。


> [!IMPORTANT]
> 使用 Azure 遷移：伺服器評估工具發現的無代理依賴項視覺化當前僅針對 VMware VM 處於預覽狀態。
> 功能可能有限或不完整。
> 此預覽版由客戶支援提供，可用於生產工作負荷。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



## <a name="before-you-start"></a>開始之前

- [瞭解](concepts-dependency-visualization.md#agentless-analysis)無代理依賴項分析。
- [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)為 VMware VM 設置無代理依賴項視覺化的先決條件和支援要求
- 請確保[已創建](how-to-add-tool-first-time.md)Azure 遷移專案。
- 如果已創建專案，請確保[已添加](how-to-assess.md)Azure 遷移：伺服器評估工具。
- 請確保已設置[Azure 遷移設備](migrate-appliance.md)以發現本地電腦。 瞭解如何為[VMware](how-to-set-up-appliance-vmware.md) VM 設置設備。 設備發現本地電腦，並將中繼資料和效能資料發送到 Azure 遷移：伺服器評估。


## <a name="current-limitations"></a>目前的限制

- 現在，您無法在依賴項分析視圖中從組中添加或刪除伺服器。
- 一組伺服器的依賴項映射當前不可用。
- 目前，依賴項資料無法以表格格式下載。

## <a name="create-a-user-account-for-discovery"></a>創建用於發現的使用者帳戶

設置使用者帳戶，以便伺服器評估可以訪問 VM 進行發現。 [瞭解](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)客戶要求。


## <a name="add-the-user-account-to-the-appliance"></a>將使用者帳戶添加到設備

將使用者帳戶添加到設備。

1. 打開裝置管理應用。 
2. 導航到 **"提供 vCenter 詳細資訊**"面板。
3. 在 **"發現 VM 上的應用程式和依賴項**"中，按一下"**添加憑據**"
3. 選擇**作業系統**，為帳戶提供易記名稱和**使用者名**/**密碼**
6. 按一下 [儲存]****。
7. 按一下 **"保存"並開始發現**。

    ![添加 VM 使用者帳戶](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>啟動依賴項發現

選擇要在其上啟用依賴項發現的電腦。

1. 在**Azure 遷移：伺服器評估中**，按一下 **"發現伺服器**"。
2. 按一下**依賴項分析**圖示。
3. 按一下"**添加伺服器**"。
3. 在 **"添加伺服器"** 頁中，選擇發現相關電腦的設備。
4. 從機器清單中，選擇機器。
5. 按一下"**添加伺服器**"。

    ![啟動依賴項發現](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

您可以在開始依賴項發現後大約六小時視覺化依賴項。

## <a name="visualize-dependencies"></a>視覺化依賴項

1. 在**Azure 遷移：伺服器評估中**，按一下 **"發現伺服器**"。
2. 搜索要查看的電腦。
3. 在 **"依賴項"** 列中，按一下"**查看依賴項"**
4. 使用 **"時間持續時間**"下拉下下拉清單更改要查看地圖的時間段。
5. 展開**用戶端**組以列出依賴于所選電腦的電腦。
6. 展開 **"埠"** 組以列出具有所選電腦依賴項的電腦。
7. 要導航到任何從屬電腦的地圖視圖，請按一下電腦名稱稱>**載入伺服器映射**

    ![展開伺服器埠組和載入伺服器映射](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![展開用戶端組 ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 展開所選電腦以查看每個依賴項的進程級詳細資訊。

    ![展開伺服器以顯示進程](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 依賴項的過程資訊並不總是可用的。 如果不可用，則依賴項將用標記為"未知進程"的進程進行描述。

## <a name="stop-dependency-discovery"></a>停止依賴項發現

選擇要停止依賴項發現的電腦。

1. 在**Azure 遷移：伺服器評估中**，按一下 **"發現伺服器**"。
2. 按一下**依賴項分析**圖示。
3. 按一下 **"刪除伺服器**"。
3. 在 **"刪除伺服器"** 頁中，選擇正在發現要停止依賴項發現的 VM**的設備**。
4. 從機器清單中，選擇機器。
5. 按一下 **"刪除伺服器**"。


## <a name="next-steps"></a>後續步驟

[對機器進行分組](how-to-create-a-group.md)以進行評估。
