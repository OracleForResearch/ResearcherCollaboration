
NOTE: This is currently a DRAFT dump of the report. Work in progress 

Oracle for metagenome reanalysis report

The first specific goal for this project was to assess the pipeline performance on Oracle BM machines, where the NVMe SSDs are expected to substantially improve performance. Subsequently, we estimated the time required to complete the entire pipeline run, and looked into further runtime optimisations.
Oracle Cloud Infrastructure (OCI) Tenancy setup
We ran MGnify assembly pipeline version 5.0 (Mitchell et al. 2020) for 10 assemblies downloaded from the European Nucleotide Archive (ENA). For comparison purposes we utilized one kind of virtual machine and one of bare metal, as follows:

ssd:
Flavor: VM.DenseIO2.16
Storage: NVMe SSD
bm
Flavor: BM.DenseIO2.52 
Storage: NVMe SSD
Activities
The MGnify pipeline is written in the Common Workflow Language (CWL) and the CWL execution engine of preference is Toil. Toil provides support for Docker to execute workflows improving the pipeline portability. Considering the complexity of the pipeline, the first activity undertaken was to increase the number of containerized tools and to trim down the size of the containers. When running the pipeline using Docker we encountered a software error in Toil. We were able to fix this error, and will contribute the fixes to the Toil project. Having fixed the docker support we then encountered a problem with Toil support for the job scheduler SLURM. Unfortunately this bug hasn’t yet been addressed by Toil, and therefore the solution was to switch to the IBM LSF job scheduler.
Pipeline performance comparison
We measured the pipeline performance on OCI and compared it with the existing performance on the EMBL-EBI in-house high-performance computing (HPC) cluster. The following table summarises the performance assessment results, showing the average turnaround time in hours and run time for the 3 different set-ups. The turnaround time is the total time a job took, including any time the job was pending due to lack of resources. 


host
average turnaround time (hrs)
average run time (hrs)
embl-ebi-hpc
127
105
bm
60
60
ssd*
128
120
* ssd results are not what we expected. It would be worth investigating the ssd performance in more detail.

The results from the tests show a significant performance improvement of the pipeline when executed in OCI compared to the EMBL-EBI HPC cluster. The throughput of OCI surpasses the EMBL-EBI cluster as the average turnaround time is significantly lower, this is expected as the HPC cluster is shared and the jobs are often put on hold until the resources are freed. 

This test has proven the benefits of OCI over the in-house HPC cluster for the reanalysis project. There are currently over 30,000 samples to be analysed with the latest version of the pipeline. Having a dedicated tenancy in OCI will greatly speed up this process. This will have a significant impact on the relevance of MGnify helping as one of the leading metagenomics hubs.

In the BM machine each sample takes roughly 60 hours, our pipeline is heterogeneous and based on the analysis of the behaviour during this test we arrived at the conclusion that uses in average 6 CPU cores per job. Considering the latter we get that each job takes approximately 360 CPU hours. Based on the average CPU time each sample requires, if we were to reanalyse the complete backlog of 30.000 samples it would take 10 million CPU hours. This estimation is an upper end estimation as the run time varies per sample, depending on multiple factors such as the biome it belongs to and the sequencing technology to name a few.

Two additional differences to the new pipeline that are particularly pertinent are (1) the surveying of viruses within environmental samples and (2) an enhanced module for indexing the MGnify proteins. The MGnify protein database is at the core of the recent advancement of AlphaFold2 (algorithm for de novo protein structure prediction) from DeepMind. We anticipate this will double the current protein database size. Both of these are anticipated to give rise to many additional discoveries and applications. 
