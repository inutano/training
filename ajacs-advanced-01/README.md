# GalaxyによるNGS解析

情報・システム研究機構  
ライフサイエンス統合データベースセンター  

Research Organization of Information and Systems (ROIS),  
Database Center for Life Science (DBCLS)  

大田達郎  
Tazro Ohta  
t.ohta@dbcls.rois.ac.jp

Prepared for AJACSa三島  
January 27, 2015

----

これは統合データベース講習会 AJACSa三島「GalaxyによるNGSデータ解析」の資料です。  
お知らせとプログラム、各講習の資料へのリンクは[こちら](http://dbcls.rois.ac.jp/archives/2715)です。

----

## 概要

本講習は、主に新型DNAシーケンサー(High-Throughput Sequencer, HTS)から得られる塩基配列データを対象に、データ解析に必要な基本的な知識と、オープンソースで公開されているデータ解析システムGalaxyを利用した解析の手順を学びます。

----

## 講習の流れ

今回の講習では、コンピュータを使って以下の内容について説明します。

-	データについて
-	データ処理について
	-	データ処理の種類
		-	マッピング
		-	アセンブル
	-	DDBJパイプラインの使い方
-	データ解析について
	-	データ解析の種類
		-	ゲノム多型解析
	-	Galaxyの使い方

----

## データについて

今回の講習では新型DNAシーケンサーから得られる塩基配列データを取り扱います。塩基配列データは様々な形式でファイルに保存されます。今回のデータ解析に関連するデータ形式(フォーマット)は以下のようなものです。

-	FASTQ
- FASTA
- SAM
- VCF

その他の主要なフォーマットについては、[こちら](http://genome.ucsc.edu/FAQ/FAQformat.html)をご参照ください。

いずれもファイル形式はプレーンテキストです。しかし、ファイルが大きすぎるとアプリケーションがクラッシュしてしまうので、メモ帳やMicrosoft Office Excelのようなアプリケーションで開くことはお勧めしません。

----

### FASTQ

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

----

#### Phred Quality Score

FASTQフォーマットで記述される塩基配列のクオリティはPhred Quality Scoreを示す記号で表されます。リードの1塩基ごとに、「その塩基を読み取った(BaseCall)精度」を記述します。

|Phred Quality Score|エラー確率|Base Callの精度|
|:-----------------:|:------:|:------------:|
|10|1/10|90%|
|20|1/100|99%|
|30|1/1000|99.9%|
|40|1/10000|99.99%|

[参考: Phred quality score / Wikipedia](http://en.wikipedia.org/wiki/Phred_quality_score)

----

### FASTA

FASTAはDNA塩基配列を記述するために最もシンプルで一般的なフォーマットです。塩基配列だけでなくアミノ酸配列もこのフォーマットで記述されます。BLASTなどのtradな塩基配列解析においてもよく用いられます。2行で1本の塩基配列を記述します。1行目は">"から始まるコメント、2行目は塩基配列です。

```
>gi|129295|sp|P01013|OVAX_CHICK GENE X PROTEIN (OVALBUMIN-RELATED)
QIKDLLVSSSTDLDTTLVLVNAIYFKGMWKTAFNAEDTREMPFHVTKQESKPVQMMCMNNSFNVATLPAE
KMKILELPFASGDLSMLVLLPDEVSDLERIEKTINFEKLTEWTNPNTMEKRRVKVYLPQMKIEEKYNLTS
VLMALGMTDLFIPSANLTGISSAESLKISQAVHGAFMELSEDGIEMAGSTGVIEDIKHSPESEQFRADHP
FLFLIKHNPTNTIVYFGRYWSP
```

[参考: FASTA format description](http://genetics.bwh.harvard.edu/pph/FASTA.html)

----

### SAM/BAM

新型シーケンサのデータ解析では、得られたリードがゲノムDNAのどの領域に由来するのかを調べるために「マッピング」という操作を行います。これは「リファレンス・マッピング」とも呼ばれます。リファレンス、つまり既に読まれたゲノム配列と、リードのデータをマッピング・ツールに与えると、どのリードがどの領域にアラインメントされたかを示すデータが得られます。SAM(Sequence Alignment/Map format)フォーマットは、このデータを記述するためのもので、BAMフォーマットは、SAMをバイナリ形式に変換したものです。

[参考: Hts-specs by samtools](http://samtools.github.io/hts-specs/)

----

### VCF

VCF(Variant Call Format)は、ゲノム上の塩基配列多型の位置とそのIDや精度を記述するためのフォーマットです。データの他に、日付やデータを生成したプログラム名、リファレンス配列などを記述するメタ情報を含みます。リファレンス配列にマッピングされたリードの情報を多型解析プログラムに入力すると、この形式のファイルが生成されます。

[参考: File Specifications - The Global Alliance Data Working Group File Formats Task Team](http://ga4gh.org/#/fileformats-team)

----

## データ処理について

新型シーケンサから得られる一次データ（シークエンスリードデータ）は、そのままでは何の情報も持たないため、情報解析を行うことができません。そのため、データ解析を行う前にデータ処理を行う必要があります。代表的なものは以下の2つです。

- マッピング
    - ゲノム塩基配列やCDSなどを参照配列（リファレンス）として、それぞれのリードがどの領域から得られたかを調べる
- アセンブル
    - 参照配列がない場合などに、リード同士の端の部分の相同性を元にリードを繋ぎあわせコンティグ配列を得る

どちらの処理も、リードの長さや数、リファレンス配列の大きさ、計算機の性能や並列化などに応じて計算時間が変わります。

----

### DDBJパイプラインの使い方

ゲノムサイズやシーケンスのスループットによっては，データ処理に必要なリソースが手元のコンピュータでは賄えないことがあります。そのような場合にはDDBJが提供している「[DDBJ Read Annotation Pipeline](http://p.ddbj.nig.ac.jp/)」を通じて遺伝研スパコンを利用することができます。使い方については以下の資料を参考にしてください。

- [Pipeline Tutorial](http://www.ddbj.nig.ac.jp/search/help/pipeline-tutorial-j.html)
- [統合TV](http://togotv.dbcls.jp/ja/)
    - [今日からはじめるDDBJ Read Annotation Pipeline](http://togotv.dbcls.jp/20100617.html)
    - [DDBJ Read Annotation Pipelineによるde novo Assembly解析](http://togotv.dbcls.jp/20110226.html#p01)
- [DDBJパイプラインとGalaxyによるデータ解析](https://github.com/inutano/training/tree/master/ajacs51)

----

## データ解析について

新型シーケンサから得られたデータは、マッピング、アセンブルなどのデータ処理を行ったあと、それぞれの目的に応じた解析を行うことで、サンプルの持つ特徴的なゲノム領域をリストアップしたり、サンプル間の差を比べたりすることができます。新型シーケンサを用いた実験は、どのようなシーケンサを使うか（リードの数や長さ）、どのようにサンプルDNAを得るかによって区別することができます。サンプルDNAの抽出方法を工夫することで様々な情報をハイスループットに得られることが新型シーケンサが強力なツールとして用いられる理由ですが、そのシーケンシングの種類は非常に多岐にわたります([*-Seq](http://applications.illumina.com/applications/sequencing/ngs-library-prep/library-prep-methods.html#.VIEmdL6Hq2w))。それぞれの目的に応じた解析の手順と、それに用いられる代表的なツールは、既存研究の論文やオンラインフォーラムで調べることができます。

![*-Seq](http://gyazo.com/9fa560a57003974dbd21ab0b74f48654.png)

----

### データ解析の種類

それぞれのシーケンシングにおける解析については、[次世代シークエンス解析スタンダード〜NGSのポテンシャルを活かしきるWET&DRY](http://www.amazon.co.jp/dp/4758101914/ref=cm_sw_r_tw_dp_40sGub0Q8C567)という書籍にもまとめられています。各シーケンスのライブラリ調製から丁寧に解説してあるため、非常に参考になります。困った時は人に聞いたり調べたりする前に、この本を読めばだいたい解決します。1人1冊持っておいて損はありません。おすすめです。

![dritoshi-book](http://gyazo.com/783282872dfc90d3b03226a7da1f1596.png)

----

### NGS現場の会

[NGS現場の会](http://www.ngs-field.org)は新型シーケンサを利用した研究に関わる人が集まる研究会です。アカデミア、企業、研究者、テクニシャン、学生などなどの垣根のいっさいない情報交換のための会を目指しています。データ解析で困ったときに助けてくれる人もいます。第四回研究会が2015年7月1日〜3日につくば国際会議場にて行われますので、ご興味のある方は是非ご参加ください(参加登録開始は2月中旬を予定しています)。

![ngs-genbanokai](http://gyazo.com/9587ff44815076cb7b8a17d0ab000064.png)

----

### Galaxyの使い方

Galaxyは複数のツールを組み合わせたデータ解析ワークフローを構築するための、オープンソースで公開されているウェブ・ベースの解析プラットフォームです。新型シーケンサのデータ解析に限らず、さまざまな生命科学のデータ解析のために、世界中で広く利用されています。

Galaxyには2つの使い方があります。1つは、Public Galaxy Serverと呼ばれる、世界中で様々な機関によって公開されているサーバを使う方法です。日本ではDBCLS, DDBJ, Pitagora-Galaxyプロジェクトなどによって誰でも利用できるサーバが公開されています。

もう1つの使い方は、自分でGalaxyのサーバを起動する方法です。大量のデータを生み出し大規模にデータを解析する研究室では、専用のGalaxyを立ち上げて利用しています。

- [Galaxy Project](https://usegalaxy.org)
- [Galaxy 101: The first thing you should try](https://usegalaxy.org/u/aun1/p/galaxy101)

----

### 本家Galaxy

今回の講習では本家である[usegalaxy.org](https://usegalaxy.org)を利用します。

![honke](http://gyazo.com/9fde98c4f2f9f271aeddc6697fc30b81.png)

----

### Galaxyの基本的な使い方

Galaxyの画面は常に左サイドバー、中央のブラウザ、右サイドバーに三分割されています。左サイドバーは利用できるツールのリストです。中央のブラウザは選択中のツールをコントロールしたり結果を見るために使います。右サイドバーでは、ツールを実行するごとにプロセスがスタックして、履歴として機能します。履歴表示だけでなく、それぞれのプロセスの情報を見たり、再実行したりできます。

![galaxy-ss](http://gyazo.com/2e7c840ca43c4aacfec29c1cfd7809d6.png)

----

### アカウントの作成

[本家Galaxy]のウェブサイトの右上の「User」をクリックして、「Register」をクリックします。Emailアドレス、パスワード、名前を入力してから「Submit」をクリックします。しばらく待つとメールが届くので、メールの中のアクティベーションのURLをクリックして、confirmしたあと、作成したアカウントでログインします。

![create-account](http://gyazo.com/0a94312f2302baff73fc133f5db3170b.png)

----

### データのインポート

先ほどと同じく[こちらのページ](https://www.dropbox.com/sh/vj019i2zwf653t3/AADhYOSz3HRi2GBlDFH_0Sn2a?dl=0)から「demo.fastq」というデータをダウンロードします。Galaxyの左パネルの「Get Data」をクリックして、開いたリストの一番上にある「Upload File」をクリックするとダイアログが開くので、「Choose local file」をクリックしてダウンロードしたdemo.fastqファイルを選択します。リストの「Genome」をクリックして、「Human Feb. 2009 (hg19)」を選択したら、「Start」をクリックします。しばらく待つと完了するので、「Close」をクリックします。

![import-data](http://gyazo.com/baef9e108d341998b57e920bad879a5b.png)

----

目玉ボタンをクリックしてみましょう。インポートが完了したデータの様子です。

![imported](http://gyazo.com/e4ccd26ba42ae96206c75f5a31a59a48.png)

----

### FastQCを実行する

インポートしたFASTQデータのクオリティを確認します。今回は、最もポピュラーな新型シーケンサデータ用クオリティ・コントロールのツールである[FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)を利用します。左パネルの検索ボックスに「fastqc」と入力して出る「FastQC: Read QC」をクリックします。「Short read data from your current history」でdemo.fastqを選んでから「Execute」をクリックすると、プロセスが右パネルの履歴に追加され、しばらくすると実行されます。

![fastqc](http://gyazo.com/3285d55b88827aba4d2949eafbc0de54.png)

----

実行が完了したら、目玉ボタンをクリックします。FastQCの結果を見ることができます。

![fastqc-done](http://gyazo.com/be7b677e1d0e9be85fc917003386b269.png)

----

### 履歴やワークフローの共有

ツールを選択して実行すると、その実行結果が右パネルにスタックしていきます。初期設定では「Unnamed history」となっていますが、これに名前をつけたり、履歴を他のユーザと共有したり、履歴において実行したツールの組み合わせからワークフローを構築することができます。右パネルの右上にある歯車のアイコンをクリックして、履歴に関するメニューを表示します。

![history_management](http://gyazo.com/992db7e62c79022e23b1c3bb79b2669c.png)

----

### 新規ワークフローの作成

FastQCを実行した履歴からワークフローを作成してみましょう。右パネルの歯車アイコンをクリックして、メニューから「Extract Workflow」を選択します。判別しやすい名前をつけてから「Create workflow」をクリックします。

![create_workflow](http://gyazo.com/6f7a03b57d272f6ffcb4a93eba1baf42.png)

----

### 作成したワークフローの実行

画面上部の「Workflow」をクリックすると利用できるワークフローの一覧が表示されます。先ほど作成したワークフローが表示されていますので、名前をクリックして「Run」をクリックすると、ワークフローが読み込まれ、別のデータで同じ処理を繰り返すことができます。

![run_workflow](http://gyazo.com/05f66a9bae7ef204536a5f73aca97fb8.png)

----

### ワークフローのインポート

Galaxyではデータや履歴、ワークフローを保存したり、ユーザ間で共有するだけでなく、Galaxyサーバを通じて公開することができます。ここでは、他のユーザによって公開されているワークフローを利用してデータ解析を行ってみます。まず、画面上部の「Shared Data」をクリックして「Published Workflow」をクリックします。

![published-workflow](http://gyazo.com/5a32a7ad6a9eb347fe24da8c357710f8.png)

----

### Exome Sequencing Workflow

左上の検索ボックスに「exome」と入力してエンターキーを押します。表示された「Exome Analysis」をクリックします。ワークフローの概要が表示されるので、右上のフロッピーディスクアイコンの隣にある緑色の+ボタンをクリックします。この操作は、ログインしていないとできないため、失敗した場合はログインできているかを画面上部の「User」から確認してください。

![workflow-detail](http://gyazo.com/dcd266543e799aafad092967662f1044.png)

----

完了しました。「start using this workflow」をクリックします。

![done](http://gyazo.com/7b90fd4fa9396c54f8055972658bcfbe.png)

----

### ワークフローを編集する

表示されたページで再び「Exome Analysis」をクリックして「View」をクリックすると、詳細を見ることができます。このワークフローでは、FASTQデータを入力として、BWAによるマッピングを行ったのち、多型を検出するツールを実行しています。しかし、BWAの操作は時間がかかるので、DDBJのパイプラインで予め処理した結果のSAMファイルを入力データとして用いることで、このステップをはぶきます。まず、[こちらのページ](https://github.com/inutano/training/ajacs-advanced-01)から、demo.samをダウンロードします(このデータのオリジナルは[こちら](http://trace.ddbj.nig.ac.jp/DRASearch/run?acc=SRR975299))。これを先ほどと同じ手順でgalaxyにインポートしてから、画面上部の「Workflow」をクリックして再度ワークフローのページを表示します。「Imported: Exome Analysis」をクリックして、「Edit」をクリックします。

![workflow](http://gyazo.com/6f4552370aee627688a88d2cc240703c.png)

----

ワークフロー・エディターが表示されます。表示されている「Map with BWA for Illumina」のボックスの✕ボタンをクリックしてこのプロセスを消します。

![workflow-editor](http://gyazo.com/888a21b61ed6623764575d0b443cad1b.png)

----

消えた様子です。

![eliminated](http://gyazo.com/3ff991ff9cb03aeb3b9d936abcc9437b.png)

----

このワークフローは配列断片の両端をシーケンスするPaired-Endのデータを入力として想定されています。Paired-Endの場合はforward/reverseそれぞれにファイルが2つ作られますが、今回はそれらをマッピングした結果のsamファイルを入力とするので、入力データは1つです。2つある「Input dataset」のどちらか片方を消して、残った「Input dataset」のボックス右側のボタンをドラッグ＆ドロップで「SAM-to-BAM」のボックスに繋げます。

![connect](http://gyazo.com/ad64ec157767d2bb3e277e45336747f9.png)

----

エディタ右上の歯車ボタンを押して「save」をクリックします。そのあと、同じく歯車ボタンを押して「Run」をクリックします。

![save](http://gyazo.com/876e9066f1abed8afd91a678d90389aa.png)

----

表示されたワークフローの入力ファイルとして「demo.sam」を選択します。ファイルが表示されない場合はアップロードが完了していない場合があるので、しばらく待って右パネル歯車アイコンの左にある「Reflesh history」をクリックするか、再度データアップロードをしてみてください。データがアップロードされたら、実行されるツール群とその情報を見てみましょう。

![edited-workflow](http://gyazo.com/8b6bfcf88cdff6136cdac6e38583e657.png)

----

ワークフローに含まれるツールの中には実行時にパラメータを要求するものがあります。このワークフローでは、変異をフィルタリングする場合におけるアレル頻度のしきい値と、出力ファイル名に利用するためのサンプル名が必要です。アレル頻度を要求するのはワークフロー中の「Annotation」の項目で、これは「Annovar」というソフトウェアのフィルタ機能を利用しています。AnnovarのフィルタではdbSNPや1000人ゲノムプロジェクトによって蓄積された変異の頻度情報を利用して、コモンな変異を取り除きます。今回は5%以上の頻度のものを除くように設定して実行します。Annovarについて詳しい情報は[こちら](http://www.openbioinformatics.org/annovar/)をご覧下さい。

![added_parameters](http://gyazo.com/f8ce14bcf0cb5bf850ac75a563a9ffe4.png)

----

確認したら、中央パネルの一番下の「Run Workflow」をクリックします。

![click_run_button](http://gyazo.com/30a6cc4a8bfe5141b091081b1c12144b.png)

----

ワークフローが実行されました。あとは待つだけです。

![running](http://gyazo.com/0da2ed2d9af14390729af744a536c32d.png)

----

データを確認してみましょう。

![confirmation]()

----

### ちょっとマニアックな話: usegalaxy.orgの後ろ側はどうなっているのか

本家GalaxyのPublic Serverは世界中の人から利用されているため、単一の計算機システムでは対応できません。負荷を分散するため、US国内の複数のデータセンターに接続しているそうです。詳しくは昨年の[Biological Data Science 2014](http://meetings.cshl.edu/meetings/2014/data14.shtml)でのJames Taylorによる[こちら](https://speakerdeck.com/jxtx/adventures-in-scaling-galaxy-at-biological-data-science-2014)のスライドをご覧ください。

![adventures_in_scaling_galaxy](http://gyazo.com/63fcb3086244c292638dfba6fc76c264.png)

![distribution_among_us](http://gyazo.com/87956e458af84bc90b801d73a52e3594.png)

----

### もっとマニアックな話: Docker で Galaxy

推奨環境でGalaxy環境をセットアップするためのDocker Container ImageをGalaxyコミッタの1人であるBjörn Grüningが公開してくださっています。GitHubのレポジトリは[こちら](https://github.com/bgruening/docker-galaxy-stable)です。コンテナは以下のコマンドで起動できます。

```sh
docker run -d -p 8080:80 -p 8021:21 bgruening/galaxy-stable
```

----

### Pitagora-Galaxy

Galaxyはこのように、ウェブブラウザで誰でも簡単にデータ解析ができるようになっています。しかし、自分のラボで独自のGalaxyを使いたいという場合には、まっさらな状態からのツールのインストールや、ワークフローの構築が難しいという難点があります。そこで、日本国内のGalaxyユーザが集まってお互いのワークフローを持ち寄って、簡単にインストールできる形で配るというプロジェクトが始まりました。それが[Pitagora Galaxy](http://www.pitagora-galaxy.org)です。誰でも使えるテストサイトもありますが、「Oracle VirtualBox」と呼ばれるフリーのソフトウェアを使って簡単にGalaxyを自分のコンピュータにインストールすることができます。また、Amazon Web Service上で動くクラウド版もあります。

![pg](http://gyazo.com/2ff7ed946475c60484d3004a781a69c5.png)

----

## 以上で終了です！

おつかれさまでした！
