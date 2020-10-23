
# Running fastSTRUCTURE on the JCU HPC

### Prepare input

I filtered it in R, and had genlight objects. To export use the below (from [radiator](https://thierrygosselin.github.io/radiator/) package)

```{r eval=FALSE}
genomic_converter(gdsGL, output = "faststructure", filename = "02_FastStructure_Input")
```

I then copy it to the project folder. As an example, let's make a projectA folder

```{bash eval=FALSE}
mkdir ~/projectA
```

We then need to get the python scripts. For up to date instructions on this, check the [fastStructure official github](https://rajanil.github.io/fastStructure/)

```{bash eval=FALSE}
cd ~/projectA
git clone https://github.com/rajanil/fastStructure
```

### Prepare .sh file

First, let me breakdown the steps that will be included.

#### Load fastStructure environment

Lucky us, the HPC people have already set up a fastStructure environment (thank you!)

To activate, use the following:

```{bash eval=FALSE}
module load anaconda3
source $CONDA_PROF/conda.sh
conda activate fastStructure
```

#### Run fastStructure

You will have to run each K independently, and run multiple times to estimate the likelihood parameters used to find the best K. Example below is from K=2 to K=N, with three runs per value of K. Adjust as you see fit.

```{bash eval=FALSE}
python structure.py -K 2 --input=genotypes --output=genotypes_output_K2_A
python structure.py -K 2 --input=genotypes --output=genotypes_output_K2_B
python structure.py -K 2 --input=genotypes --output=genotypes_output_K2_C

python structure.py -K 3 --input=genotypes --output=genotypes_output_K3_A
python structure.py -K 3 --input=genotypes --output=genotypes_output_K3_B
python structure.py -K 3 --input=genotypes --output=genotypes_output_K3_C

python structure.py -K N --input=genotypes --output=genotypes_output_KN_A
python structure.py -K N --input=genotypes --output=genotypes_output_KN_B
python structure.py -K N --input=genotypes --output=genotypes_output_KN_C
```

#### Now, below an example of your sh. file to be submitted with qsub

This is an example, with *#* lines being copied straight from the [HPC page](https://secure.jcu.edu.au/confluence/display/Public/HPRC+PBSPro+script+files), adjust as needed (memory, nodes, etc)

*To-Do for lorenzo: loop that takes number of K values and iterations and produces individual lines. Used to exist, lost somewhere. . . .*

```{bash eval=FALSE}
#!/bin/bash

#PBS -j oe
#PBS -N fastStructureRun
#PBS -l nodes=1:ppn=1
#PBS -l pmem=200mb
#PBS -l walltime=05:00:00
#PBS -m ae
#PBS -M your.name@jcu.edu.au

cd ~/projectA/fastStructure

module load anaconda3
source $CONDA_PROF/conda.sh
conda activate fastStructure

python structure.py -K 2 --input=genotypes --output=genotypes_output_K2_A
python structure.py -K 2 --input=genotypes --output=genotypes_output_K2_B
python structure.py -K 2 --input=genotypes --output=genotypes_output_K2_C

python structure.py -K 3 --input=genotypes --output=genotypes_output_K3_A
python structure.py -K 3 --input=genotypes --output=genotypes_output_K3_B
python structure.py -K 3 --input=genotypes --output=genotypes_output_K3_C

python structure.py -K N --input=genotypes --output=genotypes_output_KN_A
python structure.py -K N --input=genotypes --output=genotypes_output_KN_B
python structure.py -K N --input=genotypes --output=genotypes_output_KN_C
```

Save the above as a .sh script, then you can qsub your job and wait for the results!

### Select best value of K and plot in R

After running fastStructure for K values between 2 and N (x3), I use pophelper to estimate the most likely value of K and display the resulting barplots

```{r eval=FALSE}
runs <- list.files(path = "~/folder/containing/results",
                   pattern = "meanQ",full.names = TRUE)
fastSTR <- readQ(files = runs, filetype = "basic")
```

Haven't fixed the below. in general gdsGL is a genlight object I use to get pop names. Individuals need to be in the same order as they are in the fastSTR file. That line of code will likely not be useful for most people, having different data structures and workflows. fastSTR is the list of Q files from the structrue run.

```{r eval=FALSE}
fastSTR <- alignK(fastSTR)
grplab = data.frame("loc"=substr(gdsGL$ind.names,1,2))
grplab$loc <- as.character(grplab$loc)
f1 <- plotQ(fastSTR[3], barbordercolour = "white",barbordersize = 0.05,barsize = 1,
            splab = "K = 3", grplab = grplab,clustercol = c("blue","darkgreen","red"),
            linepos = 1,grplabpos = 0.7, height = 2,
            outputfilename="11_fastSTR",imgtype="tiff",dpi = 500,returnplot = T)
grid.arrange(f1$plot[[1]])
```
