# Primerize

<img src="https://primerize.stanford.edu/site_media/images/logo_primerize.png" alt="Primerize Logo" width="200" align="right">

**Primerize** is a Python package for PCR assembly primer design, developed by the [Das Lab](https://daslab.stanford.edu/) at Stanford University for high-throughput RNA synthesis.

The algorithm designs *forward* (sense strand) and *reverse* (anti-sense strand) primers that minimize the total length, and therefore the total synthesis cost, of the oligonucleotides.

| | |
|---|---|
| **Website** | https://primerize.stanford.edu/ |
| **Protocol** | https://primerize.stanford.edu/protocol/ |
| **Documentation** | https://ribokit.github.io/Primerize/ |
| **Site repo** | https://github.com/DasLab/primerize.github.io |

> **Note:** The interactive Primerize web server was decommissioned in May 2026. Use this Python package directly — see [Usage](#usage) below, or try the [Claude Code prompt](#use-with-claude-code-or-ai-assistants).

## Installation

```bash
pip install git+https://github.com/ribokit/Primerize.git
```

Or from a local clone:
```bash
git clone https://github.com/ribokit/Primerize.git
cd Primerize
pip install .
```

**Dependencies** (installed automatically via pip):
```
matplotlib >= 1.5.0
numpy >= 1.10.1
xlwt >= 1.0.0
```

#### Loop Optimization with `numba` _(Optional)_

To speed up **Primerize** code, we take advantage of the [`@jit`](http://numba.pydata.org/numba-doc/0.23.1/user/jit.html) decorator of [`numba`](http://numba.pydata.org/) on loop optimization. **This is totally optional.** Enabling such feature may speed up the run for up to _10x_.

#### Test

To test if **Primerize** is functioning properly, run the unit tests:

```bash
cd path/to/Primerize/tests/
python -m unittest discover
```

All 42 test cases should pass.

## Use with Claude Code or AI Assistants

Paste this prompt into [Claude Code](https://claude.ai/code) or another AI coding assistant to design primers for your sequence:

```
Help me design PCR assembly primers for my RNA construct using the Primerize
Python package (https://github.com/ribokit/Primerize).

1. Install: pip install git+https://github.com/ribokit/Primerize.git
2. Run:
     import primerize
     sequence = "PASTE_YOUR_SEQUENCE_HERE"  # RNA or DNA, any case
     result = primerize.Primerize_1D.design(sequence)
     if result.is_success:
         print(result)
         result.save()  # writes primer file with sequences for IDT ordering

My sequence is: PASTE_YOUR_SEQUENCE_HERE
```

## Usage

### 1D Primer Design (single construct)

```python
import primerize

sequence = 'TTCTAATACGACTCACTATAGGCCAAAGGCGUCGAGUAGACGCCAACAACGGAAUUGCGGGAAAGGGGUCAACAGCCGUUCAGUACCAAGUCUCAGGGGAAACUUUGAGAUGGCCUUGCAAAGGGUAUGGUAAUAAGCUGACGGACAUGGUCCUAACCACGCAGCCAAGUCCUAAGUCAACAGAUCUUCUGUUGAUAUGGAUGCAGUUCAAAACCAAACCGUCAGCGAGUAGCUGACAAAAAGAAACAACAACAACAAC'

job_1d = primerize.Primerize_1D.design(sequence, MIN_TM=60.0, prefix='P4P6')
if job_1d.is_success:
    print(job_1d)
    job_1d.save()   # writes P4P6.txt with primer sequences for IDT ordering
```

### 2D Mutate-and-Map Library (96-well plates)

```python
job_2d = primerize.Primerize_2D.design(sequence, primer_set=job_1d['primer_set'],
                                        offset=-51, which_lib=1, prefix='P4P6')
if job_2d.is_success:
    print(job_2d)
    job_2d.save()   # writes .xls plate files (for IDT plate ordering) + .svg layout images
```

### 3D Structure-Based Library

```python
structures = ['...........................((((((.....))))))...........((((((..((((((.....(((.((((.(((..(((((((((....)))))))))..((.......))....)))......)))))))....))))))..)).))))((... ((((...(((((((((...)))))))))..))))...)).............((((((.....))))))......................']
job_3d = primerize.Primerize_3D.design(sequence, primer_set=job_1d['primer_set'],
                                        offset=-51, structures=structures,
                                        N_mutations=1, which_lib=1)
if job_3d.is_success:
    print(job_3d)
    job_3d.save()
```

For advanced usage — `get()`, `save()`, `echo()`, custom mutation lists — see the [Documentation](https://ribokit.github.io/Primerize/).

## Documentation

- Full docs: https://ribokit.github.io/Primerize/
- Protocol: https://primerize.stanford.edu/protocol/

## License

Copyright &copy; of **Primerize** _Source Code_ is described in [LICENSE.md](https://github.com/ribokit/Primerize/blob/master/LICENSE.md).

## Reference

>Tian, S., *et al.* (**2015**)<br/>
>[Primerize: Automated Primer Assembly for Transcribing Interesting RNAs.](http://nar.oxfordjournals.org/content/43/W1/W522.full)<br/>
>*Nucleic Acid Research* **43 (W1)**: W522-W526.

>Tian, S., and Das, R. (**2017**)<br/>
>[Primerize-2D: automated primer design for RNA multidimensional chemical mapping.](https://academic.oup.com/bioinformatics/article-abstract/33/9/1405/2801460/Primerize-2D-automated-primer-design-for-RNA)<br/>
>*Bioinformatics* **33 (9)**: 1405-1406.

<hr/>

Developed by **Das Lab**, _Stanford University / HHMI_.
