## Quality check and fixing raw data

#### I will use fastQC, multiQC, curadapt or trimmomatic, raw data are in .fastq format. structure of data visualised below

work on organisation server, working folder is: `/mnt/SSD4TB/PROJECTS/kozyreva_works/metridia_master/raw/again`

let's look to the files, before i had had .fastq.tar archive, so `tar -xf P017.fastq.tar` was made.
Now i have got an "analysis/fastq" folder in my working directory, which contains a bunch of .fastq.gz files. 

```
for filename in *.fastq.gz;do gzip -d $filename; done

multiqc ./*.zip -o ../multiqc_report -n rew_data_multi_report --export ../multiqc_plots --pdf --data-format tsv
```
![image](https://github.com/user-attachments/assets/cb90187d-5a09-48ff-a001-09b8e448ade5)

![image](https://github.com/user-attachments/assets/ac997f2a-7337-469c-9f51-43573f179522)

Данные хорошего качества, обрезать и редактировать ничего не буду. Адаптерных последовательностей нет. Есть 20 overrepresented последовательностей. Сделала для них бласт против всей нуклеотидной базы по алгориму megablast (the highest similarity). Из 20-ти последовательностей большинство 8 выравнивается на рибосомальную рнк родственного вида Metridia gerlachei (EU375503.1) с идентичностью не ниже 95%. При этом 2 пооследовательности потенциально являются контаминантами или слишком короткими универсальными последовательностями -  выравниваются на большое количество различных организмов, это последовательности номер 5 и номер 17 файла overrepresented_seqs.fasta. Остальные 10 последовательностей не имеют каких-либо совпадений. 
Решено 2 сомнительных хита не выкидывать из данных, так как генома Metridia longa ни у кого нет, и я не могу быть уверена, что последовательность не из него, а мне ещё собирать транскриптом. 

Хорошо, проверка качества окончена, можно приступать к слудующему шагу. 

