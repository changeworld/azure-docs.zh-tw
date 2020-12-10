---
title: 教學課程：使用 Azure 應用程式組態來管理功能旗標
titleSuffix: Azure App Configuration
description: 在本教學課程中，您將了解如何使用 Azure 應用程式組態個別管理功能旗標與應用程式。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 0410a1cde12b9ef762d348a286d78b35f7b14bfd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932297"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>教學課程：在 Azure 應用程式組態中管理功能旗標

您可以將所有功能旗標儲存在 Azure 應用程式組態中，並從單一位置進行管理。 應用程式組態具有名為 **功能管理員** 的入口網站 UI，專供功能旗標使用。 應用程式組態也原生支援 .NET Core 功能旗標的資料結構描述。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在應用程式組態中定義和管理功能旗標。
> * 從應用程式存取功能旗標。

## <a name="create-feature-flags"></a>建立功能旗標

應用程式組態在 Azure 入口網站中的功能管理員提供用來建立和管理功能旗標的 UI，供您在應用程式中使用。

若要新增新的功能旗標：

1. 選取 [功能管理員]   > [+新增]  ，以新增功能旗標。

    ![功能旗標清單](./media/azure-app-configuration-feature-flags.png)

1. 輸入功能旗標的唯一索引鍵名稱。 您需要此名稱才能在程式碼中參考旗標。

1. 如有需要，您可以提供功能旗標的描述。

1. 設定功能旗標的初始狀態。 此狀態通常是「關閉」  或「開啟」  。 如果您為功能旗標新增篩選條件，「關閉」  狀態就會變更為「條件式」  。

    ![建立功能旗標](./media/azure-app-configuration-feature-flag-create.png)

1. 當狀態為「開啟」  時，選取 [新增篩選條件]  ，指定要用來限定狀態的任何其他條件。 請輸入內建或自訂的篩選鍵，然後選取 [+新增參數]  ，將一或多個參數與篩選條件產生關聯。 內建篩選條件包括：

    | Key | JSON 參數 |
    |---|---|
    | Microsoft.Percentage | {"Value":0-100 percent} |
    | Microsoft.TimeWindow | {"Start":UTC time, "End":UTC time} |
    | Microsoft.Targeting | { "Audience":定義使用者、群組和推出百分比的 JSON Blob。 請在[此設定檔](https://github.com/microsoft/FeatureManagement-Dotnet/blob/master/examples/FeatureFlagDemo/appsettings.json)的 `EnabledFor` 元素底下參閱範例

    ![功能旗標篩選條件](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>更新功能旗標狀態

若要變更功能旗標的狀態值：

1. 選取 [功能管理員]。

1. 在您要修改的功能旗標右側選取省略符號 (**...**)，然後選取 [編輯]。

1. 為功能旗標設定新狀態。

## <a name="access-feature-flags"></a>存取功能旗標

功能管理員所建立的功能旗標會以一般的索引鍵值形式來儲存與擷取。 它們會保存在特殊的命名空間前置詞 `.appconfig.featureflag` 下。 若要檢視基礎的索引鍵值，請使用組態總管。 應用程式可以使用應用程式組態的組態提供者、SDK、命令列延伸模組和 REST API 來擷取這些值。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用應用程式組態來管理功能旗標和其狀態。 若要進一步了解應用程式組態和 ASP.NET Core 中的功能管理支援，請參閱下列文章：

* [在 ASP.NET Core 應用程式中使用功能旗標](./use-feature-flags-dotnet-core.md)
