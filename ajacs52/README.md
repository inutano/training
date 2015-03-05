# データ解析プラットフォーム Galaxyの使い方

情報・システム研究機構  
ライフサイエンス統合データベースセンター  

Research Organization of Information and Systems (ROIS),  
Database Center for Life Science (DBCLS)  

大田達郎  
Tazro Ohta  
t.ohta@dbcls.rois.ac.jp

Prepared for AJACS府中  
March 5, 2015

----

これは統合データベース講習会 AJACS府中「データ解析プラットフォーム Galaxyの使い方」の資料です。  
お知らせとプログラム、各講習の資料へのリンクは[こちら](http://motdb.dbcls.jp/?AJACS52)です。

----

## 概要

本講習では、ウェブブラウザを通して利用するフリーのデータ解析プラットフォームであるGalaxyの利用方法について学びます。[Galaxy Project](http://galaxyproject.org)の概要を紹介したのち、NGSデータの解析を例として、Galaxyの実習を行います。実習では、日本国内におけるGalaxyユーザによる[Pitagora Galaxy Project](http://www.pitagora-galaxy.org)で開発されたマシンイメージを利用します。

## 講習の流れ

今回の講習では、コンピュータを使って以下の内容について説明します。

- Galaxyについて
  - The Galaxy Project
  - Public Servers
- Pitagora-Galaxy Projectについて
  - Pitagora Network
  - Galaxy Workshop Tokyo 2015のお知らせ
- GalaxyでNGSデータ解析
  - NGSデータ解析について
	  - データ処理の種類
	  - データ解析の種類
  - RNA-Seqデータ解析 (実習)
    - データフォーマットについて
    - Galaxy Workflowの使い方

## Galaxyについて

生命科学分野におけるデータ解析では、複数のツールを組み合わせたデータ解析ワークフローを構築し、異なるサンプルに対して繰り返し処理をするという作業が発生します。Galaxyは、そのようなデータ解析における作業をウェブブラウザを通して実行するためのデータ解析プラットフォームです。

![honke](http://gyazo.com/9fde98c4f2f9f271aeddc6697fc30b81.png)

### The Galaxy Project

GalaxyはPenn Stateのメンバーを中心としたGalaxyチームによって開発されています。プロジェクトのトップページのURLは [galaxyproject.org](http://galaxyproject.org) です。

![galaxyproject.org](http://gyazo.com/b1127469dc5bbf730858af2d2cc5cda8.png)

Galaxyは誰でも無料で利用できるデータ解析プラットフォームです。Galaxyを利用することによって、ワークフローの構築と実行だけでなく、解析の履歴管理、ワークフローや解析結果の共有、そして過去のデータ解析の再実行などを行うことができます。Galaxyには2つの利用方法があります;

#### 1. Public Serverを利用する

世界中の様々な機関によって、Galaxyをインストールしたウェブサーバが公開されています。それぞれのPublic Serverでアカウントを申請することで、そのサーバ上でデータ解析を行うことができます。ただし、インストールされているツールが機関によって異なったり、ユーザごとに保存できるデータサイズに上限が定められていることがあります。国内ではDBCLS, DDBJ, Pitagora-Galaxy Projectによって公開サーバが運用されています。

- [Publicly Accessible Galaxy Servers](https://wiki.galaxyproject.org/PublicGalaxyServers)
  - Galaxy Projectに認知されているPublic Serverのリスト
- [usegalaxy.org](https://usegalaxy.org)
  - Galaxy ProjectによってホストされているPublic Server
- [DBCLS Galaxy](http://galaxy.dbcls.jp)
  - DBCLSによって運用されているPublic Server
- [DDBJ Galaxy](http://p-galaxy.ddbj.nig.ac.jp)
  - DDBJによって運用されているPublic Server
- [Pitagora Galaxy Test Server](http://try.pitagora-galaxy.org/galaxy/)
  - Pitagora Galaxy ProjectによるマシンイメージをテストするためのPublic Server


#### 2. 自分の環境にGalaxyをインストールして利用する

もう1つは、専用のGalaxyサーバを用意することです。一般的なLinuxやMac OSが搭載されたコンピュータであれば、インストールはそれほど難しくありません。インターネットにアップロードできないデータを解析したい場合や、自身で必要なツールをインストールしたい場合は、自分専用のGalaxy環境をセットアップしましょう。

- [Galaxy Download and Install](https://wiki.galaxyproject.org/Admin/GetGalaxy)
  - とても簡単にインストールができます。
- [Galaxy Docker Image](https://github.com/bgruening/docker-galaxy-stable)
  - Dockerがインストールされている方は `docker run -d -p 8080:80 -p 8021:21 bgruening/galaxy-stable` で http://localhost:8080 に起動します。
  - Dockerが何かわからない方は[こちら](https://speakerdeck.com/inutano/bh14-dot-14-team-docker-wrap-up)をご覧頂くか、Google先生に訊いてみてください。

自身でセットアップと運用管理をする自信がないという場合は、次に紹介するPitagora Galaxy Projectのマシンイメージを利用してみてください。

## Pitagora-Galaxy Projectについて

[Pitagora-Galaxy Project](http://www.pitagora-galaxy.org)は、日本国内のGalaxyユーザが集まるPitagora-Networkが主体となって進めるプロジェクトです。このプロジェクトでは、各ラボで使っているツールやワークフローを持ち寄って、それらが初めからインストールされているGalaxyを配布しています。また一ヶ月に1〜2回、Galaxy Community Meetupを開催して、Galaxyの運用や開発、データ解析についての情報交換を行っています。

配布しているGalaxy環境は、Virtual Machine (VM) のマシンイメージとして開発されています。VMとは、コンピュータのOSの上に仮想的に別のOSを立ち上げて、その上でアプリケーションを動かす仮想環境のことです。利用するためには、仮想環境が起動できるクラウド環境か、仮想環境を起動するためのソフトウェア ([VirtualBox](http://virtualbox.org/)など) をインストールすることが必要です。

Pitagora-Galaxyの使い方は3つあります;

1. テストサイトを利用する
  - テスト用にPublic Serverが稼働しています
2. VMをダウンロードして手元のコンピュータで動かす
  - 最も一般的な利用方法です
3. Amazon Web Service (AWS) で公開されているAMI (Amazon Machine Image) を使う
  - AWSの利用料はかかりますが、クラウド上に構築した計算機の上でGalaxyを起動できます

トップページには、それぞれの使い方の解説のページへのリンクがあります。 http://www.pitagora-galaxy.org にアクセスして、リンクをクリックして内容を見てみましょう。

![pitagora-top](http://gyazo.com/c25b0f2c8673bf351e05d83f9d91eaf8.png)

### Pitagora-Network

ピタゴラギャラクシープロジェクトは、有志によるオープンソースプロジェクトとして発足しており、特定の機関や組織に依存しているわけではありません。国内のGalaxyユーザが情報交換を行う場として、様々な大学、研究機関から色々な立場の方が参加されています。興味のある方は、是非一度ご連絡ください。ピタゴラネットワークの活動については、[pitagora wiki](http://wiki.pitagora-galaxy.org/wiki/index.php/Main_Page)をご覧ください。

![pitagora-wiki](http://gyazo.com/932be06667404f14285173e4669d11fe.png)

### Galaxy Workshop Tokyo 2015

## GalaxyでNGSデータ解析

### NGSデータ解析について

NGSのデータから生物学的な知見を見出すために必要な作業は、シーケンサから出力された生の配列断片を変換するデータ処理のステップと、その後に続く多型解析や遺伝子発現の定量などを行う解析のステップに分かれます。

#### データ処理の種類

新型シーケンサから得られる一次データ（シークエンスリードデータ）は、そのままでは何の情報も持たないため、情報解析を行うことができません。そのため、データ解析を行う前にデータ処理を行う必要があります。代表的なものは以下の2つです。

- マッピング
    - ゲノム塩基配列やCDSなどを参照配列（リファレンス）として、それぞれのリードがどの領域から得られたかを調べる
- アセンブル
    - 参照配列がない場合などに、リード同士の端の部分の相同性を元にリードを繋ぎあわせコンティグ配列を得る

どちらの処理も、リードの長さや数、リファレンス配列の大きさ、計算機の性能や並列化などに応じて計算時間が変わります。

##### DDBJパイプラインの使い方

ゲノムサイズやシーケンスのスループットによっては，データ処理に必要なリソースが手元のコンピュータでは賄えないことがあります。そのような場合にはDDBJが提供している「[DDBJ Read Annotation Pipeline](http://p.ddbj.nig.ac.jp/)」を通じて遺伝研スパコンを利用することができます。使い方については以下の資料を参考にしてください。

- [Pipeline Tutorial](http://www.ddbj.nig.ac.jp/search/help/pipeline-tutorial-j.html)
- [統合TV](http://togotv.dbcls.jp/ja/)
    - [今日からはじめるDDBJ Read Annotation Pipeline](http://togotv.dbcls.jp/20100617.html)
    - [DDBJ Read Annotation Pipelineによるde novo Assembly解析](http://togotv.dbcls.jp/20110226.html#p01)
- [DDBJパイプラインとGalaxyによるデータ解析](https://github.com/inutano/training/tree/master/ajacs51)

#### データ解析の種類

新型シーケンサから得られたデータは、マッピング、アセンブルなどのデータ処理を行ったあと、それぞれの目的に応じた解析を行うことで、サンプルの持つ特徴的なゲノム領域をリストアップしたり、サンプル間の差を比べたりすることができます。新型シーケンサを用いた実験は、どのようなシーケンサを使うか（リードの数や長さ）、どのようにサンプルDNAを得るかによって区別することができます。サンプルDNAの抽出方法を工夫することで様々な情報をハイスループットに得られることが新型シーケンサが強力なツールとして用いられる理由ですが、そのシーケンシングの種類は非常に多岐にわたります([*-Seq](http://applications.illumina.com/applications/sequencing/ngs-library-prep/library-prep-methods.html#.VIEmdL6Hq2w))。それぞれの目的に応じた解析の手順と、それに用いられる代表的なツールは、既存研究の論文やオンラインフォーラムで調べることができます。

![*-Seq](http://gyazo.com/9fa560a57003974dbd21ab0b74f48654.png)


### データ解析の種類

それぞれのシーケンシングにおける解析については、[次世代シークエンス解析スタンダード〜NGSのポテンシャルを活かしきるWET&DRY](http://www.amazon.co.jp/dp/4758101914/ref=cm_sw_r_tw_dp_40sGub0Q8C567)という書籍にもまとめられています。各シーケンスのライブラリ調製から丁寧に解説してあるため、非常に参考になります。困った時は人に聞いたり調べたりする前に、この本を読めばだいたい解決します。ラボに1冊では足りません。1人1冊持っておいて損はありませんし、もう1冊買って、先輩や後輩の机にそっと置いておきましょう。とてもおすすめです。

![dritoshi-book](http://gyazo.com/783282872dfc90d3b03226a7da1f1596.png)

NGSに興味のある方はもちろん、今のところNGSをやる予定のない人も今すぐ買って読んでみてください。

## RNA-Seqデータ解析 (実習)

### データフォーマットについて

今回の講習では新型DNAシーケンサーから得られる塩基配列データを取り扱います。塩基配列データは様々な形式でファイルに保存されます。今回のデータ解析に関連するデータ形式(フォーマット)は以下のようなものです。

-	FASTQ
- FASTA
- SAM

その他の主要なフォーマットについては、[こちら](http://genome.ucsc.edu/FAQ/FAQformat.html)をご参照ください。

いずれもファイル形式はプレーンテキストです。しかし、ファイルが大きすぎるとアプリケーションがクラッシュしてしまうので、メモ帳やMicrosoft Office Excelのようなアプリケーションで開くことはお勧めしません。

#### FASTQ

新型シーケンサから得られる塩基配列断片は「リード」と呼ばれます。1回のシーケンス(1Run)からは機械によって数百〜数十億本のシーケンスリードが得られます。FASTQフォーマットは、1本1本のリードの塩基配列と1塩基ごとのクオリティを記述します。FASTQフォーマットは、4行で1リードの情報を記述します。

```
@SRR975299.998 HWI-ST1347:124:H0G13ADXX:1:1101:7299:3013 length=101
ACTGTTAGAAACAATATTCCTTCTGAAGTACCATGATAGAAATTACTATTAAAGCACTATCCTCTACCCCTTACCGTGCATGGTTCTGATGCATTCAAAGC
+SRR975299.998 HWI-ST1347:124:H0G13ADXX:1:1101:7299:3013 length=101
==?D=BDDFFDADGH?EHEHHHBFABHF:<FHAHGAGCGHIHHHGGIIICHFHHGEHIIIGGIGEHHCHGCFFD@E;DECEEA?BCB@>A;AACDCC@CCC
@SRR975299.999 HWI-ST1347:124:H0G13ADXX:1:1101:7473:3197 length=101
TCTTCACTGGCTTTCCATGATCAATGTCAAGAATCAGACTGAGAGCTTGGGAACATTCTTTTGCTCTTTGCCGTACCTAATATAATTATTAGAAGAAGGAA
+SRR975299.999 HWI-ST1347:124:H0G13ADXX:1:1101:7473:3197 length=101
CCCFFFFFHHHHHJJJJJHJJJJJJJFIHIHJIIJJJHJJJJIIIJHIJJIJJJJJJJJJJJJJJJJJJJJIJIHHHFFFFFFFEEFEEFFDDDDDDDDDD
@SRR975299.1000 HWI-ST1347:124:H0G13ADXX:1:1101:7557:3113 length=101
CCATGATTCTGTCCAGCCAGCAGGATGCAGCCTTTGCCTTTGCCTCTCTTGCCATAGTTTTCTCCTCCTATATCACTCTTGTTGTGCTCTTTGTGCCCAAG
+SRR975299.1000 HWI-ST1347:124:H0G13ADXX:1:1101:7557:3113 length=101
CCCFFFFFHHHHHIJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJIJJJJJJJJJHIJJJJIJJJJIJJJJJHJHHHHHHFFFFEEEEEEEDDDDDDBD
```

|||
|:--:|:--:|:--:|
|1行目|リードID|
|2行目|塩基配列|
|3行目|+から始まるコメント|
|4行目|クオリティスコア|

[参考: FASTQ format / Wikipedia](http://en.wikipedia.org/wiki/FASTQ_format)


##### Phred Quality Score

FASTQフォーマットで記述される塩基配列のクオリティはPhred Quality Scoreを示す記号で表されます。リードの1塩基ごとに、「その塩基を読み取った(BaseCall)精度」を記述します。

|Phred Quality Score|エラー確率|Base Callの精度|
|:-----------------:|:------:|:------------:|
|10|1/10|90%|
|20|1/100|99%|
|30|1/1000|99.9%|
|40|1/10000|99.99%|

[参考: Phred quality score / Wikipedia](http://en.wikipedia.org/wiki/Phred_quality_score)


#### FASTA

FASTAはDNA塩基配列を記述するために最もシンプルで一般的なフォーマットです。塩基配列だけでなくアミノ酸配列もこのフォーマットで記述されます。BLASTなどのtradな塩基配列解析においてもよく用いられます。2行で1本の塩基配列を記述します。1行目は">"から始まるコメント、2行目は塩基配列です。

```
>gi|129295|sp|P01013|OVAX_CHICK GENE X PROTEIN (OVALBUMIN-RELATED)
QIKDLLVSSSTDLDTTLVLVNAIYFKGMWKTAFNAEDTREMPFHVTKQESKPVQMMCMNNSFNVATLPAE
KMKILELPFASGDLSMLVLLPDEVSDLERIEKTINFEKLTEWTNPNTMEKRRVKVYLPQMKIEEKYNLTS
VLMALGMTDLFIPSANLTGISSAESLKISQAVHGAFMELSEDGIEMAGSTGVIEDIKHSPESEQFRADHP
FLFLIKHNPTNTIVYFGRYWSP
```

[参考: FASTA format description](http://genetics.bwh.harvard.edu/pph/FASTA.html)


#### SAM/BAM

新型シーケンサのデータ解析では、得られたリードがゲノムDNAのどの領域に由来するのかを調べるために「マッピング」という操作を行います。これは「リファレンス・マッピング」とも呼ばれます。リファレンス、つまり既に読まれたゲノム配列と、リードのデータをマッピング・ツールに与えると、どのリードがどの領域にアラインメントされたかを示すデータが得られます。SAM(Sequence Alignment/Map format)フォーマットは、このデータを記述するためのもので、BAMフォーマットは、SAMをバイナリ形式に変換したものです。

[参考: Hts-specs by samtools](http://samtools.github.io/hts-specs/)


### Galaxy Workflowの使い方

今回の講習ではピタゴラギャラクシープロジェクトのテストサイト[try.pitagora-galaxy.org](http://try.pitagora-galaxy.org:8080/)を利用します。

![try-pitagora](http://gyazo.com/177405173fae108a778677259f76d916.png)

#### Galaxyの基本的な使い方

Galaxyの画面は常に左サイドバー、中央のブラウザ、右サイドバーに三分割されています。左サイドバーは利用できるツールのリストです。中央のブラウザは選択中のツールをコントロールしたり結果を見るために使います。右サイドバーでは、ツールを実行するごとにプロセスがスタックして、履歴として機能します。履歴表示だけでなく、それぞれのプロセスの情報を見たり、再実行したりできます。

![galaxy-ss](http://gyazo.com/2e7c840ca43c4aacfec29c1cfd7809d6.png)

#### アカウントの作成

(今回の講習ではtryアカウントでログインしますので、このステップは飛ばします)

自らGalaxyをセットアップした場合や他のPublic Serverを使う際には、ユーザアカウントを作成する必要があります。右上の「User」をクリックして、「Register」をクリックします。Emailアドレス、パスワード、名前を入力してから「Submit」をクリックします。しばらく待つとメールが届くので、メールの中のアクティベーションのURLをクリックして、confirmしたあと、作成したアカウントでログインします。

![create-account](http://gyazo.com/0a94312f2302baff73fc133f5db3170b.png)

#### データのインポート

このページの上にある「demo.fastq」というデータをクリックして、「RAW」ボタンを右クリックしてリンク先のファイルをダウンロードします。Galaxyの左パネルの「Get Data」をクリックして、開いたリストの一番上にある「Upload File」をクリックするとダイアログが開くので、「Choose local file」をクリックしてダウンロードしたdemo.fastqファイルを選択します。リストの「Genome」をクリックして、「Human Feb. 2009 (hg19)」を選択したら、「Execute」をクリックします。しばらく待つと完了するので、「Close」をクリックします。

![import-data](http://gyazo.com/b65182b9ab0e08990abed8408fc726c9.png)


目玉ボタンをクリックしてみましょう。インポートが完了したデータの様子です。

![imported](http://gyazo.com/e4ccd26ba42ae96206c75f5a31a59a48.png)



### FastQCを実行する

インポートしたFASTQデータのクオリティを確認します。今回は、最もポピュラーな新型シーケンサデータ用クオリティ・コントロールのツールである[FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)を利用します。左パネルの検索ボックスに「fastqc」と入力して出る「FastQC: Read QC」をクリックします。「Short read data from your current history」でdemo.fastqを選んでから「Execute」をクリックすると、プロセスが右パネルの履歴に追加され、しばらくすると実行されます。

![fastqc](http://gyazo.com/3285d55b88827aba4d2949eafbc0de54.png)



実行が完了したら、目玉ボタンをクリックします。FastQCの結果を見ることができます。

![fastqc-done](http://gyazo.com/be7b677e1d0e9be85fc917003386b269.png)



### 履歴やワークフローの共有

ツールを選択して実行すると、その実行結果が右パネルにスタックしていきます。初期設定では「Unnamed history」となっていますが、これに名前をつけたり、履歴を他のユーザと共有したり、履歴において実行したツールの組み合わせからワークフローを構築することができます。右パネルの右上にある歯車のアイコンをクリックして、履歴に関するメニューを表示します。

![history_management](http://gyazo.com/7df8a08beaabf82285e9ac94fb5d1c25.png)



### 新規ワークフローの作成

FastQCを実行した履歴からワークフローを作成してみましょう。右パネルの歯車アイコンをクリックして、メニューから「Extract Workflow」を選択します。判別しやすい名前をつけてから「Create workflow」をクリックします。

![create_workflow](http://gyazo.com/6f7a03b57d272f6ffcb4a93eba1baf42.png)



### 作成したワークフローの実行

画面上部の「Workflow」をクリックすると利用できるワークフローの一覧が表示されます。先ほど作成したワークフローが表示されていますので、名前をクリックして「Run」をクリックすると、ワークフローが読み込まれ、別のデータで同じ処理を繰り返すことができます。

![run_workflow](http://gyazo.com/05f66a9bae7ef204536a5f73aca97fb8.png)



### ワークフローのインポート

Galaxyではデータや履歴、ワークフローを保存したり、ユーザ間で共有するだけでなく、Galaxyサーバを通じて公開することができます。ここでは、ピタゴラネットワークによって共有されているワークフローを利用してデータ解析を行ってみます。まず、画面上部の「Shared Data」をクリックして「Published Workflow」をクリックします。

![published-workflow](http://gyazo.com/69fc81d0c584149fdba99cfaaef7f7bf.png)



### RNA-Seq Workflow

「RNA-seq 01」をクリックします。ワークフローの概要が表示されるので、右上のフロッピーディスクアイコンの隣にある緑色の+ボタンをクリックします。この操作は、ログインしていないとできないため、失敗した場合はログインできているかを画面上部の「User」から確認してください。

![workflow-detail](http://gyazo.com/862b08a964e0ecf298c1a87d151f0283.png)



完了しました。「start using this workflow」をクリックします。

![done](http://gyazo.com/9b3eafc9db8c9f2cc19a723a61ae5f21.png)



### ワークフローを編集する

表示されたページで再び「RNA-seq 01」をクリックして「View」をクリックすると、詳細を見ることができます。このワークフローでは、FASTQデータを入力として、TopHat2によるマッピングを行ったのち、cufflinks, cuffdiffによって発現量の異なるものを検出します。このワークフローがどのような処理を行うのかをダイアグラムで確認してみます。

画面上部の「Workflow」をクリックして再度ワークフローのページを表示します。「Imported: RNA-seq 01」をクリックして、「Edit」をクリックします。

![workflow](http://gyazo.com/6f4552370aee627688a88d2cc240703c.png)


ワークフロー・エディターが表示されます。ここでは、ワークフローの編集をすることができます。例えば、もう既にFastQCは実行したので必要ない、という場合にはこのワークフローからFastQCを消したりすることができます。

![workflow-editor](http://gyazo.com/eb482477d3f33dd603747c0f5e57c1b9.png)

### サンプルデータを入手する

ワークフローの詳細は、ピタゴラギャラクシーのウェブサイトにも詳しく掲載されています。 http://wiki.pitagora-galaxy.org/wiki/index.php/Workflow_RNA-seq_01 にアクセスして、どのような処理を行うかを確認します。

![rna-seq-wf](http://gyazo.com/79fa3657762ac3b5f9d7347c09eca503.png)

また、テストデータが用意されています。ページに書いてある通り、GalaxyはURLからファイルをダウンロードすることもできます。これに従ってファイルをアップロードしてみましょう。

![test-data](http://gyazo.com/bddb861e3d4ab13a25039d8ce1b769a0.png)


アップロードが完了しました。

![uploaded](http://gyazo.com/6f274561c4aa15f6bf1652b51fe291a9.png)

### ワークフローを実行する

先ほどと同様に画面上部の「Workflow」から「RNA-seq 01」を選択して「Run」をクリックすると、ワークフローが読み込まれ入力ファイルを選択する状態になります。アップロードしたファイルを入力ファイルとして選択します。

![start-running-workflow](http://gyazo.com/232dabebf17413463eebb8e4b29ac306.png)

ワークフローに含まれるツールの中には実行時にパラメータを要求するものがありますが、今回は特に指定せずに実行します。確認したら、中央パネルの一番下の「Run Workflow」をクリックします。

![select_parameters](http://gyazo.com/7b7b7a1e70a19d70365b0de5f595d6dc.png)

ワークフローが実行されました。あとは待つだけです。

![running](http://gyazo.com/9481c484d29f289bfb79ca96ce0737c9.png)


完了したら、データを確認してみましょう！

![confirmation](http://gyazo.com/4e7a508e895e542c5356d747887f82c9.png)

## お知らせ&宣伝

### NGS現場の会

[NGS現場の会](http://www.ngs-field.org)は新型シーケンサを利用した研究に関わる人が集まる研究会です。アカデミア、企業、研究者、テクニシャン、学生などなどの垣根のいっさいない情報交換のための会を目指しています。データ解析で困ったときに助けてくれる人もいます。第四回研究会が2015年7月1日〜3日につくば国際会議場にて行われますので、ご興味のある方は是非ご参加ください。もうまもなく参加登録が始まる予定ですので、詳しい情報は[メーリングリスト](https://groups.google.com/forum/#!forum/ngs-field)に登録頂いてご確認ください。

![ngs-genbanokai](http://gyazo.com/436dfb67f4876e63e691b3367806248e.png)

### お花見メタゲノムプロジェクト

NGS現場の会第四回研究会では、「全国のソメイヨシノの細菌叢サンプルをみんなで集めてシーケンスする」プロジェクトを行います。現在、サンプリング協力者を募集中ですので、特に遠方にお花見に行かれる方は是非ご協力ください！

![omg-seq](http://gyazo.com/70b21e49c0981c894aa74a9b34673970.png)

### Galaxy Workshop Tokyo 2015

本日紹介したPitagora-Galaxyの開発とコミュニティ運用を行っているPitagora-Networkが主体となってGalaxyのワークショップを開催します。NGS解析を行う全ての人が対象となっています。是非ご参加ください！詳しくはこちら http://wiki.pitagora-galaxy.org/wiki/index.php/Galaxy_Workshop_Tokyo_2015

![galaxyworkshoptokyo](http://gyazo.com/fce911fdea831066bd0c984f1168ab44.png)

## 以上で終了です！

おつかれさまでした！
