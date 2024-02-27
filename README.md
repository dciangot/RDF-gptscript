# RDF-gptscript

[gptscript](https://github.com/gptscript-ai/gptscript) module to do analysis using RDataFrame 

At current time, this implementation only works with an OPENAI key. 

## Usage

Install ROOT:

```bash
conda install -c conda-forge ROOT
```

Execute the demo over CERN opendata:

```bash
export OPENAI_API_KEY=XXXXXXXXX
gptscript demo.gpt
```

## Demo content

```text
tools: sys.exec, sys.write, ./tools/init_rdf.gpt, ./tools/load_df.gpt, ./tools/4mu_system.gpt, ./tools/4mu_Z_filter.gpt, ./tools/plot_object_1D.gpt 


write a python script script.py that contains the following blocks. each line is a tool to be matched separately, one after the other:

code to import ROOT and enable multithreading, then import default headers for higgs analysis from this path: /Users/dciangot/opt/miniconda3/tutorials/dataframe/df103_NanoAODHiggsAnalysis_python.h 

code to load branch Events from file root://eospublic.cern.ch//eos/root-eos/cms_opendata_2012_nanoaod_skimmed/SMHiggsToZZTo4L.root into an RDataFrame named higgs_df

code to select events with 4 good muons candidates for Z boson in the higgs_df dataframe

code to draw an Histo1D of Muon_pt in higgs_df with 100 bins, starting from 0 to 200. Then store the image in plot.png

execute script.py
```
