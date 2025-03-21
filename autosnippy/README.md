# autosnippy チュートリアル

[autosnippy](https://github.com/pedroscampoy/autosnippy)  
[Microevolution, reinfection and highly complex genomic diversity in patients with sequential isolates of Mycobacterium abscessus](https://www.nature.com/articles/s41467-024-46552-w)

## 内容

### 1. リポジトリのクローン
```
git clone https://github.com/pedroscampoy/autosnippy.git
```

### ディレクトリの移動
```
cd autosnippy
```

### autosnippy.ymlファイルからperl-xml-parserを削除
```
sed -i '/perl-xml-parser/d' autosnippy.yml
```

### autosnippy.ymlから環境の構築
```
mamba env create -f autosnippy.yml

mamba install bioconda::entrez-direct
```

### autosnippyのアクティベート
```
mamba activate autosnippy
```

### 参照データ保存ディレクトリの作成
```
mkdir ref
mkdir -p data/m_abs
mkdir -p data/m_mas
```

### 参照配列の取得及びgunzip
CU458896.1
[Mycobacterium abscessus ATCC 19977 chromosome, complete sequence](https://www.ncbi.nlm.nih.gov/nuccore/CU458896.1)  
NC_018150.2
[Mycobacteroides abscessus subsp. massiliense str. GO 06, complete sequence](https://www.ncbi.nlm.nih.gov/nuccore/NC_018150.2)

```
# M. abscessのゲノムfastaファイルのダウンロード
esearch -db nuccore -query "CU458896.1" | efetch -format fasta > ref/eU458896.1.fasta
# M. massilienseのゲノム(NC_018150.2)fastaファイルのダウンロード
esearch -db nuccore -query "NC_018150.2" | efetch -format fasta > ref/NC_018150.2.fasta
```
### gbkファイルの取得
```
# M. abscessのgbkファイルのダウンロード
esearch -db nucleotide -query "CU458896.1" | efetch -format gbwithparts > data/m_abs/genes.gbk
# M. massilienseのgbkファイルのダウンロード
esearch -db nucleotide -query "NC_018150.2" | efetch -format gbwithparts > data/m_mas/genes.gbk
```

### refseq.genomes.k21s1000.mshの取得
wget https://gembox.cbcb.umd.edu/mash/refseq.genomes.k21s1000.msh -O data/refseq.genomes.k21s1000.msh
### snpEffのdatabase build用のgenome.configファイルの作成
```
echo "m_mas.genome : Mycobacteroides abscessus subsp. massiliense CCUG 48898 = JCM 15300" > snpEff.config
echo "m_abs.genome : Mycobacterium abscessus ATCC 19977 chromosome, complete sequence" >> snpEff.config
```
### 参照配列からsnpEffのデータベースをビルド
```
snpEff build -genbank -v m_abs
snpEff build -genbank -v m_mas
```

###  コマンド
```
python autosnippy.py -i fastq -r ref/GCF_000069185.1_ASM6918v1_genomic.fna  -T 30 -o output --mash_database refseq.genomes.k21s1000.msh --snpeff_database m_mas
```
オプション
-i: fastqディレクトリにすべてのfastqファイルを保存
-r: 参照配列を保存
-T: スレッドを指定
-o: 結果を保存するディレクトリ
--mash_database :wget https://gembox.cbcb.umd.edu/mash/refseq.genomes.k21s1000.mshで取
得したファイルを指定
--snpeff_database :snpEffでビルドしたデータベースを指定
