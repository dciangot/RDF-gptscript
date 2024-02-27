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
tools: sys.exec, sys.write, init_rdf, load_df, 4mu_system, 4mu_Z_filter, plot_object_1D


write a python script script.py that contains the following blocks. each line is a tool to be matched separately, one after the other:

code to import ROOT and enable multithreading, then import default headers for higgs analysis from this path: /Users/dciangot/opt/miniconda3/tutorials/dataframe/df103_NanoAODHiggsAnalysis_python.h 

code to load branch Events from file root://eospublic.cern.ch//eos/root-eos/cms_opendata_2012_nanoaod_skimmed/SMHiggsToZZTo4L.root into an RDataFrame named higgs_df

code to selectplot an Histo1D histogram of ${object} with ${n_bins} bins and range spannin from ${min} to ${max}. Store the image in ${filepath}

code to draw an Histo1D of muons pt with 100 bins, starting from 0 to 200. Then store the image in /tmp/plot.png

execute script.py

---
name: init_rdf
description: import ROOT and enable multithreading, then import default headers for higgs analysis
argument: higgs_header_path: the path to the higgs headers


import ROOT
import os
# Enable multi-threading
ROOT.ROOT.EnableImplicitMT()
ROOT.gInterpreter.Declare('#include ${higgs_header_path}')
---
name: load_df
description: python load branch ${branch} for file ${data} into and RDataFrame, named ${df}
argument: df: the name of the dataframe
argument: data: the URL to the file to be loaded
argument: branch: the ROOT branch to be loaded


# Create a ROOT dataframe for each dataset
df = ROOT.RDataFrame('${branch}', '${data}')
---
name: 4mu_system
description: calculate the invariant mass of the 4 muons ZZ system for dataframe ${df} and store it into a column named ${name}
argument: df: name of the dataframe
argument: name: the name of the new column
argument: function: the header function name

${df} = ${df}.Define("${name}", "${function}(Muon_pt, Muon_eta, Muon_phi, Muon_mass, Muon_charge)")

---
name: 4mu_Z_filter
description: select events from dataframe ${df} with 4 good muons candidates for Z boson
argument: df: name of the dataframe

df_ge4m = ${df}.Filter("nMuon>=4", "At least four muons")

df_iso = df_ge4m.Filter("All(abs(Muon_pfRelIso04_all)<0.40)", "Require good isolation")
df_kin = df_iso.Filter("All(Muon_pt>5) && All(abs(Muon_eta)<2.4)", "Good muon kinematics")
df_ip3d = df_kin.Define("Muon_ip3d", "sqrt(Muon_dxy*Muon_dxy + Muon_dz*Muon_dz)")
df_sip3d = df_ip3d.Define("Muon_sip3d", "Muon_ip3d/sqrt(Muon_dxyErr*Muon_dxyErr + Muon_dzErr*Muon_dzErr)")
df_pv = df_sip3d.Filter("All(Muon_sip3d<4) && All(abs(Muon_dxy)<0.5) && All(abs(Muon_dz)<1.0)",
                        "Track close to primary vertex with small uncertainty")
${df} = df_pv.Filter("nMuon==4 && Sum(Muon_charge==1)==2 && Sum(Muon_charge==-1)==2",
                       "Two positive and two negative muons")

---
name: plot_object_1D
description: plot an Histo1D histogram of ${object} with ${n_bins} bins and range spanning from ${min} to ${max}. Store the image in ${filepath}
argument: object: the name of the column in the RDataFrame datafram
argument: n_bins: number of bins
argument: min: the minimum x axis value
argument: max: the maximum x axis value

h = higgs_df.Histo1D(("Muon_pt", "" , 10, 0, 300), "Muon_pt")
c = ROOT.TCanvas()
h.Draw()
c.SaveAs("/tmp/plot.png")
```
