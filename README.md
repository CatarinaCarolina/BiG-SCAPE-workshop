# BiG-SCAPE-workshop

Expanded tutorial materials created for hands-on session of Genome Mining Workshops.


### Installing BiG-SCAPE 2.0

[Install BiG-SCAPE](https://github.com/medema-group/BiG-SCAPE/wiki/01.-Installing-and-Running-BiG-SCAPE) and download (and unzip) this [dataset](https://zenodo.org/records/14446826).

At the end of setup, you should have:
- Installed BiG-SCAPE 2.0 and be able to run it from anywhere by running the command `bigscape`. The output should look something like this:
```
(bigscape) drais008@~/src/BiG-SCAPE dev$ bigscape
Usage: bigscape [OPTIONS] COMMAND [ARGS]...

  BiG-SCAPE

  Biosynthetic Gene Similarity Clustering and Prospecting Engine.

  BiG-SCAPE can be run in three modes: cluster, query, and benchmark.
  See the help menu for each mode for more information.
  For a more comprehensive help menu and tutorials see GitHub Wiki.

Options:
  --version
  -h, --help  Show this message and exit.

Commands:
  cluster    BiG-SCAPE - CLUSTER
  query      BiG-SCAPE - QUERY
  benchmark  BiG-SCAPE - BENCHMARK
```
- Unzipped the Pfam-A models somewhere and remembered its location
- Unzipped the example data somewhere and remembered its location

#### Running BiG-SCAPE 2

Now lets actually run BiG-SCAPE 2. The first command should take approximately 1 minute, and will let you explore both a mix bin, where all BGC records are compared to each other in a pairwise manner, as well as antiSMASH product category based bins, where BGC records are grouped by their respective categories.

In the following command, replace JK1_tutorial with the location of your example data, and pfam/Pfam-A.hmm with the location of your extracted PFAM models.
You can specify any location you wish for the `-o JK1_tutorial_out` parameter. This specifies in what directory the output data will end up.

```
bigscape cluster -i JK1_tutorial/ -o JK1_tutorial_out -p pfam/Pfam-A.hmm --mix
```

#### Exploring results

Once this is done running, open the index.html in your output folder. You will be prompted to select a database to read from.

Select the .db file inside your output folder. It should have the same name as your output folder, so in the example command above it should be `JK1_tutorial_out.db`.

When you do so, you will be presented with an overview page of the results of this run. This page contains some statistics on the run itself and the networks that resulted from the run.

To explore the networks generated in the run, click on the "network" button at the top-left of the page.

This section of the results will show a list of connected components on the left side. Clicking on any one of them will visualize these in a graph individually.

Alternatively, you can click "vizualize all" to visualize all the connected components in one graph. Note that if the graph is too large, it may not be able to display on your machine.

[This section](https://github.com/medema-group/BiG-SCAPE/wiki/02.-BiG-SCAPE-Workflows#output-interactive-visualization) contains an overview of all the exploration options that are available.

#### Setting cutoff values

Now let's add a few higher distance cutoffs, and see how the GCF architectures might change.

```
bigscape cluster -i JK1_tutorial/ -o JK1_tutorial_out -p pfam/Pfam-A.hmm --mix --gcf-cutoffs 0.5,0.8
```

Re-open the index.html file inside the output folder. Or, if you still have the original output open, refresh the page.

You will be prompted to open a database again. Do so and you will be shown the results of your latest BiG-SCAPE run under one of the selected cutoffs.

Use the drop-down menu in the top-right to select a different cutoff. Run labels in this dropdown menu are suffixed with the selected cutoff (e.g. `_c0.5` for a cutoff of 0.5)

#### Selecting record types

With the next command you will re-run the same dataset, but this time using the `protocluster` [record type](https://github.com/medema-group/BiG-SCAPE/wiki/05.-antiSMASH-Record-Types), instead of the default `region`.

This mode compares pairs BGCs on a protocluster level, instead of a region level. Since regions may have multiple protoclusters, this can produce some interesting results where two protoclusters from the same region end up in different connected components.

Such pairs are still linked in the graph view by a "topological link", indicating that they share the same region.

Try finding the GCFs that are linked by topological links. (Hint: you need to search in the `mix` bin). To help us find this run quicker in the UI’s Run dropdown, we will also add a label.

```
bigscape cluster -i JK1_tutorial/ -o JK1_tutorial_out -p /pfam/Pfam-A.hmm --mix --record-type protocluster --label protocluster
```

### BiG-SCAPE Query

With `bigscape query` you can provide BiG-SCAPE with a query BGC record, and use BiG-SCAPE to find all other records that share similarity to the query BGC. For this tutorial, we have just selected a random `.gbk` record from the same dataset we are already using.

`bigscape query` will collectively see all other input and reference (user defined and/or MIBiG ) `.gbk` records as references, so you don’t need to worry about restructuring your file system.

Try running the following query command, and explore the output. Can you find your query node? (Hint: its border is highlighted).

```
bigscape query -i JK1_tutorial/ -o JK1_tutorial_out -p pfam/Pfam-A.hmm --query-bgc-path JK1_tutorial/Other_records/JCM_4504.region30.gbk
```

The previous `bigscape query` run will only calculate distances between the query record, and all other records. With the `--propagate` flag, BiG-SCAPE 2 will not only make this first set of comparisons, but will follow this by an iterative set of reference-vs-reference comparisons which will effectively ‘propagate’ the connected component until no more edges are created. Give the command below a try and see if you can spot the differences.

```
bigscape query -i JK1_tutorial/ -o JK1_tutorial_out -p pfam/Pfam-A.hmm --query-bgc-path JK1_tutorial/Other_records/JCM_4504.region30.gbk --propagate
```

Finally, `bigscape query` can also be used with any specific [record type](https://github.com/medema-group/BiG-SCAPE/wiki/05.-antiSMASH-Record-Types). In the case that we select a record type other than `region`, we must also 

```
query -i JK1_tutorial/ -o JK1_tutorial_out -p pfam/Pfam-A.hmm --query-bgc-path JK1_tutorial/Other_records/JCM_4504.region30.gbk --record-type protocluster --query-record-number 2
```

If you still have time, there is a more advanced usage of this mode where it is used to query which of the input BGCs are similar to a MIBiG BGC.

In order to use this, you will need to download a GBK file corresponding to the MIBiG entry you want to query. These files can be found [here](https://dl.secondarymetabolites.org/mibig/).
Find or generate an antiSMASH processed GBK of the MIBiG entry with ID BGC0000664, then use this GBK in the `--query-bgc-path` parameter.


```
query -i JK1_tutorial/ -o JK1_tutorial_out -p pfam/Pfam-A.hmm --query-bgc-path path_to_gbk/your_gbk_here.gbk
```

### BiG-SCAPE Benchmark

`bigscape benchmark` is designed for checking how well BiG-SCAPE groups BGCs into families, provided you, the user, has a curated/predefined set of BGC -> GCF assignments. Furthermore, `bigscape benchmark` has only been developed to work with a `bigscape cluster` `mix` mode run, in which the input BGC records are compared in an all-vs-all manner. So let’s first re-run `bigscape cluster`.

```
bigscape cluster -i JK1_tutorial/ -o JK1_tutorial_out_mix -p pfam/Pfam-A.hmm --mix --classify none
```

In the tutorial folder, we have already provided you with a random subset of GCF assignments, you will use these to run `bigscape benchmark`. Have a look at the [output description](https://github.com/medema-group/BiG-SCAPE/wiki/02.-BiG-SCAPE-Workflows) and explore the output.

```
bigscape benchmark --BiG-dir JK1_tutorial_out_mix -o JK1_tutorial_benchmark_out --GCF-assignment-file JK1_tutorial/JK1_GCF_assigmnents.tsv
```

Also explore if running `bigscape cluster` with any other settings (cutoffs, alignment or extend modes, etc) will give you better or worse benchmark scores.

### BiG-SCAPE SQLite DB 

If you have made it to this part of the tutorial, you might have noticed that some runs took longer than other runs. This is due to the fact that BiG-SCAPE 2 makes use of an SQLite database and can re-use already processed files and calculated distances. This also means that to get full access to BiG-SCAPE 2’s output data, interacting with this SQLite DB becomes paramount.

To aid with this process, we have compiled a small list of SQL queries that we have found useful in the past. In any case, if you are completely new to SQL, we advise doing some SQL specific tutorials first.

We assume that you have a DB browser already installed (here is an [example](https://sqlitebrowser.org/)), and are exploring any of the DBs generated in the tutorials above.

#### Selecting all records from a (list of) families

From the JK1_tutorial_out_mix.db, lets pick families FAM_00022 (id: 22) and FAM_00021 (id: 21). Run the command and check if the selected records are what you expected.

```
SELECT gbk.path, bgc.record_type, bgc.record_number, bgc.product, bgc.category, fam.family_id
FROM gbk
INNER JOIN bgc_record AS bgc ON bgc.gbk_id==gbk.id
INNER JOIN bgc_record_family as fam ON bgc.id==fam.record_id
WHERE fam.family_id IN (22,21)
```

#### Selecting a complete distance set of distances below a certain cutoff

```
SELECT gbk1.path, bgc1.record_type, bgc1.record_number, gbk2.path, bgc2.record_type, bgc2.record_number, distance jaccard, adjacency, dss, edge_param_id, lcs_a_start, lcs_a_stop, lcs_b_start, lcs_b_stop, ext_a_start, ext_a_stop, ext_b_start, ext_b_stop, reverse, lcs_domain_a_start, lcs_domain_a_stop, lcs_domain_b_start, lcs_domain_b_stop, params.weights, params.alignment_mode, params.extend_strategy
FROM distance
INNER JOIN bgc_record AS bgc1 ON bgc1.id==distance.record_a_id
INNER JOIN bgc_record AS bgc2 ON bgc2.id==distance.record_b_id
INNER JOIN gbk AS gbk1 ON gbk1.id==bgc1.gbk_id
INNER JOIN gbk AS gbk2 ON gbk2.id==bgc2.gbk_id
INNER JOIN edge_params AS params ON distance.edge_param_id==params.id
WHERE distance.distance<0.5
ORDER BY distance.distance
```

#### Investigate the effect of run parameters on pair comparable region and distance

```
SELECT gbk1.path, bgc1.record_type, bgc1.record_number, gbk2.path, bgc2.record_type, bgc2.record_number, distance, jaccard, adjacency, dss, edge_param_id, lcs_a_start, lcs_a_stop, lcs_b_start, lcs_b_stop, ext_a_start, ext_a_stop, ext_b_start, ext_b_stop, reverse, lcs_domain_a_start, lcs_domain_a_stop, lcs_domain_b_start, lcs_domain_b_stop, params.weights, params.alignment_mode, params.extend_strategy
FROM distance
INNER JOIN bgc_record AS bgc1 ON bgc1.id==distance.record_a_id
INNER JOIN bgc_record AS bgc2 ON bgc2.id==distance.record_b_id
INNER JOIN gbk AS gbk1 ON gbk1.id==bgc1.gbk_id
INNER JOIN gbk AS gbk2 ON gbk2.id==bgc2.gbk_id
INNER JOIN edge_params AS params ON distance.edge_param_id==params.id
ORDER BY bgc1.id, bgc2.id, edge_param_id
```

_If you would like to only see pairs that include one or more specific bgc records, or specific distance thresholds, you can play with the WHERE clauses, such as:_

```
WHERE distance.distance<0.9
AND gbk1.path LIKE '%AC-40.region14%'
```

#### Select pairs that have unique distances across diverging run parameters

```
SELECT distance.record_a_id, gbk1.path, bgc1.product, distance.record_b_id, gbk2.path, distance.distance, distance.edge_param_id, distance.ext_a_start, distance.ext_a_stop, distance.ext_b_start, distance.ext_b_stop 
FROM distance
INNER JOIN bgc_record AS bgc1 ON distance.record_a_id==bgc1.id
INNER JOIN gbk AS gbk1 ON gbk1.id==bgc1.gbk_id
INNER JOIN bgc_record AS bgc2 ON distance.record_b_id==bgc2.id
INNER JOIN gbk AS gbk2 ON gbk2.id==bgc2.gbk_id
GROUP BY distance.record_a_id, distance.record_b_id, distance.distance
HAVING COUNT(*)==1
ORDER BY distance.record_a_id, distance.record_b_id, edge_param_id
```

#### Selecting a complete distance between two BGC records

```
SELECT gbk1.path, bgc1.record_type, bgc1.record_number, gbk2.path, bgc2.record_type, bgc2.record_number, distance, jaccard, adjacency, dss, edge_param_id, lcs_a_start, lcs_a_stop, lcs_b_start, lcs_b_stop, ext_a_start, ext_a_stop, ext_b_start, ext_b_stop, reverse, lcs_domain_a_start, lcs_domain_a_stop, lcs_domain_b_start, lcs_domain_b_stop
FROM distance
INNER JOIN bgc_record AS bgc1 ON bgc1.id==distance.record_a_id
INNER JOIN bgc_record AS bgc2 ON bgc2.id==distance.record_b_id
INNER JOIN gbk AS gbk1 ON gbk1.id==bgc1.gbk_id
INNER JOIN gbk AS gbk2 ON gbk2.id==bgc2.gbk_id
WHERE gbk1.path LIKE '%NS1.region14%'
AND gbk2.path LIKE '%JCM_4504.region30%'
```

_If you would like to also specify the record type and number, add the section below to the query above._

```
AND bgc1.record_type == 'protocluster'
AND bgc2.record_type == 'protocluster'
AND bgc1.record_number== '2'
AND bgc2.record_number== '2'
```

### Loading BiG-SCAPE output into Cytoscape

Let’s do one more run, this time making sure to include singleton nodes in the output visualization.

```
bigscape cluster -i JK1_tutorial/ -o JK1_tutorial_out_mix/ -p /pfam/Pfam-A.hmm --mix --classify none --include-singletons
```

To visualize the entire network, with its singletons, toggle `Visualize all` in the Network section of the [output visualization](https://github.com/medema-group/BiG-SCAPE/wiki/02.-BiG-SCAPE-Workflows#output-interactive-visualization).

To obtain a similar network in Cytoscape, follow these steps:

1. Import the `.network` file as a network `File>Import>Network from File…` and adjust the Column types of the following columns:

	- Record_a: Source Node
	- GBK_a: Source Node Attribute
	- Record_Type_a: Source Node Attribute
	- Record_Number_a: Source Node Attribute
	- ORF_coords_a: Source Node Attribute

	- Record_b: Target Node
	- GBK_b: Target Node Attribute
	- Record_Type_b: Target Node Attribute
	- Record_Number_b: Target Node Attribute
	- ORF_coords_b: Target Node Attribute

	- All remaining columns: Edge Attribute

2. To include singletons and family information, import the `clustering_cutoff.tsv` file as a network, in the same network collection.
Adjust the Column types of the following columns:
	- Record: Source Node
	- All other columns: Source Node Attribute

3. Select both created networks and use `Tools>Merge` to union-merge them, which will create a third, merged network.

4. (optional) Import the `record_annotations.tsv` as a node table. Now you have all attributes from this file available to use for filtering, coloring, etc.

5. (optional) If topological links are present, they can be loaded from the `topolinks.network` tsv file. This file is an edge list in which nodes are records and edges are topological links. Similarly to before:

	- Import the `topolinks.network`as a network. Follow source and target node considerations as above. Do not change the 'Type' annotation.
	- Select the topolinks network as well as the previously created merged network, and union-merge these two.
	- Select the newly merged network.
	- To distinguish topolinks, go to `Style` in the left sidebar, and select `edge` at the bottom tab.
	- In `Line Type`, change the `column` to 'interaction', `mapping` type to 'discrete mapping', and `topology` to 'dash', and make sure `interacts with` is set to 'solid'. 
