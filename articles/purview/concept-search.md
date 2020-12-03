---
title: '瞭解 Azure 範疇中的搜尋功能 (預覽版) '
description: 本文說明 Azure 範疇如何透過搜尋功能啟用資料探索。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552015"
---
# <a name="understand-search-features-in-azure-purview"></a>瞭解 Azure 範疇中的搜尋功能

本文概要說明 Azure 範疇中的搜尋體驗。 搜尋是範疇的核心平臺功能，可提供組織中的資料探索和資料使用治理體驗。

## <a name="search"></a>搜尋

範疇搜尋體驗是由受控搜尋索引提供支援。 在範疇中註冊資料來源之後，其中繼資料會由搜尋服務編制索引，以允許輕鬆探索。 索引會提供搜尋相關性功能，並藉由查詢數百萬個中繼資料資產來完成搜尋要求。 搜尋可協助您探索、瞭解和使用資料，充分發揮其最大價值。

範疇中的搜尋體驗是三個階段的流程：

1. 搜尋方塊會顯示包含最近使用之關鍵字和資產的歷程記錄。
1. 當您開始輸入按鍵時，搜尋會建議相符的關鍵字和資產。 
1. [搜尋結果] 頁面會顯示與所輸入關鍵字相符的資產。

## <a name="reduce-the-time-to-discover-data"></a>縮短探索資料的時間

資料探索是資料取用者的資料分析或資料治理工作負載的第一個步驟。 資料探索可能相當耗時，因為您可能不知道要在哪裡找到您想要的資料。 即使在找到資料之後，您可能會對是否可以信任資料，並對其採取相依性。 

在 Azure 範疇中搜尋的目標是藉由提供手勢來加速資料探索的程式，以快速找出重要的資料。

## <a name="recent-search-and-suggestions"></a>最近的搜尋和建議

許多時候，您可能會同時處理多個專案。 為了讓您更輕鬆地繼續先前的專案，範疇搜尋提供了查看最近搜尋關鍵字和建議的功能。 此外，您也可以從搜尋方塊下拉式清單中選取 [ **全部查看** ]，以管理最近的搜尋歷程記錄。

## <a name="filters"></a>篩選條件

篩選 (也稱為 *facet*) 是設計來補充搜尋。 篩選會顯示在 [搜尋結果] 頁面中。 搜尋結果頁面中的篩選準則包括分類、敏感度標籤、資料來源和擁有者。 使用者可以在篩選中選取特定值，以僅查看相符的資料資產，並將搜尋結果限制為相符的資產。

## <a name="hit-highlighting"></a>搜尋結果醒目提示

[搜尋結果] 頁面中的相符關鍵字會反白顯示，讓您更輕鬆地識別搜尋傳回資料資產的原因。 關鍵字比對可出現在多個欄位中，例如資料資產名稱、描述和擁有者。

即使已啟用點擊醒目提示，也可能不會明顯將資料資產納入搜尋的原因。 預設會搜尋所有屬性。 因此，可能會因為資料行層級屬性的相符而傳回資料資產。 而且，因為多個使用者可以使用自己的分類和描述來批註資料資產，所以並非所有的中繼資料都會顯示在搜尋結果清單中。

## <a name="sort"></a>Sort

使用者有兩個選項可排序搜尋結果。 他們可以依資產名稱或依預設搜尋相關性進行排序。

## <a name="search-relevance"></a>搜尋相關性

相關性是 [搜尋結果] 頁面中的預設排序次序。 搜尋相關性會尋找包含搜尋關鍵字 (部分或所有) 的檔。 包含許多搜尋關鍵字實例的資產會排名較高。 此外，也會不斷調整自訂評分機制，以改善搜尋相關性。

## <a name="next-steps"></a>後續步驟

* [快速入門：在 Azure 入口網站中建立 Azure 範疇帳戶](create-catalog-portal.md)
* [快速入門：使用 Azure PowerShell/Azure CLI 建立 Azure 範疇帳戶](create-catalog-powershell.md)
* [快速入門：使用範疇 Studio](use-purview-studio.md)
