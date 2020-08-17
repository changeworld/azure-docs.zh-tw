---
title: 部署概觀 - Avere vFXT for Azure
description: 瞭解如何使用此總覽部署 Avere vFXT for Azure 叢集。 相關文章具有特定的部署指示。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271221"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 部署概觀

此文章提供讓 Avere vFXT for Azure 叢集運作所需之步驟的概觀。

從 Azure Marketplace 建立 vFXT 叢集前後，必須執行幾項工作。 確切了解從開始到結束的程序將有助於預估所需精力。

## <a name="deployment-steps"></a>部署步驟

在[規劃您的系統](avere-vfxt-deploy-plan.md)之後， 您就可以開始建立您的 Avere vFXT 叢集。

Azure Marketplace 中的 Azure Resource Manager 範本會收集所需的資訊，並自動部署整個叢集。

在 vFXT 叢集啟動並執行之後，仍有一些設定步驟必須在使用之前採取。 如果您已建立新的 Blob 儲存體容器，您會想要將資料移至該容器。 如果您使用 NAS 儲存體系統，您必須在建立叢集之後新增它。 您會想要將用戶端連線到叢集。

以下是所有步驟的概觀。

1. 設定必要條件

   建立 VM 訂閱 之前，您必須先為 Avere vFXT 專案建立新訂用帳戶、設定訂用帳戶擁有權、檢查配額並在需要時要求增加配額，以及接受使用 Avere vFXT 軟體的條款。 閱讀[準備建立 Avere vFXT](avere-vfxt-prereqs.md) 以取得詳細指示。

1. 建立 Avere vFXT 叢集

   使用 Azure Marketplace 建立 Avere vFXT for Azure 叢集。 範本會收集所需的資訊，並執行指令碼以建立最終產品。

   建立叢集時須執行下列步驟，而這全部都可透過 Marketplace 範本來完成：

   * 視需要建立新的網路基礎結構和資源群組
   * 建立叢集控制器

     叢集控制器是一個簡單 VM，位於與 Avere vFXT 叢集相同的虛擬網路中，且具有建立和管理叢集所需的自訂軟體。 控制器會建立 vFXT 節點並構成叢集，而且它也提供命令列介面讓您在叢集生命週期內管理叢集。

     如果您在部署期間建立新的虛擬網路或子網，您的控制器將會有公用 IP 位址。 這表示控制器可以作為跳躍主機，以便從虛擬網路外部連線到叢集。

   * 建立叢集節點 Vm

   * 從個別節點建立叢集

   * （選擇性）建立新的 Blob 容器，並將其設定為叢集的後端儲存體

   [部署 vFXT](avere-vfxt-deploy.md)叢集時，會詳細說明叢集的建立。

1. 設定叢集

   連線到 Avere vFXT 設定介面 (Avere 控制台) 以自訂叢集設定。 如果您使用硬體儲存體或其他 Blob 容器，請加入宣告支援監視，並新增您的儲存體系統。

   * [存取 vFXT 叢集](avere-vfxt-cluster-gui.md)
   * [啟用支援](avere-vfxt-enable-support.md)
   * [設定儲存體](avere-vfxt-add-storage.md) (若有需要)

1. 裝載用戶端

   依照[裝載 Avere vFXT 叢集](avere-vfxt-mount-clients.md)中的指導方針執行，以了解負載平衡方式與用戶端電腦應該如何裝載叢集。

1. 新增資料 (若有需要)

   由於 Avere vFXT 是可擴充的多用戶端快取，因此將資料移至新的後端儲存體容器的最佳方式，就是使用多個用戶端的多執行緒檔案複寫原則。

   如果您需要將工作集資料移至新的 Blob 容器或其他後端儲存體系統，請依照將 [資料移至 vFXT](avere-vfxt-data-ingest.md)叢集的指示操作。

## <a name="next-steps"></a>後續步驟

繼續 [準備建立 Avere vFXT](avere-vfxt-prereqs.md) ，以完成必要的工作。
