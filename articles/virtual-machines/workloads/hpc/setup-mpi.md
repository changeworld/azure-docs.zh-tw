---
title: 設定 HPC 的訊息傳遞介面-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何為 Azure 上的 HPC 設定 MPI。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023985"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>設定 HPC 的訊息傳遞介面

「訊息傳遞介面」（MPI）工作負載是傳統 HPC 工作負載的重要部分。 Azure 上已啟用 SR-IOV 的 VM 大小幾乎可讓您使用任何 MPI 的類別。 

在 Vm 上執行 MPI 作業需要在租使用者中設定分割區索引鍵（p 金鑰）。 請遵循[探索資料分割索引鍵](#discover-partition-keys)一節中的步驟，以取得判斷 p 索引鍵值的詳細資訊。

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx)提供 IB 的最佳效能，並可與 MPICH 和 OpenMPI 搭配運作。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

如先前所述安裝 UCX。

```bash
sudo yum install –y openmpi
```

組建 OpenMPI。

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

執行 OpenMPI。

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

如先前所述，檢查您的分割區索引鍵。

## <a name="mpich"></a>MPICH

如先前所述安裝 UCX。

組建 MPICH。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

正在執行 MPICH。

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

如先前所述，檢查您的分割區索引鍵。

## <a name="mvapich2"></a>MVAPICH2

組建 MVAPICH2。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

正在執行 MVAPICH2。

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>平臺 MPI 社區版本

安裝平臺 MPI 所需的套件。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

遵循安裝程式。

## <a name="intel-mpi"></a>Intel MPI

[下載 INTEL MPI](https://software.intel.com/mpi-library/choose-download)。

視版本而定，變更 I_MPI_FABRICS 環境變數。 若是 Intel MPI 2018，請`I_MPI_FABRICS=shm:ofa`使用2019的，使用`I_MPI_FABRICS=shm:ofi`。

根據預設，進程固定可正常運作15、30和 60 PPN。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基準測試

[下載 OSU MPI 基準](http://mvapich.cse.ohio-state.edu/benchmarks/)核對總和解壓縮。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

使用特定 MPI 程式庫建立效能評定：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基準測試位於`mpi/`資料夾底下。


## <a name="discover-partition-keys"></a>探索分割區索引鍵

探索資料分割索引鍵（p-金鑰），以與相同租使用者內的其他 Vm （可用性設定組或 VM 擴展集）進行通訊。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

兩者中的較大者就是應該搭配 MPI 使用的租使用者金鑰。 範例：如果下列是 p 按鍵，0x800b 應該與 MPI 搭配使用。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用預設（0x7fff）分割區索引鍵以外的資料分割。 UCX 需要清除 p 金鑰的 MSB。 例如，將 UCX_IB_PKEY 設定為0x800b 的0x000b。

另請注意，只要租使用者（A v 或 VMSS）存在，PKEYs 就會維持不變。 即使新增/刪除節點也是如此。 新的租使用者會取得不同的 PKEYs。


## <a name="set-up-user-limits-for-mpi"></a>設定 MPI 的使用者限制

設定 MPI 的使用者限制。

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>設定 MPI 的 SSH 金鑰

為需要的 MPI 類型設定 SSH 金鑰。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上述語法假設有共用的主目錄，否則就必須將 ssh 目錄複寫到每個節點。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 上的[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 。
