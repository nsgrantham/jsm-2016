footer: [Neal S. Grantham, North Carolina State University](https://nsgrantham.github.io)
slidenumbers: true
autoscale: true

# Deep Spatial Learning for Forensic Geolocation with Microbiome Data

### Neal S. Grantham<br>North Carolina State University<br><br><br>JSM Chicago<br>August 3<sup>rd</sup>, 2016

---

## Joint work with

- Brian Reich (NCSU Statistics)
- Eric Laber (NCSU Statistics)

## In collaboration with

- Rob Dunn (NCSU Biology)

---

## What is a microbiome?

Community of microbial organisms occupying an ecological niche.

Next-generation sequencing technologies make possible efficient identification of these microbes at affordable cost[^1].

Huge interest in understanding microbiomes as they relate to human health, diet, agriculture, environment, forensics, etc.

[^1]: Metzker. (2010) [Sequencing technologies—the next generation](http://www.ncbi.nlm.nih.gov/pubmed/19997069). Nature Reviews Genetics.

---

## _The White House Launches the National Microbiome Initiative_[^2]

![right](obama.jpg)

Half a billion dollars pledged, with three major goals:

- collaboration,
- developing better tools for studying microbiomes, and
- recruitment.


[^2]: [The Atlantic article](http://www.theatlantic.com/science/archive/2016/05/white-house-launches-the-national-microbiome-initiative/482598/) by Ed Yong, photo by Jim Young / Reuters

---

## Statistical challenges

Microbiome data are...

- high-dimensional,
- sparse,
- over-dispersed, and 
- possess complex dependence structure.

Many exciting opportunities for research with microbiologists.

---

## Motivating dataset

![right](dust_homes_cropped.jpg)

[Wild Life of Our Homes](homes.wildlife.org), a public science project by Rob Dunn Lab.

- Dust samples collected from outer door frames of $$n\approx1,000$$ homes across the continental U.S. 

- DNA sequencing revealed $$p \approx 50,000$$ fungi species[^3].


[^3]: Well, operational taxonomic units (OTUs) to be precise.

---

## Research question:<br><br><br><br>Does the microbiome composition of an ambient dust sample inform its geographic origin?

---

## Our approach:<br><br><br><br>Build a model to estimate the unknown origin $$s$$ of a dust sample conditional on its known microbiome composition $$x$$.

---

## Initial model and direction

Naive Bayes discriminant analysis estimates origin of dust samples with a median prediction error of 230 kilometers[^4].

Here, we develop a new model based on

1. spatial point pattern theory, and 
2. deep learning. 


[^4]: Grantham et al. (2015) [Fungi identify the geographic origin of dust samples](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0122605). PLOS One.

---

## 1. Spatial point pattern theory

Assume a spatial point pattern[^5] intensity surface $$\lambda(s\mid x)$$ has non-homogeneous Poisson process likelihood

$$
\mathcal{L}\left[\lambda(s\mid x); \{(s_i, x_i)\}_{i=1}^n\right] =
\exp\left[-\int_{\mathcal{D}}\lambda(s\mid x)d s\right]\prod\limits_{i=1}^n\lambda(s_i\mid x_i).
$$

May select a parametric model for $$\lambda(s\mid x)$$.

[^5]: Gelfand et al. (2010) [Handbook of Spatial Statistics](https://www.crcpress.com/Handbook-of-Spatial-Statistics/Gelfand-Diggle-Guttorp-Fuentes/p/book/9781420072877). CRC Press.

---

## 1. Spatial point pattern theory

Liang et al.[^6] propose a log Gaussian process (GP), 

$$\lambda(s\mid x) = \pi(s)\exp\left[\beta'x + w(s)\right]$$

with $$w(s)\sim GP$$, $$\pi(s)$$ a population offset, and $$\beta$$ unknown.

However, no closed-form solution to integral in $$\mathcal{L}$$.

[^6]: Liang et al. (2008) [Analysis of Minnesota colon and rectum cancer point patterns with spatial and nonspatial covariate information](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC2857924/). The Annals of Applied Statistics. 

---

## 1. Spatial point pattern theory

Numerical approximation possible with a Monte Carlo algorithm using a knot-based predictive process[^6].

Effective, but difficult to implement in practice:

- Requires careful knot construction.
- Performance suffers for large $$n$$, larger $$p$$.

[^6]: Liang et al. (2008) [Analysis of Minnesota colon and rectum cancer point patterns with spatial and nonspatial covariate information](http://www.ncbi.nlm.nih.gov/pmc/articles/PMC2857924/). The Annals of Applied Statistics. 

---

## 2. Deep learning

Well-suited for high-dimensional data with complex structure[^7].

Rich, GPU-enabled software libraries available:  

- Theano (Python)  
- Torch7 (Lua)  
- Tensorflow (C++)  

[^7]: LeCun et al. (2015) [Deep learning](http://www.nature.com/nature/journal/v521/n7553/full/nature14539.html). Nature.

---

## 2. Deep learning

Let $$\mathcal{P} = \{P_k\}_{k=1}^K$$ denote a partition of spatial domain $$\mathcal{D}$$.

Represent every $$s$$ by the region to which it belongs.

Two major benefits:

- Avoids costly approximation of integral in $$\mathcal{L}$$.
- Reframes estimation as a supervised classification problem.

---

## 2. Deep learning

For regions $$k=1,\ldots,K,$$

$$
Pr(s\in P_k\mid x) = \frac{\exp[f_k(x)]}{\sum_{l=1}^K\exp[f_l(x)]} \;\text{where}\; f_k:\mathcal{X}\rightarrow\mathbb{R}. 
$$

Estimate $$f_1(\cdot), \ldots, f_K(\cdot)$$ by training a deep neural network (DNN)  on $$\{(s_i, x_i)\}_{i=1}^n$$ with categorical cross-entropy cost function

$$
C = -\sum_{i=1}^{n}\sum_{k=1}^K \log\left[Pr(s_i\in P_k\mid x_i)\right]I(s_i \in P_k).
$$

---


## Our "Deep Space" algorithm

1. Generate random Voronoi partition $$\mathcal{P}$$ over $$\mathcal{D}$$.
2. Train DNN on available data from these regions.
3. Repeat steps 1 & 2 $$N$$ times to develop a diverse collection, $$\mathcal{M}$$, of trained DNNs.
4. Predict most likely origin $$\hat{s}$$ by averaging over DNNs in $$\mathcal{M}$$.


---

# <br><br><br><br><br>1. Generate random Voronoi partition $$\mathcal{P}$$ over $$\mathcal{D}$$.

---


![](usa.pdf)

---

![](grid.pdf)

---

![](gridseeds.pdf)

---

![](seeds.pdf)

---

![](partition.pdf)

---

![](labels.pdf)

---

# <br><br><br><br><br>2. Train DNN on available data from these regions.

---

![](points.pdf)

---


![](region2.pdf)

---

# <br><br><br><br><br>3. Repeat steps 1 & 2 $$N$$ times to develop a diverse collection, $$\mathcal{M}$$, of trained DNNs.

---

![](region6.pdf)

---

![](region3.pdf)

---

![](region7.pdf)

---

![](region8.pdf)

---

![](region9.pdf)

---

![](region10.pdf)



---

![](region12.pdf)


---

![](region14.pdf)


---

![](region15.pdf)

---

# <br><br><br><br><br>4. Predict most likely origin $$\hat{s}$$ by averaging over DNNs in $$\mathcal{M}$$. 

---

## Geolocation

A sample with microbiome $$x$$ is most likely to have originated from 

$$
\hat{s} = \underset{s\in\mathcal{D}}{\arg\max}\;g(s\mid x,\mathcal{M})
$$

where $$g(\cdot)$$ is the geolocation function given by

$$
g(s\mid x,\mathcal{M}) = \frac{1}{N}\sum_{j=1}^N \sum_{k=1}^{K_j}\frac{1}{|P_{jk}|}Pr(s\in P_{jk}\mid x)I(s\in P_{jk}). 
$$


---

![](plot1a.pdf)

---

![](plot1b.pdf)

---

![](plot1c.pdf)

---

# <br><br><br><br><br>More test sample predictions...

---

![](plot2a.pdf)

---

![](plot2b.pdf)

---

![](plot2c.pdf)

---

![](plot3a.pdf)

---

![](plot3b.pdf)

---

![](plot3c.pdf)

---

![](plot4a.pdf)

---

![](plot4b.pdf)

---

![](plot4c.pdf)

---

![](plot5a.pdf)

---

![](plot5b.pdf)

---

![](plot5c.pdf)

---

## Prediction errors

![left inline](hist.pdf)

```
    Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
   1.751   53.990  114.700  230.000  245.500 2394.000 
```

---

## Further work

Develop hypothesis testing framework to test if a sample originates from a particular county, state, etc.

Which fungi are most endemic to different biogeographies? Inference in deep learning is an active area of research.

New applications? The algorithm is not restricted to these data.

Python module `deepspace` to be made available at [github.com/nsgrantham/deepspace](http://www.github.com/nsgrantham/deepspace) upon publication. 

---

# Thank You! Questions?

Slides available at [github.com/nsgrantham/jsm-2016](https://www.github.com/nsgrantham/jsm-2016)

Contact me:

- Website: [nsgrantham.github.io](https://nsgrantham.github.io)
- Github: [github.com/nsgrantham](https://www.github.com/nsgrantham)
- Twitter: [twitter.com/nsgrantham](https://www.twitter.com/nsgrantham)

