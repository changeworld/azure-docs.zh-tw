---
title: 包含檔案
description: 包含檔案
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659862"
---
>[!IMPORTANT]
>您可以使用您所建立的資源，作為其他 Azure Machine Learning 教學課程和操作說明文章的先決條件。

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用所建立的任何資源，請刪除整個資源群組，以免產生任何費用。

1. 在 Azure 入口網站中，於視窗左側選取 [資源群組]  。
 
   ![在 Azure 入口網站中刪除資源群組](./media/aml-ui-cleanup/delete-resources.png)

1. 在清單中，選取您所建立的資源群組。

1. 選取 [刪除資源群組]  。

刪除資源群組同時會刪除您在設計工具中建立的所有資源。 

### <a name="delete-individual-assets"></a>刪除個別資產

在建立實驗的設計工具中，藉由選取個別資產，再選取 [刪除]  按鈕，即可刪除個別資產。

您在這裡建立的計算目標會在不使用時*自動調整*為零個節點。 如此可將費用降至最低。 如果您想要刪除計算目標，請採取下列步驟：

![刪除資產](./media/aml-ui-cleanup/delete-asset.png)

您可以選取每個資料集並選取 [取消註冊]  ，從工作區中將資料集取消註冊。

![取消註冊資料集](./media/aml-ui-cleanup/unregister-dataset1225.png)

若要刪除資料集，請使用 Azure 入口網站或 Azure 儲存體總管移至儲存體帳戶，並手動刪除這些資產。


