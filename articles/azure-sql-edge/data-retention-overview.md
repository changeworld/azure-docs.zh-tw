---
title: 資料保留原則總覽-Azure SQL Edge
description: 瞭解 Azure SQL Edge 中的資料保留原則
keywords: SQL Edge，資料保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976341"
---
# <a name="data-retention-overview"></a>資料保留概觀

從已連線的 IoT 裝置收集和儲存資料，對於推動和得到營運和商業見解而言很重要。 但是，如果有來自這些裝置的資料量，組織就很重要地規劃要保留的資料量，以及資料細微性。 雖然所有資料細微性都需要保留所有資料，但不一定都是可行的。 此外，可保留的資料量會受限於 IoT 或 Edge 裝置上可用的儲存體數量。 

在 Azure SQL Edge 資料庫中，系統管理員可以在 SQL Edge 資料庫及其基礎資料表上定義資料保留原則。 一旦定義資料保留原則，就會執行背景系統工作，以清除使用者資料表中任何過時的 (舊) 資料。 

> [!Note]
> 從資料表中清除後的資料無法復原。 復原已清除資料的唯一可能方式，就是從較舊的備份還原資料庫。

快速入門：

- [啟用及停用資料保留原則](data-retention-enable-disable.md)
- [使用保留原則管理歷程記錄資料](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>資料保留的運作方式

若要設定資料保留，您可以使用 DDL 語句。 如需詳細資訊，請 [啟用和停用資料保留原則](data-retention-enable-disable.md)。 若要自動刪除過時的記錄，您必須先針對您想要在該資料庫中清除的資料庫和資料表啟用資料保留。 

設定資料表的資料保留期之後，會執行背景工作來識別資料表中的過時記錄並刪除這些記錄。 如果基於某些原因，自動清除工作不會執行或無法跟上刪除作業，則可以在這些資料表上執行手動清除操作。 如需有關自動和手動清除的詳細資訊，請參閱 [自動和手動清除](data-retention-cleanup.md)。

## <a name="limitations-and-restrictions"></a>限制事項

- 當資料庫從完整備份還原或重新附加時，會自動停用資料保留（若已啟用）。 
- 無法啟用時態歷程記錄資料表的資料保留
- 無法更改資料保留篩選 colomn。 若要變更資料行，請停用資料表的資料保留。  

## <a name="next-steps"></a>後續步驟

- [SQL Edge 中採用 ONNX 的機器學習與人工智慧](onnx-overview.md)。
- [使用 SQL Edge 和 IoT Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的資料串流](stream-data.md)
