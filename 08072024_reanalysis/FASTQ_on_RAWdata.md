## Quality check and fixing raw data

#### I will use fastQC, multiQC, curadapt or trimmomatic, raw data are in .fastq format. structure of data visualised below

work on organisation server, working folder is: `/mnt/SSD4TB/PROJECTS/kozyreva_works/metridia_master/raw/again`

let's look to the files, before i had had .fastq.tar archive, so `tar -xf P017.fastq.tar` was made.
Now i have got an "analysis/fastq" folder in my working directory, which contains a bunch of .fastq.gz files. 

```
for filename in *.fastq.gz;do gzip -d $filename; done

multiqc ./*.zip -o ../multiqc_report -n rew_data_multi_report --export ../multiqc_plots --pdf --data-format tsv
```


Данные хорошего качества, обрезать и редактировать ничего не буду. Есть 20 overrepresented последовательностей. Сделала для них бласт против всей нуклеотидной базы по алгориму megablast (the highest similarity). Хиты для каждой представлены в списке ниже.

