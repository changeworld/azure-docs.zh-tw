---
title: 為 HPC 設置消息傳遞介面 - Azure 虛擬機器 |微軟文檔
description: 瞭解如何在 Azure 上為 HPC 設置 MPI。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023985"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>為 HPC 設置消息傳遞介面

消息傳遞介面 （MPI） 工作負載是傳統 HPC 工作負載的重要組成部分。 Azure 上啟用的 SR-IOV VM 大小幾乎可以使用任何 MPI 的味道。 

在 VM 上運行 MPI 作業需要跨租戶設置分區鍵（p 鍵）。 請按照["發現分區鍵](#discover-partition-keys)"部分中的步驟瞭解有關確定 p 鍵值的詳細資訊。

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx)在 IB 上提供最佳性能，並與 MPICH 和 OpenMPI 配合使用。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

按照前面所述安裝 UCX。

```bash
sudo yum install –y openmpi
```

構建 OpenMPI。

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

運行 OpenMPI。

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

如上所述，請檢查分區金鑰。

## <a name="mpich"></a>MPICH

按照前面所述安裝 UCX。

構建 MPICH。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

運行 MPICH。

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

如上所述，請檢查分區金鑰。

## <a name="mvapich2"></a>MVAPICH2

構建 MVAPICH2。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

運行 MVAPICH2。

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>平臺MPI社區版

安裝平臺 MPI 所需的套裝軟體。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

按照安裝過程操作。

## <a name="intel-mpi"></a>英特爾 MPI

[下載英特爾 MPI](https://software.intel.com/mpi-library/choose-download)。

根據版本更改I_MPI_FABRICS環境變數。 對於英特爾 MPI 2018，使用`I_MPI_FABRICS=shm:ofa`和 2019， 使用`I_MPI_FABRICS=shm:ofi`。

預設情況下，對於 15、30 和 60 PPN，進程固定工作正常。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基準

[下載 OSU MPI 基準](http://mvapich.cse.ohio-state.edu/benchmarks/)和取消。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

使用特定的 MPI 庫構建基準：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基準測試位於`mpi/`資料夾下。


## <a name="discover-partition-keys"></a>發現分區鍵

發現分區鍵（p 鍵），以便與同一租戶中的其他 VM（可用性集或 VM 規模集）進行通信。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

兩者中較大的一個是應與 MPI 一起使用的租戶金鑰。 示例：如果以下為 p 鍵，則應將 0x800b 與 MPI 一起使用。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用預設 （0x7fff） 分區鍵以外的分區。 UCX 要求清除 p 鍵的 MSB。 例如，將UCX_IB_PKEY設置為 0x000b 的 0x800b。

另請注意，只要租戶（AVSet 或 VMSS）存在，PKEY 將保持不變。 即使添加/刪除節點也是如此。 新租戶獲取不同的 PKEY。


## <a name="set-up-user-limits-for-mpi"></a>為 MPI 設置使用者限制

為 MPI 設置使用者限制。

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>為 MPI 設置 SSH 金鑰

為需要它的 MPI 類型設置 SSH 金鑰。

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

上述語法假定共用主目錄，否則必須將 .ssh 目錄複寫到每個節點。

## <a name="next-steps"></a>後續步驟

在 Azure 上瞭解有關[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多詳細資訊。
