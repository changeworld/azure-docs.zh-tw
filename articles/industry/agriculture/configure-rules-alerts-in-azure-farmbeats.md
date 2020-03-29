---
title: 設定規則和管理警示
description: 描述如何在伺服器場Beats中配置規則和管理警報
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482981"
---
# <a name="configure-rules-and-manage-alerts"></a>設定規則和管理警示

Azure FarmBeats 允許您根據業務邏輯創建規則，此外還允許您從伺服器場中部署的感應器和設備流動的感應器資料。 每當感應器值超過閾值時，規則都會在系統中觸發警報。 通過查看和分析閾值後創建的警報，您可以快速對任何問題採取行動並獲得所需的解決方案。

## <a name="create-rule"></a>建立規則

1. 在主頁上，轉到**規則**。
2. 選擇**新規則**。 將顯示"新規則"視窗。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. 輸入**規則名稱**和**規則描述**，然後從 **"選擇伺服器場"** 下拉式功能表中選擇伺服器場。
4. 鍵入伺服器場名稱以選擇同一視窗中顯示的伺服器場和 **"條件"** 部分。  

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. 在 **"條件"** 中，輸入**度量值**、**運算子**和**值**的值。
6. 在 **"度量值**"下拉式功能表中鍵入度量值名稱。
7. 選擇 **"添加條件**"以向規則添加更多條件。
8. 選擇**嚴重性級別**。
9. 在 **"操作"中**，打開**啟用電子郵件**的切換按鈕以啟用電子郵件警報。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 輸入要向其發送電子郵件提醒的**電子郵件地址**，以及 **"電子郵件主題**"和 **"附加說明**"。  
11. 在 **"規則狀態**"中，打開 **"已啟用**"切換按鈕以啟用或禁用規則。
    您可以查看受規則影響的設備數。
12. 選擇 **"應用"** 以創建規則。

## <a name="view-rule"></a>檢視規則

"**伺服器場"** 頁顯示可用規則的清單。 選擇**規則名稱**。 視窗顯示適用于所選規則的以下詳細資訊：
 - 規則名稱
 - 連結到規則關聯的伺服器場
 - 建立日期
 - 上次更新日期
 - 嚴重性層級
 - 規則狀態
 - 條件清單  
 - 受規則影響的設備數

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>編輯規則

要編輯規則，請按照以下步驟操作：

1. 在主頁上，從左側導航功能表中選擇 **"規則**"。
   將顯示規則視窗。
2. 選擇要為其編輯的規則。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 從動作列中選擇 **"編輯**"，"**編輯規則"** 視窗將顯示。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. 更改**規則名稱**和**規則說明**，然後從 **"選擇伺服器場"** 下拉式功能表中選擇伺服器場。
5. 鍵入伺服器場名稱以選擇**伺服器場，條件**將顯示在同一視窗中。  
6. 在 **"條件**"中，編輯**度量值**、**運算子**和**值**。
7. 在 **"度量值**"下拉式功能表中鍵入度量值名稱。
8. 選擇 **"添加條件**"以向規則添加/編輯條件。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  選擇**嚴重性級別**。  
10. 在 **"操作"中**，打開**啟用電子郵件**的切換按鈕以啟用電子郵件警報。
11. 編輯要發送電子郵件提醒的**電子郵件地址**，以及 **"電子郵件主題**"和 **"附加說明**"。  
12. 在 **"規則狀態**"中，打開 **"已啟用**"切換按鈕以啟用或禁用規則。
您可以查看受此規則影響的設備數。
13. 選擇 **"應用"** 以編輯規則。

## <a name="change-rule-status"></a>更改規則狀態

要更改規則的狀態，請按照以下步驟操作：

1. 在主頁上，從左側導航功能表中選擇 **"規則**"。 將顯示規則視窗。
2. 選擇要為其更改狀態的規則。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 從動作列中選擇 **"更改狀態**"。 將顯示 **"更改狀態"** 視窗。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. 使用 **"更改狀態**"切換按鈕更改規則狀態。
   您可以查看受規則影響的設備數。
4. 選擇 **"應用"** 以更改規則狀態。

## <a name="delete-rule"></a>刪除規則

要刪除規則，請按照以下步驟操作：

1. 在主頁上，從左側導航功能表中選擇 **"規則**"。 將顯示規則視窗。
2. 選擇要為其刪除的規則。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 從動作列中選擇 **"刪除**"。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. 將顯示"**刪除規則**"對話方塊。 選擇 **"刪除**"。
