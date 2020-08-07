---
title: 使用 Velero 建立 Azure Red Hat OpenShift 4 叢集應用程式還原
description: 瞭解如何使用 Velero 建立 Azure Red Hat OpenShift 叢集應用程式的還原
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 95e2b268da0785816423ec1ee9c8e31730d48ed8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854217"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>建立 Azure Red Hat OpenShift 4 叢集應用程式還原

在本文中，您將準備您的環境，以建立 Azure Red Hat OpenShift 4 叢集應用程式還原。 您將學習如何：

> [!div class="checklist"]
> * 設定必要條件並安裝必要的工具
> * 建立 Azure Red Hat OpenShift 4 應用程式還原

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 版2.6.0 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>開始之前

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>建立 Azure Red Hat OpenShift 4 應用程式備份

若要建立 Azure Red Hat OpenShift 4 應用程式備份，請參閱[建立 Azure Red Hat OpenShift 4 備份](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>還原 Azure Red Hat OpenShift 4 應用程式

這些步驟可讓您還原先前使用 Velero 備份的應用程式。
您可以檢查叢集目前所識別的備份清單，以查看有哪些備份可供還原。  若要執行此步驟，您必須執行下列命令：

_ (此步驟假設您已在名為 "Velero" 的專案中安裝 Velero ) _

```bash
oc get backups -n velero
```

一旦擁有您想要還原的備份，就必須使用下列命令來執行還原：

```bash
velero restore create --from-backup <name of backup from above output list>
```

此步驟將會建立在建立備份時，從上一個步驟中備份的 Kubernetes 物件。

若要查看還原的狀態，請執行下列步驟：

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
當階段顯示時 `Completed` ，應該會還原您的 Azure Red Hat 4 應用程式。

## <a name="next-steps"></a>後續步驟

在本文中，已還原 Azure Red Hat OpenShift 4 叢集應用程式。 您已了解如何︰

> [!div class="checklist"]
> * 使用 Velero 建立 OpenShift v4 叢集應用程式還原


前進到下一篇文章，以瞭解 Azure Red Hat OpenShift 4 支援的資源。

* [Azure Red Hat OpenShift v4 支援的資源](supported-resources.md)


