---
title: FarmBeats 的嚴重損壞修復
description: 本文說明資料復原如何保護遺失資料的能力。
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683900"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats 的嚴重損壞修復

資料復原可防止您在像是 Azure 區域折迭的事件中遺失資料。 在此事件中，您可以開始容錯移轉，並復原儲存在 FarmBeats 部署中的資料。

資料復原不是 Azure FarmBeats 中的預設功能。 您可以設定 FarmBeats 用來將資料儲存在 Azure 配對區域中的必要 Azure 資源，以手動方式設定此功能。 使用主動–被動方法來啟用復原。

下列各節提供有關如何在 Azure FarmBeats 中設定資料復原的資訊：

- [啟用資料冗余](#enable-data-redundancy)
- [從線上備份還原服務](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>啟用資料冗余

FarmBeats 會將資料儲存在三個 Azure 第一方服務中，也就是**azure 儲存體**、 **Cosmos DB**和**時間序列深入解析**。 使用下列步驟，將這些服務的資料冗余啟用至配對的 Azure 區域：

1.  **Azure 儲存體**-遵循此指導方針，為您 FarmBeats 部署中的每個儲存體帳戶啟用資料冗余。
2.  **Azure Cosmos DB** -遵循此指導方針，為您的 FarmBeats 部署 Cosmos DB 帳戶啟用資料冗余。
3.  **Azure 時間序列深入解析（TSI）** -TSI 目前不提供資料冗余。 若要復原時間序列深入解析資料，請前往您的感應器/氣象合作夥伴，然後再次將資料推送至 FarmBeats 部署。

## <a name="restore-service-from-online-backup"></a>從線上備份還原服務

您可以起始容錯移轉，並復原針對您的 FarmBeats 部署所提及的每個資料存放區的資料。 復原 Azure 儲存體和 Cosmos DB 的資料之後，請在 Azure 配對的區域中建立另一個 FarmBeats 部署，然後將新的部署設定為使用還原的資料存放區（亦即 Azure 儲存體和 Cosmos DB）中的資料，方法是使用下列步驟：

1. [設定 Cosmos DB](#configure-cosmos-db)
2. [設定儲存體帳戶](#configure-storage-account)


### <a name="configure-cosmos-db"></a>設定 Cosmos DB

複製已還原之 Cosmos DB 的存取金鑰，並更新新的 FarmBeats Datahub Key Vault。


  ![災害復原](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 複製已還原之 Cosmos DB 的 URL，並在新的 FarmBeats Datahub App Service 設定中加以更新。 您現在可以在新的 FarmBeats 部署中刪除 Cosmos DB 帳戶。

  ![災害復原](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>設定儲存體帳戶

複製已還原之儲存體帳戶的存取金鑰，並在新的 FarmBeats Datahub Key Vault 中加以更新。

![災害復原](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 請確定在新的 FarmBeats Batch VM 設定檔中更新儲存體帳戶名稱。

![災害復原](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

同樣地，如果您已為您的加速器儲存體帳戶啟用資料復原，請遵循步驟2，在新的 FarmBeats 實例中更新加速器儲存體帳戶存取金鑰和名稱。
