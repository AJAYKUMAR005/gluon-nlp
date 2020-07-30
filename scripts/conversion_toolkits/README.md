# Conversion Scripts

In GluonNLP, we provide shared scripts to convert the model checkpoints in other repositories to GluonNLP.  

At this stage, the model needs to be downloaded locally, and the converting scripts accepts only the file directory as the argument,
without the support of accepting the url. In addition, both the tensorflow fine-tuned models that
can be loaded in TF1 Hub modules and TF2 SavedModels are accepted, although the parameters of mask
language model are not provided in TF2 SavedModels in most cases, and
the differences of these parameters are not required to be tested after converting.

The testing step mentioned above are controlled by the flag `--test`, in which the maximum
tolerance of 1e-3 between gluon model with converted weights and original tensorflow model.
In addition, we can use GPU in all converting scripts by adding `--gpu 0`.

For RoBERTa XLM-R and BART model, please instal the [fairseq](https://github.com/pytorch/fairseq#requirements-and-installation) package locally as `pip install git+https://github.com/pytorch/fairseq.git@master`.

## BERT
Convert model from [BERT LIST](https://tfhub.dev/google/collections/bert/1).

You can use the script provided in [convert_bert_from_tf_hub.sh](convert_bert_from_tf_hub.sh).
The following command give you a rough idea about the code.

```bash
bash convert_bert_from_tf_hub.sh
```

In the process, we downloaded the config file from the [official repo](https://github.com/google-research/bert#pre-trained-models), download the configuration file `bert_config.json`,
and move it into `${case}_bert_${model}/assets/`.

## ALBERT

```bash
for model in base large xlarge xxlarge
do
    mkdir albert_${model}_v2
    wget "https://tfhub.dev/google/albert_${model}/3?tf-hub-format=compressed" -O "albert_${model}_v3.tar.gz"
    tar -xvf albert_${model}_v3.tar.gz --directory albert_${model}_v2
    python convert_tf_hub_model.py --tf_hub_model_path albert_${model}_v2 --model_type albert --test
done
```

## ELECTRA
The TF Hub is not available for ELECTRA model currently.
Thus, you will need to clone the [electra repository](https://github.com/ZheyuYe/electra)
and download the checkpoint. The parameters are converted from local checkpoints.
By running the following command, you can convert + verify the ELECTRA model with both the discriminator and the generator.

Notice: pleas set up the `--electra_path` with the cloned path ~~or get this electra repository packaged by `pip install -e .`.~~

```bash
# Need to use TF 1.13.2 to use contrib layer
pip uninstall tensorflow
pip install tensorflow==1.13.2

# Actual conversion
bash convert_electra.sh
```

## Mobile Bert
```bash
bash convert_mobilebert.sh
```

## RoBERTa
```bash
for model in base large
do
    mkdir roberta_${model}
    wget "https://dl.fbaipublicfiles.com/fairseq/models/roberta.${model}.tar.gz"
    tar zxf roberta.${model}.tar.gz --directory roberta_${model}
    python convert_fairseq_roberta.py --fairseq_model_path roberta_${model}/roberta.${model} --test
done
```

## XLM-R

```bash
for model in base large
do
    mkdir xlmr_${model}
    wget "https://dl.fbaipublicfiles.com/fairseq/models/xlmr.${model}.tar.gz"
    tar zxf xlmr.${model}.tar.gz --directory xlmr_${model}
    python convert_fairseq_xlmr.py --fairseq_model_path xlmr_${model}/xlmr.${model} --model_size ${model} --test
done
```

## BART
```bash
for model in base large
do  
    mkdir bart_${model}
    wget  "https://dl.fbaipublicfiles.com/fairseq/models/bart.${model}.tar.gz"
    tar zxf bart.${model}.tar.gz --directory bart_${model}
    python convert_fairseq_bart.py --fairseq_model_path bart_${model}/bart.${model} --test
done
```