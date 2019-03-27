---
title: オンプレミスから Azure への復旧
titleSuffix: Azure Resiliency Technical Guidance
description: オンプレミス インフラストラクチャから Azure への復旧システムとその設計について説明します。
author: adamglick
ms.date: 08/18/2016
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: seojan19, resiliency
ms.openlocfilehash: 768e53e1024533b384c610378385c96d88d8571f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248547"
---
[!INCLUDE [header](../_includes/header.md)]

# <a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Azure の回復性技術ガイダンス:オンプレミスから Azure への復旧

Azure は、高可用性とディザスター リカバリーの目的で、オンプレミスのデータセンターから Azure への拡張を可能にする包括的なサービスのセットを提供します。

- **ネットワーク**:仮想プライベート ネットワークでは、オンプレミスのネットワークをクラウドに安全に拡張できます。
- **コンピューティング**:Hyper-V をオンプレミスで使用しているお客様は、既存の仮想マシン (VM) を Azure にリフト アンド シフトすることができます。
- **ストレージ**:StorSimple でファイル システムを Azure Storage に拡張します。 Azure Backup サービスでは、ファイルや SQL データベースを Azure Storage にバックアップできます。
- **データベースのレプリケーション**:SQL Server 2014 (またはそれ以降) の可用性グループでは、オンプレミスのデータに対して高可用性とディザスター リカバリーを実装できます。

## <a name="networking"></a>ネットワーク

Azure Virtual Network を使用すると、論理的に切り離されたセクションを Azure 内に作成し、IPsec 接続を使用してオンプレミス データセンターまたは単一のクライアント コンピューターに安全に接続できます。 Virtual Network を使用すると、Windows Server、メインフレーム、UNIX で実行中のシステムを含むオンプレミス システムにあるデータおよびアプリケーションへの接続性を保ちながら、Azure のスケーラブルなオンデマンドのインフラストラクチャを活用できます。 詳細については、 [Azure Network のドキュメント](/azure/virtual-network/virtual-networks-overview/) を参照してください。

## <a name="compute"></a>Compute

Hyper-V をオンプレミスで使用している場合は、"リフトとシフト" によって既存の仮想マシンを Azure、および Windows Server 2012 (またはそれ以降) を実行するサービス プロバイダーに移行できます。VM に変更を加えたり、VM 形式を変換したりする必要はありません。 詳細については、「[Azure 仮想マシン用のディスクと VHD について](/azure/virtual-machines/virtual-machines-linux-about-disks-vhds/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="azure-site-recovery"></a>Azure Site Recovery

サービスとしてのディザスター リカバリー (DRaaS) を希望する場合は、[Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)を利用できます。 Azure Site Recovery は、VMware、Hyper-V、および物理サーバーの包括的な保護を提供します。 Azure Site Recovery では、別のオンプレミス サーバーまたは Azure を復旧サイトとして使用することができます。 Azure Site Recovery の詳細については、 [Azure Site Recovery のドキュメント](https://azure.microsoft.com/documentation/services/site-recovery/)を参照してください。

## <a name="storage"></a>Storage

オンプレミスのデータのバックアップ サイトとして Azure を使用する場合、いくつかのオプションがあります。

### <a name="storsimple"></a>StorSimple

StorSimple は、オンプレミスのアプリケーション用のクラウド ストレージを安全かつ透過的に統合します。 また、パフォーマンスの高い階層型のローカルとクラウドのストレージ、ライブ アーカイブ、クラウドベースのデータ保護、障害復旧を実現する単一のアプライアンスを提供します。 詳細については、 [StorSimple 製品のページ](https://azure.microsoft.com/services/storsimple/)を参照してください。

### <a name="azure-backup"></a>Azure Backup

Azure Backup を使用すると、Windows Server 2012 (またはそれ以降)、Windows Server 2012 Essentials (またはそれ以降)、および System Center 2012 Data Protection Manager (またはそれ以降) で、使い慣れたバックアップ ツールによるクラウドでのバックアップを行うことができます。 これらのツールにより、バックアップのストレージの場所がローカル ディスクか Azure Storage かに関係なく、バックアップ管理のためのワークフローを確立できます。 データがクラウドにバックアップされたのち、許可されているユーザーは簡単にバックアップをサーバーに回復できます。

増分バックアップにより、ファイルの変更部分だけがクラウドに転送されます。 そうすることで、ストレージの効率的な使用、帯域幅の消費の削減、および複数のバージョンのデータの特定の時点への復旧が可能になります。 データ保持ポリシー、データ圧縮、データ転送スロットルなどの追加機能を使用することもできます。 Azure をバックアップの場所として使用すると、バックアップが自動的に "オフサイト" になるという明らかな利点があります。 これにより、オンサイトのバックアップ メディアを保護するための追加の要件が不要になります。

詳細については、「[Azure Backup とは](/azure/backup/backup-introduction-to-azure-backup/)」と「[Azure Backup への DPM データの構成](https://technet.microsoft.com/library/jj728752.aspx)」を参照してください。

## <a name="database"></a>Database

ハイブリッド IT 環境内の SQL Server データベースのディザスター リカバリー ソリューションを実現するには、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布、および Azure Blob Storage によるバックアップと復元を使用します。 これらすべてのソリューションは、Azure Virtual Machines で実行される SQL Server を使用します。

AlwaysOn 可用性グループは、オンプレミスとクラウドの両方にデータベースのレプリカが存在するハイブリッド IT 環境で使用できます。 これを次の図に示します。

![SQL Server AlwaysOn Availability Groups in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

データベース ミラーリングは、証明書ベースのセットアップにおいて、オンプレミスのサーバーとクラウドにまたがって実行することもできます。 次の図にこの概念を示します。

![SQL Server database mirroring in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

ログ配布を使用して、オンプレミスのデータベースを Azure 仮想マシン内の SQL Server データベースと同期することができます。

![SQL Server log shipping in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

最後に、オンプレミスのデータベースを Azure Blob Storage に直接バックアップできます。

![Back up SQL Server to Azure Blob storage in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

詳細については、「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)」と「[Azure Virtual Machines における SQL Server のバックアップと復元](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery/)」を参照してください。

## <a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Microsoft Azure でのオンプレミスの回復のためのチェックリスト

<!-- markdownlint-disable MD024 -->

### <a name="networking"></a>ネットワーク

1. このドキュメントの「ネットワーク」セクションを確認する。
2. Virtual Network を使用してオンプレミスをクラウドに安全に接続する。

### <a name="compute"></a>Compute

1. このドキュメントの「計算」セクションを確認する。
2. VM を Hyper-V と Azure 間に再配置する。

### <a name="storage"></a>Storage

1. このドキュメントの「Storage」セクションを確認する。
2. クラウド ストレージを使用するために StorSimple サービスを利用する。
3. Azure Backup サービスを使用する。

### <a name="database"></a>Database

1. このドキュメントの「データベース」セクションを確認する。
2. Azure VM でバックアップとしての SQL Server の使用を検討する。
3. AlwaysOn 可用性グループを設定する。
4. 証明書ベースのデータベース ミラーリングを構成する。
5. ログ配布を使用する。
6. オンプレミスのデータベースを Azure Blob Storage にバックアップする。

<!-- markdownlint-enable MD024 -->
