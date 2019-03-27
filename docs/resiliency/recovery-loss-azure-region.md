---
title: Azure リージョンの損失からの復旧
description: 回復力のある高可用性のフォールト トレラント アプリケーションとその設計、およびディザスター リカバリーの計画について説明します。
author: adamglick
ms.date: 08/18/2016
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: 7f207bbc0bb0128126f9b828dc100d43553cb100
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242713"
---
[!INCLUDE [header](../_includes/header.md)]

# <a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Azure の回復性に関する技術ガイダンス: リージョン全体のサービス中断からの復旧

Azure はリージョンと呼ばれる物理的および論理的な単位に分割されます。 リージョンは、ごく近くにある 1 つ以上のデータセンターで構成されます。

まれに、1 つのリージョン全体の施設がネットワーク障害などでアクセスできなくなることがあります。 また、自然災害などにより、施設が完全に失われることもあります。 ここでは、複数のリージョンに分散するアプリケーションを作成するための Azure の機能について説明します。 そのように分散することで、1 つのリージョンの障害が他のリージョンに影響を与える可能性を最小限に抑えることができます。

## <a name="cloud-services"></a>クラウド サービス

### <a name="resource-management"></a>リソース管理

リージョン間でコンピューティング インスタンスを分散するには、各対象リージョンに個別のクラウド サービスを作成し、各クラウド サービスにデプロイ パッケージを発行します。 ただし、さまざまなリージョンのクラウド サービス間でのトラフィックの分散は、アプリケーション開発者またはトラフィック管理サービスによって行われる必要があることに注意してください。

ディザスター リカバリーのために事前にデプロイする予備のロール インスタンスの数を決定することは、容量計画の重要な要素です。 フルスケールのセカンダリ デプロイを備えると、必要になったときに容量がすぐに使用できるようになりますが、実質的にコストが倍になります。 一般的なパターンは、重要なサービスを実行するのに十分な規模の小さなセカンダリ デプロイだけを持つ方法です。 容量を確保するためにも、セカンダリ環境の構成をテストするためにも、小規模のセカンダリ デプロイを作成することをお勧めします。

> [!NOTE]
> サブスクリプションのクォータは、容量が保証されているわけではありません。 クォータは単なる与信限度額です。 容量を保証するには、必要な数のロールをサービス モデルで定義し、ロールをデプロイする必要があります。

### <a name="load-balancing"></a>負荷分散

リージョン間でトラフィックの負荷分散を行うためには、トラフィック管理ソリューションが必要です。 Azure には [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)が用意されています。 また、同様のトラフィック管理機能を提供しているサードパーティのサービスを活用することもできます。

### <a name="strategies"></a>方法

リージョン間での分散コンピューティングを実装するには、多くの代替方法を利用できます。 これらは、特定のビジネス要件とアプリケーション環境に合わせる必要があります。 大まかに言えば、方法は以下のカテゴリに分類できます。

- **災害発生時の再デプロイ**:この方法では、アプリケーションは災害時に一から再デプロイされます。 これは、復旧時間が保証されている必要のない、重要性の低いアプリケーションに適しています。

- **ウォーム スペア (アクティブ/パッシブ)**:2 番目のホストされるサービスは別のリージョンに作成され、ロールは最小容量を保証するようにデプロイされます。しかし、このロールでは運用環境のトラフィックを受信しません。 この方法は、リージョン間にトラフィックを分散するように設計されていないアプリケーションに有用です。

- **ホット スペア (アクティブ/アクティブ)**:アプリケーションは複数のリージョンで運用負荷を受信するように設計されています。 各リージョンのクラウド サービスは、ディザスター リカバリーのために必要な容量よりも大きい容量で構成されている場合があります。 また、クラウド サービスは災害およびフェールオーバー時に必要に応じてスケールアウトする可能性があります。 この方法は、アプリケーション設計にかなりの投資を必要としますが、大きな利点があります。 たとえば、復旧時間が短いことが保証されていること、すべての復旧場所で連続してテストを実行できること、容量を有効に使用できることなどです。

分散設計の詳細については、このドキュメントでは説明しません。 詳細については、 [Azure アプリケーションの障害復旧と高可用性](https://aka.ms/drtechguide)に関するページを参照してください。

## <a name="virtual-machines"></a>仮想マシン

サービスとしてのインフラストラクチャ (IaaS) 仮想マシン (VM) の復旧は、多くの点でサービスとしてのプラットフォーム (PaaS) コンピューティングの復旧と似ています。 ただし、IaaS VM は VM と VM ディスクの両方で構成されているという事実が重要な違いです。

- **Azure Backup を使用して、アプリケーション整合性のある、複数のリージョンにまたがるバックアップを作成する**。
  [Azure Backup](https://azure.microsoft.com/services/backup/) を使用すると、お客様は複数の VM ディスクに一貫性のあるアプリケーション バックアップを作成し、リージョン間でのバックアップのレプリケーションをサポートできます。 そのためには、バックアップ コンテナーの作成時に、バックアップ コンテナーを geo レプリケートするように選択します。 バックアップ コンテナーのレプリケーションは作成時に構成する必要があります。 後で設定することはできません。 リージョンが失われた場合、Microsoft はお客様が使用可能なバックアップを作成します。 お客様は構成した復元ポイントのいずれかにも復元を行うことができます。

- **オペレーティング システム ディスクからデータ ディスクを分離する**。 IaaS VM に関する重要な考慮事項は、オペレーティング システム ディスクを変更するには VM を作成し直す必要があるということです。 これは、復旧計画が災害後に再デプロイすることになっている場合には問題ではありません。 ただし、容量を確保するためにウォーム スペア アプローチを使用する場合には問題となる可能性があります。 これを適切に実行するには、プライマリの場所とセカンダリの場所の両方に正しいオペレーティング システム ディスクをデプロイし、アプリケーション データは別のドライブに保存する必要があります。 可能であれば、両方の場所に提供できる標準のオペレーティング システム構成を使用します。 フェールオーバー後、データ ドライブをセカンダリ DC にある既存の IaaS VM にアタッチする必要があります。 AzCopy を使用して、データ ディスクのスナップショットをリモート サイトにコピーします。

- **複数の VM ディスクの geo フェールオーバー後の潜在的な一貫性の問題に注意する**。 VM ディスクは、Azure Storage BLOB として実装され、同じ geo レプリケーションの特性を備えています。 [Azure Backup](https://azure.microsoft.com/services/backup/) を使用しない場合、geo レプリケーションは非同期で個別にレプリケートするため、ディスク間の一貫性については保証されません。 個々の VM ディスクは geo フェールオーバー後にクラッシュ整合性状態になることが保証されています。ただし、ディスク間の一貫性についての保証はありません。 これは、場合によっては問題を引き起こす可能性があります (ディスク ストライピングの場合など)。

## <a name="storage"></a>Storage

### <a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>BLOB、テーブル、キュー、および VM ディスク ストレージの geo 冗長ストレージを使用した復旧

Azure の BLOB、テーブル、キュー、および VM ディスクはすべて、既定で geo レプリケーションに対応しています。 これは geo 冗長ストレージ (GRS) と呼ばれます。 GRS は、ストレージ データを特定の地理的リージョン内の数百マイル離れた場所にあるペアのデータセンターにレプリケートします。 GRS を使用すると、データセンターで大きな災害が発生したときのデータの持続性が向上します。 フェールオーバーが発生するタイミングは Microsoft によって制御されており、元のプライマリの場所が一定の時間内に復旧不可能と見なされるような大きな災害が発生した場合にのみフェールオーバーが行われます。 シナリオによっては、これには数日かかる場合があります。 データは通常数分以内にレプリケートされますが、同期の間隔はまだサービス レベル アグリーメントによってカバーされていません。

geo フェールオーバーが行われた場合、アカウントへのアクセス方法に変更はありません (URL とアカウント キーは変わりません)。 ただし、ストレージ アカウントは、フェールオーバー後は別のリージョンに存在します。 このことは、ストレージ アカウントと同じリージョンに置かれる必要があるアプリケーションに影響を与える可能性があります。 同じデータセンターにストレージ アカウントを必要としないサービスとアプリケーションについても、データセンター間の待機時間と帯域幅の料金の問題のために、トラフィックをフェールオーバー リージョンに一時的に移動させなければならないことがあります。 これはディザスター リカバリー方法全体に組み入れることができます。

GRS によって提供される自動フェールオーバーに加えて、セカンダリ ストレージ場所にあるデータのコピーを読み取ることができるサービスが Azure には導入されています。 これは読み取りアクセス geo 冗長ストレージ (RA-GRS) と呼ばれています。

GRS および RA-GRS ストレージの詳細については、「 [Azure Storage のレプリケーション](/azure/storage/storage-redundancy/)」を参照してください。

### <a name="geo-replication-region-mappings"></a>geo レプリケーションのリージョン マッピング

ストレージと同じリージョンにある必要があるデータの他のインスタンスをデプロイする場所を知るために、データが geo レプリケーションされている場所を知ることが重要です。 詳細については、「[Azure のペアになっているリージョン](/azure/best-practices-availability-paired-regions)」を参照してください。

### <a name="geo-replication-pricing"></a>geo レプリケーションの価格

geo レプリケーションは Azure Storage の現在の価格に含まれます。 これは geo 冗長ストレージ (GRS) と呼ばれます。 自分のデータに geo レプリケーションを行う必要がない場合は、自分のアカウントの geo レプリケーションを無効にできます。 これにはローカル冗長ストレージと呼ばれ、GRS と比較すると割り引きされた料金で課金されます。

### <a name="determining-if-a-geo-failover-has-occurred"></a>geo フェールオーバーが発生したかどうかの判断

geo フェールオーバーが発生した場合、これが [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)に報告されます。 ただし、アプリケーションは geo リージョンでストレージ アカウントを監視することによって、これを検出するための自動的な方法を実装できます。 これは、ストレージが移動される geo リージョンのコンピューティング リソースのアクティブ化など、他の復旧操作をトリガーするために使用できます。 [Get Storage Account Properties](https://msdn.microsoft.com/library/ee460802.aspx)を使用して、Service Management API からこれに対するクエリを実行できます。 関連するプロパティは次のとおりです。

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

### <a name="vm-disks-and-geo-failover"></a>VM ディスクと geo フェールオーバー

VM ディスクのセクションで説明したように、フェールオーバー後は VM ディスク間でデータに一貫性の保証はありません。 バックアップを正しく行うために、Data Protection Manager などのバックアップ製品を使用してアプリケーション データのバックアップと復元を行う必要があります。

## <a name="database"></a>Database

### <a name="sql-database"></a>SQL Database

Azure SQL Database では、地理リストアおよびアクティブ geo レプリケーションという 2 種類の復旧機能が提供されます。

#### <a name="geo-restore"></a>地理リストア

[geo リストア](/azure/sql-database/sql-database-recovery-using-backups/#geo-restore) も、Basic、Standard、および Premium のデータベースで利用できます。 データベースがホストされているリージョンでのインシデントのためにデータベースが利用できない場合は、既定の復旧オプションを提供します。 ポイントインタイム リストアと同様に、geo リストアも geo 冗長 Azure Storage のデータベースのバックアップに依存します。 geo レプリケーション バックアップ コピーから復元するため、プライマリ リージョンにおけるストレージの障害に対する回復力があります。 詳細については、「 [Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](/azure/sql-database/sql-database-disaster-recovery/)」を参照してください。

#### <a name="active-geo-replication"></a>アクティブ geo レプリケーションを選択するとき

[アクティブ geo レプリケーション](/azure/sql-database/sql-database-geo-replication-overview/) は、すべてのデータベース レベルで使用できます。 アクティブ geo レプリケーションは、geo リストアよりもアグレッシブな復旧要件があるアプリケーション用に設計されています。 アクティブ geo レプリケーションを使用して、別のリージョン内のサーバーで最大 4 つの読み取り可能なセカンダリを作成できます。 いずれかのセカンダリへのフェールオーバーを開始できます。 さらに、アクティブ geo レプリケーションを使用すると、アプリケーションのアップグレードや再配置のシナリオをサポートするだけでなく読み取り専用ワークロードの負荷を分散することができます。 詳細については、[geo レプリケーションの構成](/azure/sql-database/sql-database-geo-replication-portal/)に関するページと、[セカンダリ データベースへのフェールオーバー](/azure/sql-database/sql-database-geo-replication-failover-portal/)に関するページをご覧ください。 アプリケーションを設計して実装する方法と、ダウンタイムなしのアプリケーション アップグレードの詳細については、「[SQL Database のアクティブ geo レプリケーションを使用したクラウド ディザスター リカバリー用アプリケーションの設計](/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery/)」と「[SQL Database アクティブ geo レプリケーションを使用したクラウド アプリケーションのローリング アップグレードの管理](/azure/sql-database/sql-database-manage-application-rolling-upgrade/)」をご覧ください。

### <a name="sql-server-on-virtual-machines"></a>Virtual Machines 上の SQL Server

SQL Server 2012 (以降) を Azure Virtual Machines で実行した場合、復旧および高可用性のためのさまざまなオプションを利用できます。 詳細については、「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)」を参照してください。

## <a name="other-azure-platform-services"></a>その他の Azure プラットフォーム サービス

複数の Azure リージョンでクラウド サービスを実行しようとする場合、依存関係ごとに影響について検討する必要があります。 以下のセクションにおいて、サービスに固有のガイダンスでは代替の Azure データセンターにおいては同じ Azure サービスを使用する必要があると想定されています。 これには、構成タスクとデータ レプリケーション タスクの両方があります。

> [!NOTE]
> 場合によっては、これらの手順はデータセンター イベント全体ではなく、サービス固有の障害を軽減するために役立つ可能性があります。 アプリケーションの観点からは、サービス固有の障害は単に制限的なもので、サービスを代替の Azure リージョンへ一時的に移行する必要があります。

### <a name="service-bus"></a>Service Bus

Azure Service Bus は、Azure リージョンにまたがらない一意の名前空間名を使用します。 したがって、最初の要件は代替リージョンで必要なサービス バス名前空間を設定することです。 ただし、キューに置かれたメッセージの持続性についても考慮事項があります。 Azure リージョン間でメッセージをレプリケートするにはいくつかの方法があります。 これらのレプリケーション方法とその他のディザスター リカバリー計画の詳細については、「[Service Bus の障害および災害に対するアプリケーションの保護のベスト プラクティス](/azure/service-bus-messaging/service-bus-outages-disasters/)」をご覧ください。 その他の可用性の考慮事項については、 [Service Bus (可用性)](recovery-local-failures.md#other-azure-platform-services)に関するページを参照してください。

### <a name="app-service"></a>App Service

Azure App Service アプリケーション (Web Apps や Mobile Apps など) をセカンダリ Azure リージョンに移行するには、発行に使用できる Web サイトのバックアップが必要です。 障害が Azure データセンター全体を含んでいなければ、FTP を使用してサイト コンテンツの最新のバックアップをダウンロードできる場合があります。 次に、容量を確保するために代替リージョンにまだ新しいアプリを作成していない場合は、それを作成します。 サイトを新しいリージョンに発行し、必要な構成の変更を行います。 これらの変更には、データベース接続文字列またはその他のリージョン固有の設定が含まれる場合があります。 必要に応じて、サイトの SSL 証明書を追加し、DNS の CNAME レコードを変更して、カスタム ドメイン名が再デプロイされた Azure の Web サイトの URL を参照するようにします。

### <a name="hdinsight"></a>HDInsight

HDInsight に関連付けられているデータは、既定では Azure BLOB ストレージに保存されます。 HDInsight では、MapReduce ジョブを処理する Hadoop クラスターは、分析されるデータを保持したストレージ アカウントと同じリージョンに併置する必要があります。 Azure Storage で利用可能な geo レプリケーション機能を使用すると、何らかの理由によりプライマリ リージョンが使用できなくなった場合、データがレプリケートされたセカンダリ リージョンのデータにアクセスできます。 データがレプリケートされているリージョンに新しい Hadoop クラスターを作成し、データの処理を継続することができます。 その他の可用性の考慮事項については、 [HDInsight (可用性)](recovery-local-failures.md#other-azure-platform-services)に関するページを参照してください。

### <a name="sql-reporting"></a>SQL Reporting

現時点では、Azure リージョンの損失からの復旧には、異なる Azure リージョンにそれぞれ複数の SQL Reporting インスタンスが必要です。 これらの SQL Reporting インスタンスは、同じデータにアクセスする必要があり、そのデータには災害が発生した場合の独自の復旧計画が必要です。 また、各レポートに RDL ファイルの外部バックアップ コピーを保持できます。

### <a name="media-services"></a>Media Services

Azure Media Services には、エンコードとストリーミングのために異なる復旧方法があります。 通常、ストリーミングは地域的な障害が発生している場合にはより重要になります。 これに備えて、2 つの異なる Azure リージョンに Media Services アカウントを用意する必要があります。 エンコードされた内容は、両方のリージョンに配置される必要があります。 障害時には、ストリーミング トラフィックを代替リージョンにリダイレクトできます。 エンコードは任意の Azure リージョンで実行できます。 ライブ イベントの処理中など、エンコードが時間の影響を受ける場合、ジョブを障害中に代替のデータセンターへ送信する準備をしておく必要があります。

### <a name="virtual-network"></a>仮想ネットワーク

構成ファイルを使用すると、代替の Azure リージョンで仮想ネットワークを最もすばやくセットアップできます。 プライマリ Azure リージョンで仮想ネットワークを構成した後、ネットワーク構成ファイルに現在のネットワークの [仮想ネットワーク設定をエクスポート](/azure/virtual-network/virtual-networks-create-vnet-classic-portal/) します。 プライマリ リージョンで障害が発生した場合は、保存された構成ファイルから [仮想ネットワーク構成ファイルを復元](/azure/virtual-network/virtual-networks-create-vnet-classic-portal/) します。 次に、その他のクラウド サービス、仮想マシン、またはクロスプレミス設定を構成して、新しい仮想ネットワークで作業します。

## <a name="checklists-for-disaster-recovery"></a>障害復旧のためのチェックリスト

## <a name="cloud-services-checklist"></a>Cloud Services のチェックリスト

1. このドキュメントの「Cloud Services」セクションを確認する。
2. 複数のリージョンにわたるディザスター リカバリー方法を作成する。
3. 代替リージョンにおける容量の確保においてのトレードオフを理解する。
4. Azure Traffic Manager などのトラフィック ルーティング ツールを使用する。

## <a name="virtual-machines-checklist"></a>Virtual Machines のチェックリスト

1. このドキュメントの「Virtual Machines」セクションを確認する。
2. [Azure Backup](https://azure.microsoft.com/services/backup/) を使用して、リージョン間でアプリケーション整合性のあるバックアップを作成する。

## <a name="storage-checklist"></a>Storage のチェックリスト

1. このドキュメントの「Storage」セクションを確認する。
2. ストレージ リソースの geo レプリケーションを無効にしない。
3. フェールオーバーが発生した場合の geo レプリケーションの代替リージョンを理解する。
4. ユーザー制御のフェールオーバー方法に有効なカスタム バックアップ方法を作成する。

## <a name="sql-database-checklist"></a>SQL Database のチェックリスト

1. このドキュメントの「SQL Database」セクションを確認する。
2. 必要に応じて [geo リストア](/azure/sql-database/sql-database-recovery-using-backups/#geo-restore)または [geo レプリケーション](/azure/sql-database/sql-database-geo-replication-overview/)を使用する。

## <a name="sql-server-on-virtual-machines-checklist"></a>Virtual Machines での SQL Server のチェックリスト

1. このドキュメントの「Virtual Machines 上の SQL Server」セクションを確認する。
2. 複数のリージョンにわたる AlwaysOn 可用性グループまたはデータベース ミラーリングを使用する。
3. または、バックアップを使用して、Blob Storage に復元します。

## <a name="service-bus-checklist"></a>Service Bus のチェックリスト

1. このドキュメントの「Service Bus」セクションを確認する。
2. 代替リージョンで Service Bus 名前空間を構成する。
3. リージョン間でのメッセージのカスタム レプリケーション方法を検討する。

## <a name="app-service-checklist"></a>App Service のチェックリスト

1. このドキュメントの「App Service」セクションを確認する。
2. プライマリ リージョンの外でサイトのバックアップを維持する。
3. 障害が部分的な場合は、FTP を使用して現在のサイトの取得を試みる。
4. 代替リージョンの新規または既存のサイトにサイトをデプロイする計画を作成する。
5. アプリケーションと DNS の CNAME レコードの両方の構成変更を計画する。

## <a name="hdinsight-checklist"></a>HDInsight のチェックリスト

1. このドキュメントの「HDInsight」セクションを確認する。
2. レプリケートされたデータを使用して、リージョンに新しい Hadoop クラスターを作成する。

## <a name="sql-reporting-checklist"></a>SQL Reporting のチェックリスト

1. このドキュメントの「SQL Reporting」セクションを確認する。
2. SQL Reporting の代替インスタンスを別のリージョンで保持する。
3. そのリージョンに対象をレプリケートする別の計画を維持する。

## <a name="media-services-checklist"></a>Media Services のチェックリスト

1. このドキュメントの「Media Services」セクションを確認する。
2. 代替リージョンで Media Services アカウントを作成する。
3. 両方のリージョンで同じ内容をエンコードして、ストリーム フェールオーバーをサポートする。
4. サービスの中断が発生した場合は、エンコード ジョブを代替リージョンに送信する。

## <a name="virtual-network-checklist"></a>Virtual Network のチェックリスト

1. このドキュメントの「Virtual Network」セクションを確認する。
2. エクスポートされた仮想ネットワーク設定を使用して、別のリージョンでテーブルを再作成する。
