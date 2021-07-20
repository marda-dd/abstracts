# Generating the dataset

Note: the dataset requires a corpus of abstracts as input. This corpus is not included here due
to copyright concerns.

Get [lbnlp](https://github.com/lbnlp/lbnlp/) working with the `matscholar_2020v1` package's `ner`
model as follows:

```bash
# in shell
git clone https://github.com/lbnlp/lbnlp/
cd lbnlp
# python 3.6 or 3.7 needed for tensorflow==1.15.0
# python 3.6 classifier in lbnlp/setup.py, so using 3.6
conda create -n lbnlp python=3.6
conda activate lbnlp
pip install -e .[ner]
pip install jupyter
python -m ipykernel install --name lbnlp
jupyter notebook
# open this repo's `lbnlp_matscholar_marda_abstracts.ipynb` notebook, set to use lbnlp kernel
```

Followed instructions at <https://lbnlp.github.io/lbnlp/pretrained/>.

The `ner_model = load("ner")` part took a few minutes, and there were many tensorflow deprecation
warnings, but no module requirement errors.

When running `ner_model.tag_doc(doc)`, there was a helpful error message suggesting to run `cde data
download` in a shell. Ran this in the notebook as `!cde data download`. Tagging then worked.

The tags use the so-called ["inside-outside-beginning" tagging
scheme](https://en.m.wikipedia.org/wiki/Inside%E2%80%93outside%E2%80%93beginning_(tagging)) for
multi-token entities. B- means beginning, I- means inside, O means outside. So, for example, B-MAT
followed by I-MAT means it’s a 2-token MAT entity.

The notebook produces gzipped JSON-Lines files (one JSON document per line) for each of abstracts,
sentences, and taggings, including taggings normalized according to the published normalizations:

- [abstracts](https://n2t.net/ark:57802/md1snr3c886): (doi: str, n_sents: int) # DOI, number of sentences
- [sentences](https://n2t.net/ark:57802/md1rc4y7a15): (doi: str, idx_s: int, raw_s: str) # DOI, index of sentence in abstract (0-based), “raw” sentence (imperfect pdf->txt conversion)
- [taggings](https://n2t.net/ark:57802/md19sjz1c79): (doi: str, idx_s: int, ne: str, cat: str) # DOI, index of sentence in abstract (0-based), named entity, named-entity category (from matscholar)
- [taggings_normalized](https://n2t.net/ark:57802/md1nytzhs82) - (doi: str, idx_s: int, ne: str, cat: str) # DOI, index of sentence in abstract (0-based), named entity (normalized via published normalizations), named-entity category (from matscholar)

The notebook also produces the taggings (not normalized) in a single table (though "raw" sentence
values are provided only one to conserve space) as a zipped CSV table
[here](https://n2t.net/ark:57802/md122y5mw44).
