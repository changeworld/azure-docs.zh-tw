---
title: 使用 Azure 遷移在本機伺服器上發現應用、角色和功能
description: 瞭解如何通過 Azure 遷移伺服器評估在本機伺服器上發現應用、角色和功能。
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453577"
---
# <a name="discover-machine-apps-roles-and-features"></a>發現電腦應用、角色和功能

本文介紹如何使用 Azure 遷移：伺服器評估發現本機伺服器上的應用程式、角色和功能。

發現應用清單以及本地電腦上運行的角色/功能可説明您識別和規劃針對工作負荷定制的 Azure 遷移路徑。

> [!NOTE]
> 應用發現當前僅針對 VMware VM 處於預覽狀態，僅限於發現。 我們尚未提供基於應用程式的評估。 基於機器的評估，適用于本地 VMware VM、超虛擬機器和物理伺服器。

使用 Azure 遷移的應用發現：伺服器評估是無代理的。 電腦和 VM 上未安裝任何內容。 伺服器評估使用 Azure 遷移設備與電腦來賓憑據一起執行發現。 設備使用 VMware API 遠端存取 VMware 電腦。


## <a name="before-you-start"></a>開始之前

1. 請確保[已創建](how-to-add-tool-first-time.md)Azure 遷移專案。
2. 確保將 Azure 遷移：伺服器評估工具[添加到](how-to-assess.md)專案中。
4. 使用 Azure 遷移設備檢查[VMware 要求](migrate-support-matrix-vmware.md#vmware-requirements)，以發現和評估 VMware VM。
5. 檢查部署 Azure 遷移設備[的要求](migrate-appliance.md)。
6. [驗證應用程式發現的支援和要求](migrate-support-matrix-vmware.md#application-discovery)。

## <a name="prepare-for-app-discovery"></a>準備應用發現

1. [準備設備部署](tutorial-prepare-vmware.md)。 準備包括驗證設備設置，以及設置設備將用於訪問 vCenter Server 的帳戶。
2. 請確保您有一個使用者帳戶（每個為 Windows 和 Linux 伺服器一個），並且對要發現應用、角色和功能的電腦具有管理員許可權。
3. [部署 Azure 遷移設備](how-to-set-up-appliance-vmware.md)以開始發現。 要部署設備，請將 OVA 範本下載並導入 VMware，以將設備創建為 VMware VM。 配置設備，然後將其註冊到 Azure 遷移。
2. 部署設備時，要開始連續發現，請指定以下內容：
    - 要連接到的 vCenter 伺服器的名稱。
    - 為設備為連接到 vCenter 伺服器而創建的憑據。
    - 為設備為連接到 Windows/Linux VM 而創建的帳戶憑據。

部署設備並提供憑據後，設備將開始持續發現 VM 中繼資料和效能資料，以及應用、功能和角色的發現。  應用發現的時間長取決於您擁有多少個 VM。 應用發現 500 個 VM 通常需要一個小時。

## <a name="review-and-export-the-inventory"></a>審核並匯出庫存

發現結束後，如果提供了應用發現憑據，則可以在 Azure 門戶中查看和匯出應用清單。

1. 在**Azure 遷移 - 伺服器** > **Azure 遷移：伺服器評估中**，按一下顯示的計數以打開 **"發現"伺服器**頁面。

    > [!NOTE]
    > 在此階段，您還可以選擇為已發現的電腦設置依賴項映射，以便可以視覺化要評估的電腦之間的依賴項。 [深入了解](how-to-create-group-machine-dependencies.md)。

2. 在**發現的應用程式**中，按一下顯示的計數。
3. 在 **"應用程式"清單**中，您可以查看發現的應用、角色和功能。
4. 要匯出庫存，請在 **"發現伺服器"** 中按一下"**匯出應用清單**"。

應用清單以 Excel 格式匯出和下載。 "**應用程式清單**"表顯示在所有電腦上發現的所有應用。

## <a name="next-steps"></a>後續步驟

- [創建有關](how-to-create-assessment.md)已發現伺服器的提升和移動遷移的評估。
- 使用[Azure 遷移：資料庫評估](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)SQL Server 資料庫。
