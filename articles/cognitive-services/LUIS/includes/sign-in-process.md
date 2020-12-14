---
title: 包含檔案
description: 包含檔案
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371301"
---
## <a name="sign-in-to-luis-portal"></a>登入 LUIS 入口網站

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

LUIS 的新使用者必須遵循此程序：

1. 登入 [LUIS 入口網站](https://www.luis.ai)，選取您的國家/地區並同意使用規定。 如果您看到的是 **我的應用程式**，表示已有 LUIS 資源存在，您應直接跳到建立應用程式的步驟。 您將會有兩個註冊選項：

    * 使用 Azure 資源 (建議選項) - 可讓您將 LUIS 帳戶與新的或現有的 Azure 撰寫資源連結。 這相當於註冊已遷移。 您稍後不需要進行[移轉程序](../luis-migration-authoring.md#what-is-migration)。

    * 使用試用金鑰。 這可讓您使用不需要設定的試用資源來登入 LUIS。 如果您選擇此選項，最終將需要[遷移帳戶](../luis-migration-authoring.md#migration-steps)並且將應用程式連結至撰寫資源。

1. 在顯示的 [選擇撰寫] 視窗中，尋找您的 Azure 訂用帳戶，然後尋找 LUIS 撰寫資源。 如果您沒有資源，可以建立新的資源。

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="選擇 Language Understanding 撰寫資源的類型。":::
    
    在建立新的撰寫資源時，請提供下列資訊：
    * **租用戶名稱** - Azure 訂用帳戶所關聯的租用戶。
    * **Azure 訂用帳戶名稱** - 要作為資源收費對象的訂用帳戶。
    * **Azure 資源群組名稱** - 您選擇或建立的自訂資源群組名稱。 資源群組可讓您將 Azure 資源分組以方便存取和管理。
    * **Azure 資源名稱** - 您選擇的自訂名稱，用來作為撰寫和預測端點查詢 URL 的一部分。
    * **定價層** - 定價層會決定每秒和每月的交易上限。


