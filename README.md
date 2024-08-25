## Overview
varpepdb is a python package for generating a fasta database of genetically variant peptides for peptide spectrum matching after data acquisition by LC/MS. It takes in a list of amino acid substitutions for a protein sequence and generates all possible variant peptides after enzymatic cleaving. It allows for multiple digestion enzymes and up to 1 miscleavage. It also takes into account the effect of amino acid substitutions on enzyme cleavage.

## Installation
Tested Python version: >= 3.11.5 <br>
Requires:
* [rpg](https://rapid-peptide-generator.readthedocs.io/en/latest/userguide.html#installation) 


## Usage
```
import varpepdb
import rpg

# Set enzymes to and Asp-N and trypsin
varpepdb.setenzyme([rpg.RapidPeptidesGenerator.ALL_ENZYMES[1], 
                    rpg.RapidPeptidesGenerator.ALL_ENZYMES[41]])
# Allow 1 miscleave
varpepdb.miscleave = True
# Set peptide length limits. Default values are 6 and 30. 
varpepdb.setpeptidelengths(min_length: 6, max_length: 30)

# Demo inputs. In practice, these will be generated programmatically.
variants = ['O75844:p.Trp11Trp', 
            'O75844:p.Ala22Lys', 
            'O75844:p.Glu34Thr', 
            'O75844:p.Gln41His']
sequence = 'MGMWASLDALWEMPAEKRIFGAVLLFSWTVYLWETFLAQRQRRIYKTTTH'
gene = 'ZMPSTE24'

# Generate variant peptides
peptides = generate_single(variants=variants, 
                           sequence=sequence, 
                           gene=gene)

# Removes variant peptides that do not contain at least 1 amino acid substitution
var_peptides=varpepdb.variant_containing_peptides(peptides)

# Write variant peptides into fasta file
varpepdb.write_fasta(path='path/to/output.fasta', 
                     peptides=var_peptides,
                     include_non_unique=True)
```

Multiple proteins can be processed in parallel using `varpepdb.generate`. 

```
peptides = generate(input_list=[(variants1, sequence1, gene1),
                                (variants2, sequence2, gene3),
                                (variants3, sequence2, gene3)])

var_peptides=varpepdb.variant_containing_peptides(peptides)

varpepdb.write_fasta(path='path/to/output.fasta', 
                     peptides=var_peptides,
                     include_non_unique=True)
```
Refer to functions' docstring for more information.

## Fasta output
Example of an entry written into the fasta file:
>\>A0A8I5KQE6-v1 RPSA2 129-143 (p.Pro143Arg),p.Thr135Met 0 <br>
ADHQPLMEASYVNLR

`A0A8I5KQE6-v1` is the sequence identifier of the parent protein (in this case the Uniprot ascension number) with <br>
'v{number}' appended to identify it as a peptide of the parent protein. <br>
`RPSA2` is the name of the gene for this protein. <br>
`129-143` is the position of the parent protein sequence from which this peptide is dervied.
`(p.Pro143Arg)` is an amino acid substitution that affected the enzyme cleavage site. Amino acid substitutions that introduce or remove cleavage sites are marked by parenthesis. <br>
`p.Thr135Met` is an amino acid subtstitution that didn't affect enzyne cleavage site. <br>
`0` refers to the number of miscleavages <br>


## Contact
For further information please contact jaren_sia@htx.gov.sg