---
title: "Transfer Defect Learning in Large Industrial Projects"
bibliography: config/library.bib
abstract:  ""
author: 
- name: Mathieu Nayrolles
  affiliation: La Forge Research Lab, Ubisoft
  location: Montréal, QC, Canada
  email: mathieu.nayrolles@ubisoft.com
- name: Fàbio Petrillo
  affiliation: ECE Department, Concordia University
  location: Montréal, QC, Canada
  email: fabio.petrillo@concordia.ca
- name: Abdelwahab Hamou-Lhadj
  affiliation: ECE Department, Concordia University
  location: Montréal, QC, Canada
  email: wahab.hamou-lhadj@concordia.ca
- name: Yann-Gaël Guéhéneuc
  affiliation: CS Department, Concordia University
  location: Montréal, QC, Canada
  email: yann-gael.gueheneuc@concordia.ca
csl: config/acm-sig-proceedings.csl
keyword: 
- Bug Prediction
- Risky Software Commits
- Transfert Defect Learning
- Software Maintenance
---

\section{Introduction}\label{sec:introduction}

# Related Work {#sec:relwork}

[Related work are for defect prediction, we have to spin it towards transfer defect learning]

## File, Module and Risky Change Prediction

Existing studies for predicting risky changes within a repository rely mainly on code and process metrics. As discussed in the introduction section, Rosen et al. [@Rosen2015]  developed  Commit-guru a tool that relies on building  models from historical commits using code and process metrics (e.g., code complexity, the experience of the developers, etc.) as the main features.  There exist other studies that leverage several code metric suites such as the CK metrics suite [@Chidamber1994] or the Briand's coupling metrics [@Briand1999a]. These metrics have been used, with success, to predict defects  as shown by Subramanyam *et al.* [@Subramanyam2003] and Gyimothy *et al.* [@Gyimothy2005].

Further improvements to these metrics have been proposed by Nagappan *et al.* [@Nagappan2005; @Nagappan2006]  and  Zimmerman *et al.* [@Zimmermann2007; @Zimmermann2008] who used  call graphs as the main artifact for computing code metrics with a static analyzer.

Nagappan *et al.* et proposed a technique that uses data mined from source code repository such as churns to assess the quality of a change [@Nagappan]. Hassan *et al* and Ostrand *et al* used past changes and defects to predict buggy locations  [@Hassan2005], [@Ostrand2005]. Their methods rely on various heuristics to identify the locations that are most likely to introduce a defect. Kim *et al* [@Kim2007a] proposed the bug cache approach, which is an improved technique over Hassan and Holt's approach [@Hassan2005]. Rahman and Devanbu found that, in general, process-based metrics perform as good as  code-based metrics [@rahman2013].

Other studies that aim to predict risky changes use the entropy of a given change [@SunghunKim2008; @Hassan2009] and the size of the change combined with files being changed [@Kamei2013].

These techniques operate at different levels of the systems and may require the presence of the entire source code. In addition, the reliance of metrics may result in high false positives rates. We need a way to to validate whether a suspicious change is indeed risky.  In this paper,  we address this issue using a two-phase process that combines the use of metrics to detect suspicious risky changes, and code matching to increase the detect accuracy. As we will show in the evaluation section, XXX reduces the number of false positives while keeping good recall. In addition, XXX  operates at commit-time for preventing the introduction of faults before they reach the code repository. Through interactions with Ubisoft developers, we found that this integrates well with the workflow of developers.

## Automatic Patch Generation

One feature of XXX  is the ability to propose fixes that can help developers correct the detected risky commit. This is similar in principle to the work on automatic patch generation.
Pan *et al.* and  Kim *et al.* proposed two approaches that extract and apply fix patterns [@Pan2008; @Kim2013]. Pan *et al.*  identified 27 patterns and were able to fix 45.7% - 63.6% of bugs using one of the proposed patterns.
The patterns found by Kim *et al.* are mined from human-written patches and were able to successfully generate patches for 27 out of 119 bugs. The tool by Kim *et al.*, named PAR, is similar to the second part of XXX where we propose fixes. Our approach also mines potential fixes from human-written patches found in the historical data. In our work, we do not generate patches,  but instead  propose known patches to developers for further assessment. It has also been shown that patch generation is useful in understanding and debugging the causes of faults [@tao2014automatically].

Despite the advances in the field of automatic patch generation, this task remains overly complex. Developers expect from tools high quality patches that can be safely deployed. Many studies proposed a classification of what is considered  an acceptable quality patch for an automatically generated patch to be adopted in industry [@Dallmeier; @le2012systematic; @le2015should].

# Defect Prediction at Ubisoft {#sec:XXXT}

[Desribe the approach Prediction approach in a few paragraphs and introduce the cold-start problem. Basically https://github.com/PapersOfMathieuNls/misfire/blob/master/combined.md.pdf]

## Clustering Projects {#sec:clustering}

We cluster projects according to their dependencies. The rationale is that projects that share dependencies are most likely to contain defects caused by misuse of these dependencies. In this step, the project dependencies are analysed and saved into a single NoSQL graph database as shown in Figure \ref{fig:CLEVERT3}. A node corresponds to a project that is connected to other projects on which it depends. Dependencies can be _external_ or _internal_ depending on whether the products are created in-house or supplied by a third-party. For confidentiality reasons, we cannot reveal the name of the projects involved in the project dependency graph. We show the 12 projects in yellow color with their dependencies in blue color in Figure \ref{fig:dep-graph}. In total, we discovered 405 distinct dependencies that are internal and external both.
The resulting partitioning is shown in Figure \ref{fig:network-sample}.

\input{tex/dependencies}

At Ubisoft, dependencies are managed within the framework of a single repository, which makes their automatic extraction possible. The dependencies could also be automatically retrieved if the projects use a dependency manager such as Maven.

\input{tex/network-sample}

Once the project dependency graph is extracted, we use a clustering algorithm to partition the graph. To this end, we choose the Girvan–Newman algorithm [@Girvan2002; @Newman2004], used to detect communities by progressively removing edges from the original network. Instead of trying to construct a measure that identifies the edges that are the most central to communities, the Girvan–Newman algorithm focuses on edges that are most likely "between" communities. This algorithm is very effective at discovering community structure in both computer-generated and real-world network data [@Newman2004]. Other clustering algorithms can also be used.

# Case Study Setup {#sec:exp}

In this section, we present the setup of our case study in terms of repository selection, dependency analysis, comparison process and evaluation measures.

## Project Repository Selection {#sec:rep}

In collaboration with Ubisoft developers, we selected XX major software projects (i.e., systems) developed at Ubisoft to evaluate the effectiveness of XXX. These systems continue to be actively maintained by thousands of developers. Ubisoft projects are organized by game engines. A game engine can be used in the development of many high-budget games. The projects selected for this case study are related to the same game engine. For confidentiality and security reasons, neither the names nor the characteristics of these projects are provided. We can however disclose that the size of these systems altogether consists of millions of lines of code.

## Project Dependency Analysis {#sec:dependencies}

Figure \ref{fig:dep-graph} shows the project dependency graph.
As shown in Figure \ref{fig:dep-graph}, these projects are highly interconnected.
A review of each cluster shows that this partitioning divides projects in terms of their high-level functionalities. For example, one cluster is related to a particular given family of video games, whereas the other cluster refers to another family. We showed this partitioning to 11 experienced software developers and ask them to validate it. They all agreed that the results of this automatic clustering is accurate and reflects well the various project groups of the company.
The clusters are used for decreasing the rate of positive. 
In addition, fixes mined accross projects but within the cluster are qualitative as show in our experiments.

## Process of Comparing New Models

## Evaluation Measures

# Case Study Results {#sec:result}

## Performance

## Cluster Classifier Performance
  
The clusters computed with the dependencies of each project help to solve an important problem in defect  prediction, known as cold start [@Schein2002]. 
 [Wahab: you need to explain this in one or two sentences]
The clusters computed with the dependencies of each project help to solve an important problem in defect prediction, known as cold start [@Schein2002].
Indeed, as performant as a classifier can be it, after training, it still needs to be train with historical data.
While the system is learning, no prediction can be done.
  
There exist approaches have been proposed to solve this problems, however, they all require to classify _similar_ projects by hand and then, manipulate the feature space in order to adapt the model learnt from one project to another one [@Nam2013].
  
[Wahab: you need to develop this section. The text is not clear. The graphs are not either. I think defect learning transfer is another topic you can tackle in another paper. One possibility to fix this is to talk about the result of CLEVER without the clustering step]
 
With our approach, the _similarity_ between projects is computed automatically and the results show that we do not actually need to manipulate the feature space. Figures \ref{fig:bluecluster}, \ref{fig:yellowcluster} and \ref{fig:redcluster} show the performance of CLEVER classification in terms of ROC-curve for the first thousand commits of the last project (chronologically) for the blue, yellow and red clusters presented in Figure \ref{fig:network-sample}.
The left sides or the graph are low cutoff (aggressive) while the right sides are high cutoff (conservative).
The area under the ROC curves are 0.817, 0.763 and 0.806 for the blue, yellow and red clusters, respectively.
In other words, if we train a classifier with historical data from system in the same cluster as the targeted systems, then we do not have to wait to start classifying incoming commits.

[I have better results since then, this is a placeholder]

\input{tex/clusterRoc}

# Discussion {#sec:threats}

In this section, we propose a discussion on limitations and threats to validity.

## Limitations

We identified two main limitations of our approach, XXX, which require further studies.

XXX is designed to work on multiple related systems. Applying XXX on a single system will most likely be less effective. The the two-phases classification process of XXX would be hindered by the fact that it is unlikely to have a large number of similar bugs within the same system. For single systems, we recommend the use of metric-based models. A metric-based solution, however, may turn to be ineffective when applied across systems because of the difficulty associated with identifying common thresholds that are applicable to a wide range of systems.

The second limitation we identified has to do with the fact that XXX is designed to work with Ubisoft systems. Ubisoft uses C\#, C, C++, Java and other internally developed languages. It is however common to have  other languages used in an environment with many inter-related systems. We intend to extend XXX to process commits from other languages as well.

## Threats to Validity

The selection of target systems is one of the common threats to validity for approaches aiming to improve the analysis of software systems. It is possible that the selected programs share common properties that we are not aware of and therefore, invalidate our results. Because of the industrial nature of this study, we had to work with the systems developed by the company.

The programs we used in this study are all based on the C\#, C, C++ and Java programming languages.
This can limit the generalization of the results to projects written in other languages, especially that the main component of XXX is based on code clone matching.

Finally, part of the analysis of the XXX proposed fixes that we did was based on manual comparisons of the XXX fixes with those proposed by developers with a focus group composed of experienced engineers and software architects. Although, we exercised great care in analysing all the fixes, we may have misunderstood some aspects of the commits.

In conclusion, internal and external validity have both been minimized by choosing a set of 12 different systems, using input data that can be found in any programming languages and version systems (commits and changesets).

# Conclusion {#sec:conclusion}

In this paper, we presented XXX (Combining Levels of Bug Prevention and Resolution Techniques), an approach that detects risky commits (i.e., a commit that is likely to introduce a bug) with an average of 79.10% precision and a 65.61% recall.
XXX combines code metrics, clone detection techniques, and project dependency analysis to detect risky commits within and across projects.  XXX operates at commit-time, i.e., before the commits reach the central code repository. Also, because it relies on code comparison, XXX does not only detect risky commits but also makes recommendations to developers on how to fix them. We believe that this makes XXX a practical approach for preventing bugs and proposing corrective measures that integrate well with the developer's workflow through the commit mechanism.

As future work, we want to build a feedback loop between the users and the clusters of known buggy commits and their fixes.
If a fix is never used by the end-users, then we could remove it from the clusters and improve our accuracy. We also intend to improve XXX to deal with generated code. Moreover, we will investigate how to improve the fixes proposed by XXX to add contextual information to help developers better assess the applicability of the fixes.

# Reproduction Package

For security and confidentiality reasons we cannot provide a reproduction package that will inevitably involve Ubisoft's copyrighted source code.
However, the XXX source code is in the process of being open-sourced and will be soon available at https://github.com/ubisoftinc.

\begin{acks}
We are thankful to the software development team at  Ubisoft for their participations to the study and their assessment of the effectiveness of XXX.
\end{acks}
\vfill\eject
\section*{References}
<!-- End Footnotes text -->
\setlength{\parindent}{0pt} 