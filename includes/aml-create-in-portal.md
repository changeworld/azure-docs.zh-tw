---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 11/04/2019
ms.openlocfilehash: 8ccd3e6129f4a061eacf83a1f4e70174c697480f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73633623"
---
1. 使用您 Azure 訂閱的認證來登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在 Azure 入口網站的左上角，選取 [+建立資源]  。

    ![建立新的資源](media/aml-create-in-portal/create-workspace.gif)

1. 使用搜尋列尋找 **Machine Learning**。

1. 選取 [Machine Learning]  。

1. 在 [Machine Learning]  窗格中選取 [建立]  來開始操作。

1. 提供下列資訊來設定新的工作區：

   欄位|描述 
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。  
   訂用帳戶 |選取您要使用的 Azure 訂用帳戶。
   資源群組 | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 
   Location | 選取最接近您的使用者與資料資源的位置，以建立工作區。
   工作區版本 | 選取 [基本]  作為此教學課程的工作區類型。 工作區類型 (基本與企業) 會決定您可以存取的功能和定價。 此教學課程中的所有內容都可以使用基本或企業工作區來執行。

1. 在完成工作區的設定後，選取 [檢閱 + 建立]  。 

   > [!Warning] 
   > 在雲端中建立工作區可能需要數分鐘的時間。

   程序完成後，會出現部署成功訊息。 
 
 1. 若要檢視新的工作區，選取 [前往資源]  。

