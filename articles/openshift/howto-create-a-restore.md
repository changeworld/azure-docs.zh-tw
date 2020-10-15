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
ms.openlocfilehash: 9eac34d643ba0df4be79a064858c580c884de727
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078556"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>建立 Azure Red Hat OpenShift 4 叢集應用程式還原

在本文中，您將準備您的環境，以建立 Azure Red Hat OpenShift 4 叢集應用程式還原。 您將學習如何：

> [!div class="checklist"]
> * 設定必要條件，並安裝必要的工具
> * 建立 Azure Red Hat OpenShift 4 應用程式還原

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.6.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>開始之前

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>建立 Azure Red Hat OpenShift 4 應用程式備份

若要建立 Azure Red Hat OpenShift 4 應用程式備份，請參閱 [建立 Azure Red Hat OpenShift 4 備份](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>還原 Azure Red Hat OpenShift 4 應用程式

這些步驟可讓您還原先前使用 Velero 備份的應用程式。
您可以檢查叢集目前已辨識的備份清單，以查看有哪些備份可供還原。  若要執行此步驟，您需要執行下列命令：

_ (這個步驟假設您已在名為 "Velero" 的專案中安裝 Velero ) _

```bash
oc get backups -n velero
```

當您有想要還原的備份之後，您必須使用下列命令執行還原：

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

此步驟會建立在建立備份時，從上一個步驟備份的 Kubernetes 物件。

若要查看還原的狀態，請執行下列步驟：

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
當階段顯示時 `Completed` ，應還原您的 Azure Red Hat 4 應用程式。

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>使用包含的快照還原 Azure Red Hat OpenShift 4 應用程式


若要使用 Velero 來建立具有持續性磁片區的 Azure Red Hat OpenShift 4 應用程式的還原，您必須使用下列命令執行還原：

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
此步驟會建立在建立備份時，從上一個步驟備份的 Kubernetes 物件。

若要查看還原的狀態，請執行下列步驟：

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
當階段顯示時 `Completed` ，應還原您的 Azure Red Hat 4 應用程式。

如需如何使用 Velero 建立備份和還原的詳細資訊，請參閱以 [原生方式備份 OpenShift 資源](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>後續步驟

在本文中，已還原 Azure Red Hat OpenShift 4 叢集應用程式。 您已了解如何︰

> [!div class="checklist"]
> * 使用 Velero 建立 OpenShift v4 叢集應用程式還原
> * 使用 Velero 建立 OpenShift v4 叢集應用程式與快照集的還原


前進到下一篇文章，以瞭解 Azure Red Hat OpenShift 4 支援的資源。

* [Azure Red Hat OpenShift v4 支援的資源](supported-resources.md)


