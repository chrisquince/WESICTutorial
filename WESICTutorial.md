# Taxonomic and Functional Profiling


## Taxonomic profiling

We will start by taxonomically profiling the Thames reads with Kraken. We will use forward reads only:

```
mkdir ~/Projects/Thames
cd ~/Projects/Thames
ln -s ~/Data/Thames/Reads .
```

Run Kraken on all samples:
```
mkdir Kraken
for file in Reads/*R1*fastq
do
    base=${file##*/}
    stub=${base%_R1.fastq}
    echo $stub
    kraken --db ~/Databases/minikraken_20141208/ --threads 8 --preload --output Kraken/${stub}.kraken $file
done
```

We match against the 'minikraken' database which corresponds to RefSeq 2014.
Would we expect the profile to differ between R1 and R2?

Look at percentage of reads classified. Sediments are under studied communities!

Discussion point what can we do about under representation in Database?

The output is just a text file:

```
head Kraken/p5_A01_Sub.kraken
```

And we can generate a report:

```
kraken-report --db ~/Databases/minikraken_20141208/ Kraken/p5_A01_Sub.kraken >  Kraken/p5_A01_Sub.kraken.report
```

Some people prefer a different format:
```
kraken-mpa-report --db ~/Databases/minikraken_20141208/ Kraken/p5_A01_Sub.kraken > Kraken/p5_A01_Sub.kraken.mpa.report
```

We can get a report of the predicted genera:
```
cat  Kraken/p5_A01_Sub.kraken | awk '$4=="G"'
```

What is awk?

Now lets get reports on all samples:
```
for file in Kraken/*.kraken
do
    stub=${file%.kraken}
    echo $stub
    kraken-report --db ~/Databases/minikraken_20141208/ $file >  ${stub}.kraken.report
done
```

Having done this we want to get one table of annotations at the genera level for community comparisons:

```
for file in Kraken/*.kraken.report
do
    stub=${file%.kraken.report}
    cat  $file | awk '$4=="G"' > $stub.genera
done
```

