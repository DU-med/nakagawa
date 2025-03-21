# autosnippy チュートリアル

[autosnippy](https://github.com/pedroscampoy/autosnippy)  
[Microevolution, reinfection and highly complex genomic diversity in patients with sequential isolates of Mycobacterium abscessus](https://www.nature.com/articles/s41467-024-46552-w)

## 内容

### 1. リポジトリのクローン
```
git clone https://github.com/pedroscampoy/autosnippy.git
```

### autosnippy.ymlファイルからperl-xml-parserを削除
```
sed -i '/perl-xml-parser/d' autosnippy.yml
```

### autosnippy.ymlから環境の構築
```
mamba env create -f autosnippy.yml
```

### autosnippyのアクティベート
```
mamba activate autosnippy
```

### 参照配列の取得及びgunzip
CU458896.1
[Mycobacterium abscessus ATCC 19977 chromosome, complete sequence](https://www.ncbi.nlm.nih.gov/nuccore/CU458896.1)  
NC_018150.2
[Mycobacteroides abscessus subsp. massiliense str. GO 06, complete sequence](https://www.ncbi.nlm.nih.gov/nuccore/NC_018150.2)

```
# M. abscessのゲノムfastaファイルのダウンロード
wget "https://www.ncbi.nlm.nih.gov/sviewer/viewer.fcgi?id=CU458896.1&db=nuccore&report=fasta&retmode=text" -O CU458896.1.fasta
# M. massilienseのゲノムfastaファイルのダウンロード
wget "https://www.ncbi.nlm.nih.gov/search/api/sequence/NC_018150.2/?report=fasta&format=text" -O NC_018150.2.fasta
```

# snpEff
参照配列からsnpEffのデータベースをビルド

# コマンド
python autosnippy.py -i fastq -r ref/GCF_000069185.1_ASM6918v1_genomic.fna  -T 30 -o output --mash_database refseq.genomes.k21s1000.msh --snpeff_database m_mas

# オプション
-i: fastqディレクトリにすべてのfastqファイルを保存
-r: 参照配列を保存
-T: スレッドを指定
-o: 結果を保存するディレクトリ
--mash_database :wget https://gembox.cbcb.umd.edu/mash/refseq.genomes.k21s1000.mshで取得したファイルを指定
--snpeff_database :snpEffでビルドしたデータベースを指定
