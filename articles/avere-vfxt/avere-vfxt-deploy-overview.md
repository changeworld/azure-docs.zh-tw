---
title: 部署概觀 - Avere vFXT for Azure
description: 部署 Avere vFXT for Azure 的概觀
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 78140fea74272dff6056bebfbd44ed9d55b0e1db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153678"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 部署概觀

此文章提供讓 Avere vFXT for Azure 叢集運作所需之步驟的概觀。

從 Azure Marketplace 建立 vFXT 叢集前後，必須執行幾項工作。 確切了解從開始到結束的程序將有助於預估所需精力。

## <a name="deployment-steps"></a>部署步驟

在[規劃您的系統](avere-vfxt-deploy-plan.md)之後， 您就可以開始建立您的 Avere vFXT 叢集。

Azure Marketplace 中的 Azure Resource Manager 範本會收集所需的資訊，並自動部署整個叢集。

vFXT 群集啟動並運行後，在使用它之前仍有一些配置步驟需要執行。 如果創建新的 Blob 存儲容器，則需要將資料移動到該容器。 如果使用 NAS 存儲系統，則需要在創建群集後添加它。 您需要將用戶端連接到群集。

以下是所有步驟的概觀。

1. 設定必要條件

   建立 VM 訂閱 之前，您必須先為 Avere vFXT 專案建立新訂用帳戶、設定訂用帳戶擁有權、檢查配額並在需要時要求增加配額，以及接受使用 Avere vFXT 軟體的條款。 閱讀[準備建立 Avere vFXT](avere-vfxt-prereqs.md) 以取得詳細指示。

1. 建立 Avere vFXT 叢集

   使用 Azure Marketplace 建立 Avere vFXT for Azure 叢集。 範本會收集所需的資訊，並執行指令碼以建立最終產品。

   建立叢集時須執行下列步驟，而這全部都可透過 Marketplace 範本來完成：

   * 根據需要創建新的網路基礎設施和資源組
   * 創建叢集控制器

     叢集控制器是一個簡單 VM，位於與 Avere vFXT 叢集相同的虛擬網路中，且具有建立和管理叢集所需的自訂軟體。 控制器會建立 vFXT 節點並構成叢集，而且它也提供命令列介面讓您在叢集生命週期內管理叢集。

     如果在部署期間創建新的虛擬網路或子網，則控制器將具有公共 IP 位址。 這意味著控制器可以充當從虛擬網路外部連接到群集的跳轉主機。

   * 創建叢集節點 VM

   * 從各個節點創建群集

   * 或者，創建新的 Blob 容器並將其配置為群集的後端存儲

   在[部署 vFXT 群集](avere-vfxt-deploy.md)中詳細介紹了群集創建。

1. 設定叢集

   連線到 Avere vFXT 設定介面 (Avere 控制台) 以自訂叢集設定。 加入宣告支援監視，如果使用硬體存儲或其他 Blob 容器，請添加存儲系統。

   * [存取 vFXT 叢集](avere-vfxt-cluster-gui.md)
   * [啟用支援](avere-vfxt-enable-support.md)
   * [設定儲存體](avere-vfxt-add-storage.md) (若有需要)

1. 裝載用戶端

   依照[裝載 Avere vFXT 叢集](avere-vfxt-mount-clients.md)中的指導方針執行，以了解負載平衡方式與用戶端電腦應該如何裝載叢集。

1. 新增資料 (若有需要)

   由於 Avere vFXT 是可擴展的多用戶端緩存，因此將資料移動到新的後端存儲容器的最佳方式是使用多用戶端多執行緒檔案複製策略。

   如果需要將工作集資料移動到新的 Blob 容器或其他後端存儲系統，請按照[將資料移動到 vFXT 群集](avere-vfxt-data-ingest.md)中的說明操作。

## <a name="next-steps"></a>後續步驟

繼續[準備創建 Avere vFXT](avere-vfxt-prereqs.md)以完成先決條件任務。
