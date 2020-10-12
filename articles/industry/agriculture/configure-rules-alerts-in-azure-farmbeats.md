---
title: 設定規則和管理警示
description: 描述如何在 FarmBeats 中設定規則和管理警示
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75482981"
---
# <a name="configure-rules-and-manage-alerts"></a>設定規則和管理警示

除了從伺服器陣列中部署的感應器和裝置流動的感應器資料以外，Azure FarmBeats 還可讓您建立以商務邏輯為基礎的規則。 每當感應器值跨越閾值時，規則就會觸發系統中的警示。 藉由查看並分析閾值之後所建立的警示，您可以快速處理任何問題，並取得所需的解決方案。

## <a name="create-rule"></a>建立規則

1. 在首頁上，移至 [ **規則**]。
2. 選取 [ **新增規則**]。 [新增規則] 視窗隨即顯示。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. 輸入 **規則名稱** 和 **規則描述** ，然後從 [ **選取伺服器** 陣列] 下拉式功能表中選取伺服器陣列。
4. 輸入您的伺服器陣列名稱，以選取 [伺服器陣列和 **條件** ] 區段會出現在相同的視窗中。  

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. 在 [ **條件**] 中，輸入 **Measure**、 **Operator** 和 **Value**的值。
6. 在 [ **量值** ] 下拉式功能表中輸入量值名稱。
7. 選取 [ **+ 新增條件** ]，將更多條件新增至規則。
8. 選取 **嚴重性層級**。
9. 在 [**已啟用電子郵件**切換] 按鈕上切換 **，以啟用**電子郵件警示。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 輸入您要傳送電子郵件警示的 **電子郵件地址** ，以及 **電子郵件** 主旨和 **其他注意事項**。  
11. 在 [ **規則狀態**] 中，切換至 [ **已啟用** ] 切換按鈕以啟用或停用規則。
    您可以查看將受規則影響的裝置數目。
12. 選取 **[** 套用] 以建立規則。

## <a name="view-rule"></a>檢視規則

[ **伺服器** 陣列] 頁面會顯示可用規則的清單。 選取 **規則名稱**。 視窗會顯示下列適用于所選規則的詳細資料：
 - 規則名稱
 - 連結至與規則相關聯的伺服器陣列
 - 建立日期
 - 上次更新日期
 - 嚴重性層級
 - 規則狀態
 - 條件清單  
 - 受規則影響的裝置數目

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>編輯規則

若要編輯規則，請遵循下列步驟：

1. 在首頁上，選取左側導覽功能表中的 [ **規則** ]。
   [規則] 視窗隨即顯示。
2. 選取您要編輯的規則。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 從動作列選取 [ **編輯** ]，[ **編輯規則** ] 視窗隨即顯示。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. 變更 **規則名稱**和 **規則描述** ，然後從 [ **選取伺服器** 陣列] 下拉式功能表中選取伺服器陣列。
5. 輸入您的伺服器陣列名稱，以選取伺服器陣列和 **條件** 出現在相同的視窗中。  
6. 在 [ **條件**] 中編輯 **量值**、 **運算子** 和 **值**。
7. 在 [ **量值** ] 下拉式功能表中輸入量值名稱。
8. 選取 [ **+ 新增條件** ] 以新增/編輯規則的條件。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  選取 **嚴重性層級**。  
10. 在 [**已啟用電子郵件**切換] 按鈕上切換 **，以啟用**電子郵件警示。
11. 編輯您想要傳送電子郵件警示的 **電子郵件地址** ，以及 **電子郵件** 主旨和 **其他注意事項**。  
12. 在 [ **規則狀態**] 中，切換至 [ **已啟用** ] 切換按鈕以啟用或停用規則。
您可以查看將受此規則影響的裝置數目。
13. 選取 **[** 套用] 以編輯規則。

## <a name="change-rule-status"></a>變更規則狀態

若要變更規則的狀態，請遵循下列步驟：

1. 在首頁上，選取左側導覽功能表中的 [ **規則** ]。 [規則] 視窗隨即顯示。
2. 選取您要變更其狀態的規則。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 從動作列選取 [ **變更狀態** ]。 [ **變更狀態** ] 視窗隨即顯示。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. 使用 [ **變更狀態** ] 切換按鈕來變更規則狀態。
   您可以查看將受規則影響的裝置數目。
4. 選取 **[** 套用] 以變更規則狀態。

## <a name="delete-rule"></a>刪除規則

若要刪除規則，請遵循下列步驟：

1. 在首頁上，選取左側導覽功能表中的 [ **規則** ]。 [規則] 視窗隨即顯示。
2. 選取您要刪除的規則。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 從動作列選取 [ **刪除** ]。

    ![專案 FarmBeats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. [ **刪除規則** ] 對話方塊隨即顯示。 選取 [刪除]。
