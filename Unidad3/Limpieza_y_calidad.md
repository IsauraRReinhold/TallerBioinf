# 4.1 Limpieza y calidad

## Introducción

De manera general, los análisis de datos de secuenciación masiva son fuertemente influenciados por la calidad de los propios datos. Por lo que es imprescindible realizar protocolos de evaluación de calidad, limpieza y filtrado de los datos crudos, de manera previa a los análisis principales.

¿Qué es lo que descartamos?

- Lecturas de mala calidad (o fragmentos)

- Secuencias artificiales (ej. adaptadores, primers)

  

## 4.1.1 Formatos de secuencias

### .fasta (DNA o RNA -> .fna, .fn AA -> .faa, .fa)

Cada secuencia contiene 2 lineas:

1.- *Header*: inicia con "**>**" y contiene el nombre de la <span style="color:red"> secuencia </span> (y puede contener más información de la secuencia).

2.- Secuencia: Puede estar en 1 o varias lineas. DNA, RNA, AA 

```
less sequence.fasta
```

```
>gi|971468117|gb|KT354980.1| Laccaria trichodermophora strain EF36 internal transcribed spacer 1, partial sequence; 5.8S ribosomal RNA gene and internal transcribed spacer 2, complete sequence; and 28S ribosomal RNA gene, partial sequence
TGATGTGACTGTTAGCTGGCTTTTCGAAGCATGTGCTCGTCCATCATCTTTATCTCTCCACCTGTGCACA
TTTTGTAGTCTTGGATACCTCTCGAGGAAACTCGGATTTTAGGATCGCCGTGCTGTACAAGTCGGCTTTT
CTTTCATTTCCAAGACTATGTTTTTATATACACCAAAGTATGTTTATAGAATGTCATCAATGGGAACTTG
TTTCCTATAAAATTATACAACTTTCAGCAACGGATCTCTTGGCTCTCGCATCGATGAAGAACGCAGCGAA
ATGCGATAAGTAATGTGAATTGCAGAATTCAGTGAATCATCGAATCTTTGAACGCACCTTGCGCTCCTTG
GTATTCCGAGGAGCATGCCTGTTTGAGTGTCATTAAATTCTCAACCTTCCAACTTTTATTAGCTTGGTTA
GGCTTGGATGTGGGGGTTGCGGGCTTCATCACTGAGGTCGGCTCTCCTTAAATGCATTAGCGGAACTTTT
GTGGACCGTCTATTGGTGTGATAATTATCTACGCCGTGGATGTGAAGCAGCTTTATGAAGTTCTGCTTCT
AACCGTCCATTGACTTGGACAATTTTGACAATTTGACCTCAAATCAGGTAGGACTACCCGCTGAACTTAA
GCATATC
```



### .fastq (q = quality)

1.- *Header*: inicia con "**@**" y contiene el nombre de la <span style="color:red">lectura</span> (compuesto por varias secciones).

2.- *Read*: en una solo linea

3.- Otro *header*: al menos contiene un "**+**" y puede repetir la información del *header*

4.- *Phred score*: la calidad de cada base codificado en algún formato [ASCII](http://drive5.com/usearch/manual/quality_score.html).

```
head -n 4 L.t_nextseq_R1.fasta
```

```
@HWI-ST999:102:D1N6AACXX:1:1101:1235:1936 1:N:0:
ATGTCTCCTGGACCCCTCTGTGCCCAAGCTCCTCATGCATCCTCCTCAGCAACTTGTCCTGTAGCTGAGGCTCACTGACTACCAGCTGCAG
+
1:DAADDDF<B<AGF=FGIEHCCD9DG=1E9?D>CF@HHG??B<GEBGHCG;;CDB8==C@@>>GII@@5?A?@B>CEDCFCC:;?CCCAC
```



Las tecnologías de secuenciación masiva de tercera generación entregan los datos crudos en otros formatos: Sequel [.bam](https://dnatech.genomecenter.ucdavis.edu/faqs/which-data-will-i-receive-from-the-pacbio-sequel-sequencer-will-they-have-quality-scores/) y MinION [.fast5](https://medium.com/@shiansu/a-look-at-the-nanopore-fast5-format-f711999e2ff6).



## 4.1.2 Análisis de calidad

### FastQC

Herramienta para el fácil y rápido análisis de calidad de datos .fastq y este es el  [manual](https://dnacore.missouri.edu/PDF/FastQC_Manual.pdf).

¿Qué tan certeras/confiables son las palomas y taches en report?

¿Qué es Casava?

¿Qué representa cada color del Per Base Sequence Quality plot?

¿Cómo influye el tipo de muestra en:

- Per Base Sequence Content plot
- Duplicate Sequencest plot
- Overrepresented Sequences

?

Ejercicio:

Evaluar la calidad de los datos *toy*

- entrar al servidor [así](https://github.com/AliciaMstt/TallerBioinf/blob/master/Unidad3/ssh.txt)
- git clone /la Unidad 3/
- ejecutar FastQC:

```
#go to the main wd
cd H.hv6_toy/
#do the QC analysis
fastqc -o data/ -f fastq data/Hhv6.R1.fastq data/Hhv6.R2.fastq
#decompress ressults
unzip data/*.zip
```

los resultados se pueden visualizar con un explorador como firefox

```
firefox data/Hhv6.R1.fastqc.html data/Hhv6.R2.fastqc.html
```

Todxs tienen XQuartz? (o algo así)

Si no, mejor desde un shell local:

```
#get file to my local disc
scp gene19@172.16.9.173:/LUSTRE/Genetica/common/gene19/TallerBioinf/Unidad3/H.hv6_toy/data/*.html .
```

 

## 4.1.3 Limpieza

### Trim Galore!

**USAGE:** `trim_galore [options] <filename(s)>`

[manual](https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md)



Consta de 4 pasos principales:

1: Quality Trimming

```
-q/--quality <INT>
```

- Default Phred score: `20`

2: Adapter Trimming

```
-a/--adapter <STRING>
-a2/--adapter2 <STRING>
-s/--stringency <INT>
```

- Default busca: Illumina universal, Nextera transposase o Illumina small RNA

3: Removing Short Sequences

```
--length <INT>
--max_length <INT>
```

4: Specialised Trimming

```
--max_n COUNT
--trim-n
--clip_R1 <int>
--three_prime_clip_R1 <int>
```

Además:

```
--fastqc_args "<ARGS>"
-o
--paired
--retain_unpaired
-j/--cores INT
```



Cada uno de estos pasos involucra parámetros particulares, algunos se "auto-detectan" o se asignan.

Ejercicio:

Después de analizar la calidad de los .fastq, límpialos con trim galore <span style="color:red">con los parámetros que tú decidas</span>.

 

```
cd H.hv6_toy/

trim_galore --fastqc --paired --retain_unpaired --three_prime_clip_R1 20 --three_prime_clip_R2 20 -o data/clean data/Hhv6.R1.fastq data/Hhv6.R2.fastq
```



### Trimmomatic

[manual](http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf)



### Principales diferencias entre Trim Galore y Trimmomatic

- FastQC
- threads
- windows
- adapter file

