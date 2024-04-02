#### Identification of viruses and other pangenomic elements within SAGs

In this tutorial we are focusing on viruses from plates AM-388 and AM-390 from GORG-Dark.  

Notebooks 1, 2 and 3 will take you through virus searching for and curation of viruses within an indivial SAG.

To get started, open a terminal and navigate into your working directory:

```
cd ~/storage/userlab/{your name here}
```

then clone this github repository:  

```
git clone 
```

and navigate into this directory in terminal:

```
cd 
```

Navigate into this directory in the jupyterhub navigation pane on the left side of your screen, select ```1_finding_phages.ipynb``` to open it in a new window in jupyterhub and let's find some viruses.


---
#### Virus Finding
[VirSorter2](https://github.com/jiarong/VirSorter2):
```
source activate /mnt/storage/envs/vs2
virsorter run -i ${contigs_fasta} --include-groups "dsDNAphage,ssDNA"
```

[DeepVirFinder](https://github.com/jessieren/DeepVirFinder):
```
source activate /mnt/storage/envs/dvf
python /mnt/storage/envs/opt/DeepVirFinder/dvf.py -i ${contig} -o ${plateout} -c ${cpus}
```
---
### Identification of initial candidates

Results from the above workflows were merged, contigs were maintained as initial viral candidates based on the following criteria:
* VirSorter2: max_score = 0.9, hallmark genes > 0  
* DeepVirFinder: dvf_score_min = 0.9, dvf_pvalue_max = 0.05  

---
### Candidate initial QC
Initial virus candidate contigs were then extracted and written to a new fasta file and examined by CheckV:

[CheckV](https://bitbucket.org/berkeleylab/checkv/src/master/)
```
source activate /mnt/storage/envs/checkv
checkv end_to_end $infa $outdir -t 20 -d /mnt/storage/reference_dbs/checkv/checkv-db-v1.5
```


---
### Annotation

[DRAM](https://github.com/WrightonLabCSU/DRAM)

```
source activate /mnt/storage/envs/dram
DRAM-v.py annotate -i ${contig} -o $plateout --min_contig_size 2000
```

---
### Summary Tables
#### *sag_vcontig_summary.csv*

Columns:  
sag: sag ID  
total_vcontig_bp: total length of viral candidates sequences in SAG  
'Complete', 'High-quality', 'Low-quality', 'Medium-quality', 'Not-det ermined’: contig count for checkV quality categories  
'Total vContigs’: total number of viral candidate contigs in SAG  
'dramv_top_vir_genome_match’: viral genome from NCBI with the most hits to vCandidate open reading frames (orfs)  
'dramv_top_vir_pct_matched’: percent orfs matching the top hit virus  
'total_vc_orfs’: total number of orfs on virus candidate contigs   
'total_annotated_vc_orfs’: total vCandidate orfs that were annotated by DRAMv  
'pct_annotated’: percent annotated orfs on virus candidates  
'phage structurals’: list of identified viral structural genes on vCandidates  
'plate_id’: SAG plate ID  
'integrase_count’: total number of integrases on vCandidates within SAG  

#### *vcandidate_contig_summary_stats.csv*  

Columns:  
'contig’: contig ID  
'sag’: SAG id  
'plate’: plate id  
**from virsorter2**  
'type_vs2’: virsorter2 type (integrated or individual)  
'vs2_pos’: 1 if virsorter identified  
**from vibrant**  
'type_vib’: VIBRANT type (X or Y)  
'vib_pos’: 1 if VIBRANT identified  
**from deepvirfinder**. 
'dvf_pos’: 1 if DeepVirFinder identified  
**added columns**  
'consensus_score’: count of how many virus finders identified as viral (max 3)  
'contig_length’: contig length in bp  
**from checkV**. 
'provirus’: 'Yes' if checkv identified as integrated  
'gene_count’: total genes on contig  
'viral_genes’: total viral genes on contig (checkV determined)  
'host_genes’: total host genes on contig (checkV determined)  
'pct_host_genes’: percent genes on contig identified as ‘host’ by checkV  
'checkv_quality’: checkV determined viral genome quality  
'miuvig_quality’: another genome quality metric  
'completeness': checkv estimated viral contig completeness  
'completeness_method': method checkv used to estimate completeness  

#### *per-SAG ORF annotation tables*
'orfid': DRAM-v open reading frame ID  
'fasta': Originating fasta file  
'scaffold': Scaffold/contig ID  
'gene_position': Gene position on contig  
'start_position': Gene start position on contig  
'end_position': Gene end position  
'strandedness': gene strandedness  
'rank': strength of the metabolic annotation -- see (here)[https://github.com/WrightonLabCSU/DRAM/wiki/4a.-Interpreting-the-Results-of-DRAM#the-annotations-master-table]  
Blast style database searches:  
'viral_id'  
'viral_hit'  
'viral_RBH'  
'viral_identity'  
'viral_bitScore'  
'viral_eVal'  
'peptidase_id'  
'peptidase_family'  
'peptidase_hit'  
'peptidase_RBH'  
'peptidase_identity'  
'peptidase_bitScore'   
'peptidase_eVal'  
hmm style searches: 
'kegg_id'  
'kegg_hit'  
'pfam_hits'  
'cazy_hits'  
'vogdb_description'   
'vogdb_categories'  
'heme_regulatory_motif_count'  
'vogdb_hits'  
'is_transposon'  
'amg_flags'  
**added columns**  
'sag': sag id  
'vogdb_text': extracted vogdb text description  
'vog_db_vir_protein': identified as a viral protein by columns 'vogdb_categories'  
'viral_hit_genome': extracted genome description for the viral hit  
'viral_hit_gene': extracted gene text for the viral hit  
'viral_hit_gene_desc': extracted gene description for the viral hit  
'is_integrase': identified as integrase in any annotation column (1 if integrase, 0 if not)  
'annotation': summarized gene annotation from all database comparison results  
'annotation_source': column that annotation was extracted from   
