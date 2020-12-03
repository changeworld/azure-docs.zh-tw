---
title: 掃描如何偵測已刪除的資產
description: 本文說明 Azure 範疇帳戶在掃描期間如何偵測已刪除的資產。
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552356"
---
# <a name="how-scans-detect-deleted-assets"></a>掃描如何偵測已刪除的資產

本文說明 Azure 範疇如何使用掃描結果來偵測已刪除的資產。

## <a name="background-info"></a>背景資訊

Azure 範疇目錄只有在掃描資料存放區時，才會知道該資料存放區的狀態。 為了讓目錄知道檔案、資料表或容器是否已刪除，它會比較最後一個掃描輸出與目前的掃描輸出。 例如，假設您上次掃描 Azure Data Lake Storage Gen2 帳戶時，它會包含名為 *folder1* 的資料夾。 再次掃描相同的帳戶時， *folder1* 就會遺失。 因此，目錄會假設已刪除資料夾。

## <a name="detecting-deleted-files"></a>偵測已刪除的檔案

偵測遺失檔案的邏輯可與相同使用者以及不同使用者的多個掃描一起運作。 例如，假設使用者在 a、B 和 C 資料夾的 Data Lake Storage Gen2 資料存放區上執行一次性掃描。之後，相同帳戶中的不同使用者會在相同資料存放區的 C、D 和 E 資料夾上執行不同的一次性掃描。 因為資料夾 C 掃描兩次，所以類別目錄會檢查它是否有可能的刪除。 不過，資料夾 A、B、D 和 E 只掃描一次，而目錄將不會檢查是否有已刪除的資產。

若要從目錄中保留已刪除的檔案，請務必執行定期掃描。 掃描間隔很重要，因為在執行另一次掃描之前，目錄無法偵測到已刪除的資產。 因此，如果您在特定存放區上每個月執行一次掃描，則在您稍後執行下次掃描之前，目錄無法偵測到該存放區中任何已刪除的資料資產。

當您列舉大型資料存放區（例如 Data Lake Storage Gen2）時，有多種方式可 (包含列舉錯誤和卸載的事件) 遺漏資訊。 特定掃描可能會錯過檔案的建立或刪除。 因此，除非目錄是已刪除的檔案，否則它不會將它從目錄中刪除。 這項策略表示當掃描的資料存放區中不存在的檔案仍存在於目錄中時可能會發生錯誤。 在某些情況下，資料存放區可能需要經過兩次或三次的掃描，才能攔截某些已刪除的資產。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure 範疇目錄，請參閱 [快速入門：建立範疇帳戶](create-catalog-portal.md)。
