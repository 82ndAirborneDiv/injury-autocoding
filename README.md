# Autocoding Injury Narratives with BERT
This repo contains code for training an ensemble of BERT models to autocode injury narratives. 

## Overview

### Task
Building on recent research on autocoding injury narratives, like that in [Measure (2014)](https://www.bls.gov/iif/deep-neural-networks.pdf) and in [Bertke et al. (2016)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4915551/), our task here was to classify free-text injury narratives using [OIICS](https://wwwn.cdc.gov/wisards/oiics/Trees/MultiTree.aspx?Year=2012) event codes. The project was for an internal Kaggle-like competition hosted by [NIOSH](https://www.cdc.gov/niosh/index.htm) at CDC, which you can read more about [here](https://www.cdc.gov/od/science/technology/innovation/innovationfund.htm). In our data, there were 47 classifiable event codes distributed across 7 categories:

  1. Violence and other injuries by persons and animals
  2. Transportation incidents
  3. Fires and explosions
  4. Falls, slips, and trips
  5. Exposure to harmful substances or environments
  6. Contact with objects and equipment
  7. Overexertion and bodily reaction

Events not belonging to one of these categories were marked with with code 99, making for a grand total of 48 event codes. Since each narrative only receives a single code, we formulated the problem as one of multiclass classification.

### Model
BERT stands for Bidirectional Encoder Representations from Transformers. One of the newer large-scale contextual language models, it's a good baseline for a wide variety of downstream NLP tasks. To learn more about how the base model is trained, check out the paper on [arXiv](https://arxiv.org/abs/1810.04805). To see how folks from Google implemented it in TensorFlow, check out the original [repo](https://github.com/google-research/bert) on GitHub, which we've also included here (but not updated in while, so you may want to pull down a fresh copy). 

For this project, we used an ensemble of 4 separate BERTs as our final classifier. To generate predicted codes for the test narratives, we average the predicted probabilities from the 4 models and then take the highest as the winner. We also tried blending and stacking, because [Kaggle](https://mlwave.com/kaggle-ensembling-guide/), but they didn't give us much gain over simple averaging, and so we went with the latter to reduce computational overhead.

### Code
The main scripts are the two ```.bat``` files. To fine-tune the base BERT checkpoint on your own data, train the model with ```train.bat```, and then update the ckeckpoint number in the call to ```bert\run_classifer.py``` in ```test.bat``` to reflect the last checkpoint saved during training. To use our fine-tuned checkpoints to get predictions on your own data, simply run ```test.bat```, leaving the checkpoint number the same. In both cases, you'll want to have run ```src\preprocessing.py``` on your raw text files, which should have the structure outlined in the Data section below. 

### Data
To download a copy of the data directory we reference in our code, including the small base BERT model we fine-tuned to classify the narratives, head [here](https://www.dropbox.com/s/xk343thvl8tt7oh/injury_autocoding.zip?dl=1). Once you've upzipped the file, you'll see a directory with a BERT folder, two CSV files with information about the injury codes, and a few empty folders for holding the individual model checkpoints that go into our final ensemble. The next step is will be to put your raw text files in the new directory so that ```src\preprocessing.py``` has something to work with. Your files should be in ```.csv``` format, with the following names and columns:

  1. ```train.csv```: 'id', 'text', 'event'
  2. ```test.csv```: 'id', 'text'

If your narratives don't already have an 'id' column with unique record identifiers, our script will generate one during the preprocessing steps. Also, if you'd like to use our pre-fine-tuned BERT checkpoints to get our ensemble's predictions, replace the ```train_runs``` folder in the original data directory template with the one [here](https://www.dropbox.com/s/3syexlfa3a6uyfm/train_runs.zip?dl=1). 

### Technical requirements
For software, our Python (3.x) code uses NumPy, scikit-learn, and pandas, so you'll need the latest versions of those installed. You'll also need whatever's required by BERT, like TensorFlow. See the [requirements file](requirements.txt) for more information.

For hardware, we highly recommend having at least one [cuDNN-enabled GPU](https://developer.nvidia.com/cuda-gpus) on your macine. Running inference with our pre-fine-tuned checkpoints should be OK on a CPU unless you have a ton of data, but fine-tuning the base checkpoint on new data might take a super long time without a GPU (we did use an ensemble of 4 separate BERT models, after all). 

## Public Domain Standard Notice
This repository constitutes a work of the United States Government and is not
subject to domestic copyright protection under 17 USC § 105. This repository is in
the public domain within the United States, and copyright and related rights in
the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
All contributions to this repository will be released under the CC0 dedication. By
submitting a pull request you are agreeing to comply with this waiver of
copyright interest.

## License Standard Notice
The repository utilizes code licensed under the terms of the Apache Software
License and therefore is licensed under ASL v2 or later.

This source code in this repository is free: you can redistribute it and/or modify it under
the terms of the Apache Software License version 2, or (at your option) any
later version.

This source code in this repository is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE. See the Apache Software License for more details.

You should have received a copy of the Apache Software License along with this
program. If not, see http://www.apache.org/licenses/LICENSE-2.0.html

The source code forked from other open source projects will inherit its license.

## Privacy Standard Notice
This repository contains only non-sensitive, publicly available data and
information. All material and community participation is covered by the
[Disclaimer](https://github.com/CDCgov/template/blob/master/DISCLAIMER.md)
and [Code of Conduct](https://github.com/CDCgov/template/blob/master/code-of-conduct.md).
For more information about CDC's privacy policy, please visit [http://www.cdc.gov/privacy.html](http://www.cdc.gov/privacy.html).

## Contributing Standard Notice
Anyone is encouraged to contribute to the repository by [forking](https://help.github.com/articles/fork-a-repo)
and submitting a pull request. (If you are new to GitHub, you might start with a
[basic tutorial](https://help.github.com/articles/set-up-git).) By contributing
to this project, you grant a world-wide, royalty-free, perpetual, irrevocable,
non-exclusive, transferable license to all users under the terms of the
[Apache Software License v2](http://www.apache.org/licenses/LICENSE-2.0.html) or
later.

All comments, messages, pull requests, and other submissions received through
CDC including this GitHub page are subject to the [Presidential Records Act](http://www.archives.gov/about/laws/presidential-records.html)
and may be archived. Learn more at [http://www.cdc.gov/other/privacy.html](http://www.cdc.gov/other/privacy.html).

## Records Management Standard Notice
This repository is not a source of government records, but is a copy to increase
collaboration and collaborative potential. All government records will be
published through the [CDC web site](http://www.cdc.gov).

## Additional Standard Notices
Please refer to [CDC's Template Repository](https://github.com/CDCgov/template)
for more information about [contributing to this repository](https://github.com/CDCgov/template/blob/master/CONTRIBUTING.md),
[public domain notices and disclaimers](https://github.com/CDCgov/template/blob/master/DISCLAIMER.md),
and [code of conduct](https://github.com/CDCgov/template/blob/master/code-of-conduct.md).
