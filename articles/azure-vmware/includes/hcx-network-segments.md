---
title: VMware HCX 網路區段
description: VMware HCX 需要四個網路。
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999618"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX 需要四個網路：

- **管理網路：** 一般來說，其與 vSphere 叢集上使用的是相同管理網路。 請至少在此網路區段上識別 VMware HCX 的兩個 IP。 您可能需要較大的數量，視部署而定。

   > [!NOTE]
   > 我們建議的方法是建立 /26 網路。 在 /26 網路上，您最多可使用 10 個服務網格和 60 個網路擴充項 (每個服務網格 1 個擴充項)。 您可以使用 Azure VMware 解決方案私人雲端，為每個網路擴充項延展八個網路。
   >
   
- **vMotion 網路：** 一般來說，其與 vSphere 叢集上用於 vMotion 的網路相同。  請至少在此網路區段上識別 VMware HCX 的兩個 IP。 您可能需要較大的數量，視部署而定。  

   vMotion 網路必須在分散式虛擬交換器或 vSwitch0 上公開。 如果不是，請修改環境。

   > [!NOTE]
   > 此網路可以是私人 (未路由的) 網路。

- **上行網路：** 您想要建立新的 VMware HCX 上行網路，並透過連接埠群組將其延伸至您的 vSphere 叢集。 請至少在此網路區段上識別 VMware HCX 的兩個 IP。 您可能需要較大的數量，視部署而定。  

   > [!NOTE]
   > 我們建議的方法是建立 /26 網路。 在 /26 網路上，您最多可使用 10 個服務網格和 60 個網路擴充項 (每個服務網格 1 個擴充項)。 您可以使用 Azure VMware 解決方案私人雲端，為每個網路擴充項延展八個網路。
   >
   
- **複寫網路：** 這是選擇性的。 您想要建立新的 VMware HCX 複寫網路，並透過連接埠群組將該網路延伸至您的 vSphere 叢集。 請至少在此網路區段上識別 VMware HCX 的兩個 IP。 您可能需要較大的數量，視部署而定。

   > [!NOTE]
   > 只有在內部部署叢集主機使用專用複寫 VMkernel 網路時，才可進行此設定。  如果您的內部部署叢集未定義專用複寫 VMkernel 網路，則不需要建立此網路。
