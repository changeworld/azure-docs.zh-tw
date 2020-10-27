---
title: VMware HCX 網路區段
description: VMware HCX 需要四個網路。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173625"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX 需要四個網路：

- **管理網路：** 一般來說，其與 vSphere 叢集上使用的是相同管理網路。 請至少在此網路區段上識別 VMware HCX 的兩個 IP。 (視您的部署而定，可能需要較大的數量)。

- **vMotion 網路：** 一般來說，其與 vSphere 叢集上用於 vMotion 的網路相同。  請至少在此網路區段上識別 VMware HCX 的兩個 IP。 (視您的部署而定，可能需要較大的數量)。  

   vMotion 網路必須在分散式虛擬交換器或 vSwitch0 上公開。 如果不是，請修改環境。

   > [!NOTE]
   > 如果此網路未路由 (私人)，這是正常的。

- **上行網路：** 您想要建立新的 VMware HCX 上行網路，並透過連接埠群組將其延伸至您的 vSphere 叢集。 請至少在此網路區段上識別 VMware HCX 的兩個 IP。 (視您的部署而定，可能需要較大的數量)。  

   > [!NOTE]
   > 建議的方法是建立 /29 網路，但所有的網路大小都可行。

- **複寫網路：** 您想要建立新的 VMware HCX 複寫網路，並透過連接埠群組將該網路延伸至您的 vSphere 叢集。 請至少在此網路區段上識別 VMware HCX 的兩個 IP。 (視您的部署而定，可能需要較大的數量)。

   > [!NOTE]
   > 建議的方法是建立 /29 網路，但所有的網路大小都可行。