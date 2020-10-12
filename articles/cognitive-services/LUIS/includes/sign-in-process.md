---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541421"
---
## <a name="sign-in-to-luis-portal"></a>登入 LUIS 入口網站

LUIS 的新使用者必須遵循此程序：

1. 登入 [LUIS 入口網站](https://www.luis.ai)，選取您的國家/地區並同意使用規定。 如果您看到的是**我的應用程式**，表示已有 LUIS 資源存在，您應直接跳到建立應用程式的步驟。 對於支援的區域，請造訪[撰寫和發佈區域及相關聯的金鑰](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)。

1. 選取 [建立 Azure 資源]，然後選取 [建立要作為應用程式遷移目的地的撰寫資源]。

    ![選擇 Language Understanding 撰寫資源的類型](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 填入資源的詳細資料。

    ![顯示 [建立用於撰寫的新資源] 窗格的螢幕擷取畫面。](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    在**建立新的撰寫資源**時，請提供下列資訊：

    * **資源名稱** - 您選擇的自訂名稱，用來作為撰寫和預測端點查詢 URL 的一部分。
    * **租用戶** - Azure 訂用帳戶所關聯的租用戶。
    * **訂用帳戶名稱** - 要作為資源收費對象的訂用帳戶。
    * **資源群組** - 您選擇或建立的自訂資源群組名稱。 資源群組可讓您將 Azure 資源分組以方便存取和管理。
    * **位置** - 位置選擇是以**資源群組**選取項目作為依據。
    * **定價層** - 定價層會決定每秒和每月的交易上限。

1. 隨即會顯示所要建立資源的摘要。 選取 [下一步] 。

    ![此螢幕擷取畫面顯示的 [歡迎使用] 頁面，其中包含連結至您的 Azure 帳戶的選項。](../media/sign-in/sign-in-confirm-key-selection.png)

1. 請選取 [繼續] 來表示確認。

    ![螢幕擷取畫面顯示您連結至 Azure 帳戶之後的 [歡迎使用] 頁面。](../media/sign-in/sign-in-confirm-continue.png)
