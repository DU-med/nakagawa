# autosnippy チュートリアル

GitHub : [autosnippy](https://github.com/pedroscampoy/autosnippy)  
論文 : [Microevolution, reinfection and highly complex genomic diversity in patients with sequential isolates of Mycobacterium abscessus](https://www.nature.com/articles/s41467-024-46552-w)

## 🖥️　解析手順

### リポジトリのクローン
```
git clone https://github.com/pedroscampoy/autosnippy.git
```

### ディレクトリの移動
```
cd autosnippy
```

### autosnippy.ymlファイルからperl-xml-parserを削除
環境構築の際に下記を実行しないと構築に失敗する  
```
sed -i '/perl-xml-parser/d' autosnippy.yml
```

### autosnippy.ymlから環境の構築
```
mamba env create -f autosnippy.yml -y
```

### 構築した仮想環境のアクティベートおよび追加パッケージのインストール
```
mamba activate autosnippy
mamba install bioconda::entrez-direct -y
```

### 参照データ保存ディレクトリの作成
```
mkdir -p data/ref # 参照配列保存用
mkdir -p data/m_abs # M. abscessusのgbkファイル保存
mkdir -p data/m_mas # M. abscessus subsp. massilienseのgbkファイル保存
mkdir -p data/fastq/m_abs data/fastq/m_mas # それぞれの種のサンプルfastqファイ保存
mkdir -p output # 出力ファイルの保存
```

### fastqファイルの保存
M. abscessusのfastqファイルはdata/fastq/m_absに  
M. abscessus subsp. massilienseのfastqファイルはdata/fastq/m_masに

### 参照配列の取得及びgunzip
CU458896.1 : [Mycobacterium abscessus ATCC 19977 chromosome, complete sequence](https://www.ncbi.nlm.nih.gov/nuccore/CU458896.1)  
NC_018150.2 : [Mycobacteroides abscessus subsp. massiliense str. GO 06, complete sequence](https://www.ncbi.nlm.nih.gov/nuccore/NC_018150.2)

```
# M. abscessのゲノムfastaファイルのダウンロード
esearch -db nuccore -query "CU458896.1" | efetch -format fasta > data/ref/CU458896.1.fasta

# M. abscessus subsp. massilienseのゲノム(NC_018150.2)fastaファイルのダウンロード
esearch -db nuccore -query "NC_018150.2" | efetch -format fasta > data/ref/NC_018150.2.fasta
```
### gbkファイルの取得
```
# M. abscessのgbkファイルのダウンロード
esearch -db nucleotide -query "CU458896.1" | efetch -format gbwithparts > data/m_abs/genes.gbk

# M. subsp. massilienseのgbkファイルのダウンロード
esearch -db nucleotide -query "NC_018150.2" | efetch -format gbwithparts > data/m_mas/genes.gbk
```

### refseq.genomes.k21s1000.mshの取得
```
wget https://gembox.cbcb.umd.edu/mash/refseq.genomes.k21s1000.msh -O data/refseq.genomes.k21s1000.msh
```

### snpEffのdatabase build用のgenome.configファイルの作成
```
echo "m_abs.genome : Mycobacterium abscessus ATCC 19977 chromosome, complete sequence" >> snpEff.config
echo "m_mas.genome : Mycobacteroides abscessus subsp. massiliense CCUG 48898 = JCM 15300" > snpEff.config
```
### 参照配列からsnpEffのデータベースをビルド
```
snpEff build -genbank -v m_abs
snpEff build -genbank -v m_mas
```

###  コマンド
```
# M.abscessus 
python autosnippy.py -i data/fastq/m_abs -r data/ref/CU458896.1.fasta  -T 30 -o output --mash_database data/refseq.genomes.k21s1000.msh --snpeff_database m_abs

# M. abscessus subsp. massiliense
python autosnippy.py -i data/fastq/m_mas -r data/ref/NC_018150.2.fasta -T 30 -o output --mash_database data/refseq.genomes.k21s1000.msh --snpeff_database m_mas
```
オプション  
-i: fastqディレクトリにすべてのfastqファイルを保存  
-r: 参照配列を保存  
-T: スレッドを指定  
-o: 結果を保存するディレクトリ  
--mash_database: wget https://gembox.cbcb.umd.edu/mash/refseq.genomes.k21s1000.msh で取得したファイルを指定  
--snpeff_database: snpEffでビルドしたデータベースを指定  
