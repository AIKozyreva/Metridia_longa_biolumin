## Quality check and fixing raw data

#### I will use fastQC, multiQC, curadapt or trimmomatic, raw data are in .fastq format. structure of data visualised below

```
for filename in *.fastq;
do
echo -e “$filename\t `cat $filename | wc -l | awk ‘{print $1 /
4}’`”
done
```
