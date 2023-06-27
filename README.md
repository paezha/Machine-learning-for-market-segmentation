
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Using machine learning to identify spatial market segments. A reproducible study of major Spanish markets

<!-- badges: start -->
<!-- badges: end -->

David Rey Blanco (Idealista) Pelayo Arbués (Idealista) Fernando A. López
(Universidad Politécnica de Cartagena) [Antonio
Páez](paezha@mcmaster.ca) (McMaster University)

## Keywords:

- Hedonic prices
- market segments
- decision trees
- machine learning
- spatial econometrics
- reproducible research
- Spain

## Abstract

Identifying market segments can improve the fit and performance of
hedonic price models. In this paper, we present a novel approach to
market segmentation based on the use of machine learning techniques.
Concretely, we propose a two-stage process. In the first stage,
classification trees with interactive basis functions are used to
identify non-orthogonal and non-linear sub-market boundaries. The market
segments that result are then introduced in a spatial econometric model
to obtain hedonic estimates of the implicit prices of interest. The
proposed approach is illustrated with a reproducible example of three
major Spanish real estate markets. We conclude that identifying market
subsegments using the approach proposed is a relatively simple and
demonstrate the potential of the proposed modelling strategy to produce
better models and more accurate predictions

# Introduction

Hedonic price analysis is one of the most widely-used approaches for the
study and valuation of properties in real estate markets. This approach
is attractive due to its strong theoretical grounding and appealing
interpretation (Rosen 1974). Indeed, when hedonic price models are
estimated using multiple linear regression the coefficients of the model
are thought to capture the implicit prices of attributes in a bundled
good. In this way, while a room may lack an explicit price in the
valuation of a property, the coefficient of a hedonic price model
quantifies its implicit value. Such decomposition of the price of a
bundled good into the implicit prices of its constituent parts is
important for multiple reasons: this analysis is the industry standard
for property assessment for tax purposes (Morillo, Cepeda, and
Martinez-Cuevas 2017); these models are used to quantify the willingness
to pay for non-market environmental amenities (Montero,
Fernandez-Aviles, and Minguez 2018), including air quality and open
spaces and similarly they can be used to assess the cost of disamenities
(e.g., Graevenitz 2018).

The need to assess property values in a transparent, accurate, and
precise way has led to numerous developments. A strand of research has
aimed at enhancing the performance of models by incorporating spatial
information. Geographic Information Systems (GIS) in particular have
been used to make explicit some attributes of properties and their
environments that might otherwise be overlooked (Paterson and Boyle
2002). The use of spatial data, in turn, has brought increased attention
to the question of statistical sufficiency and therefore the need for
approaches that appropriately consider the issues of spatial association
and spatial heterogeneity in hedonic price analysis (Pace and Gilley
1997; A. Paez, Uchida, and Miyamoto 2001). As a result, there has been a
proliferation of studies that apply spatial statistical or econometric
methods to the issue of property valuation (A. Paez 2009). Recent
applications include the use of hierarchical spatial autoregressive
models (Cellmer, Kobylinska, and Belej 2019), moving windows approaches
(Páez, Long, and Farber 2008), spatial filtering (Helbich and Griffith
2016), and kriging techniques (Montero-Lorenzo, Larraz-Iribas, and Paez
2009), among others.

In addition to interest in spatial data, the application of machine
learning techniques for hedonic price analysis has also become an active
topic of research. There are at least two distinct ways in which machine
learning can be used for hedonic price analysis. In some studies, the
role of machine learning algorithms is to process information that would
otherwise be difficult or impossible to obtain using non-automated
means. The information obtained is then used as an input in econometric
hedonic models. For example, Humphreys, Nowak, and Zhou (2019) and Nowak
and Sayago-Gomez (2018) used machine learning classifiers to ethnically
profile buyers and sellers based on last names to understand whether
potential cultural biases and/or discrimination issues exist in property
transactions. In other research, machine learning algorithms replace the
conventional hedonic price model (Hu et al. 2019; Yoo, Im, and Wagner
2012; Füss and Koller 2016). The evidence available shows that machine
learning methods can perform remarkably well, but can also be seen as
black boxes[^1] with low interpretability (see page 25 in James et al.
2013).

Our objective in this paper is to introduce a novel approach that
retains the interpretability of econometric approaches, but is enhanced
by the identification of spatial market segments obtained from the use
of machine learning techniques. We propose a two-stage approach. In the
first stage, classification trees are implemented to identify
homogeneous spatial market segments. The number of market segments is
endogenous, and, compared to Füss and Koller (2016), the use of
interactive basis functions (see Antonio Paez et al. 2019) can
accommodate non-orthogonal and non-linear decision boundaries. The
market segments are then introduced as covariates in an econometric
model. This approach can potentially enhance the model without
compromising its interpretability.

A reproducible case study of property values in three major markets in
Spain helps to illustrate the proposed approach. Following
recommendations for openness and reproducibility in geospatial research
(Antonio Paez 2021), this paper is accompanied by a fully documented and
open data product (see Arribas-Bel et al. 2021), and the code is
embedded in a self-contained Rmarkdown document. The results show that
modeling prices using the approach proposed to identify spatial market
segments improves the fit of the models and can in addition enhance the
quality of predictions.

<!--
Use machine learning to predict prices: @Hu2019monitoring; @Ceh2018estimating compare random forests versus multiple regression; @Chen2017forecasting use support vector machine to forecast prices; @Yoo2012variable use Cubist and Random Forest (RF) for variable selection and modelling of prices;  @Perez2019machine use machine learning to estimate prices

@Balsera2018artificial, @Feng2015comparing; @Fuss2016role
-->

# Spatial Market Segmentation

The importance of housing submarkets has long been recognized in the
literature (e.g. Rapkin, Winnick, and Blank 1953). Market
differentiation can be the result of a variety of processes operating
separately or in conjunction, including substitution, differentiation,
and variations in consumer preferences (Galster 1996). In principle,
this implies a degree of homogeneity within the market segment that
differentiates it from other segments. According to Thibodeau (2003,
4–5) a spatial housing submarket “defines a geographic area where the
price of housing per unit of housing service is constant”. Given the
non-tradeable nature of location, research has shown the relevance of
spatial market segments (Bourassa, Cantoni, and Hoesh 2007; Royuela and
Duque 2013; Usman, Lizam, and Adekunle 2020).

Submarket analysis is often implemented in a pragmatic way, encompassing
regional boundaries, for instance those of metropolitan regions, cities,
or municipalities. It has long been recognized, though, that sub-markets
may exist at smaller scales (e.g., Rapkin, Winnick, and Blank 1953). In
particular, the pioneering work of Alonso (Alonso 1964) on urban
structure led to the realization of the importance of geography in terms
of differentiation of real estate property. Since then, vast amounts of
empirical evidence have contributed to demonstrate just how commonplace
differences in hedonic prices are at the intraurban scale. Concurrently,
market segmentation has been shown to be not only a conceptually sound
practice (see Watkins 2001), but also conducive to higher quality models
and improved predictive performance, in particular when geography is
explicitly taken into consideration (Páez, Long, and Farber 2008).

Numerous approaches have been proposed to identify market segments. Some
are based on expert opinion, such as from appraisers (Wheeler et al.
2014). Many others are data-driven, using statistical or machine
learning techniques (e.g., Helbich et al. 2013; Wu et al. 2018).
Heuristic approaches also exist that exploit the latent homogeneity in
values (Royuela and Duque 2013). Implementation of market segments in
hedonic price models can be accomplished by means of fixed effects
(i.e., dummy variables) for sub-regions (e.g., Bourassa, Cantoni, and
Hoesh 2007), spatial drift by means of a trend surface (e.g. Pace and
Gilley 1997), spatially autoregressive models (e.g., Pace et al. 1998),
switching regressions (e.g., Islam and Asami 2011; A. Paez, Uchida, and
Miyamoto 2001), multilevel and/or Bayesian models (e.g., Wheeler et al.
2014), or by means of spatially moving windows or non-parametric
techniques to obtain soft market segments (Páez, Long, and Farber 2008;
Hwang and Thill 2009). As is commonly the case, there is no one
technique that performs consistently better than the alternatives in
every case, since performance depends to some extent on the
characteristics of the process being modeled (Usman, Lizam, and Adekunle
2020). It is therefore valuable to explore alternative approaches to
identify and model market segments, to further enrich the repertoire of
techniques available to analysts.

A recent proposal along these lines is due to Füss and Koller (2016),
who suggest using decision trees to identify and model market segments.
James et al. (2013) list some attractive features of decision trees.
They are relatively simple to estimate and intuitive to interpret. They
divide attribute space into a set of mutually exclusive and collectively
exhaustive regions, and thus are ideally suited for market segmentation.
By design, the regions generated are spatially compact and internally
homogeneous. And they can outperform other regression techniques. Market
segments derived from a decision tree can be used in combination with
other modeling techniques, such as a second-stage tree regression (with
fixed effects for the market segments from the preliminary tree
regression), linear models, or models with spatial or spatio-temporal
effects, such as space-time autoregression. Füss and Koller (2016)
compare several different modeling techniques. Their findings confirm
that introducing a form of market segmentation greatly improves
prediction accuracy, and the use of tree-based market segments does so
more than the use of an *a priori* zoning system defined by ZIP codes.
Furthermore, accounting for residual spatial pattern in the form of a
spatial autoregressive model further improves the accuracy of
estimation.

The results reported by Füss and Koller (2016) are appealing. However,
the modeling strategy that they implement inherits a limitation of tree
regression, namely the relatively inflexible way in which attribute
space is partitioned using recursive binary splits. What this means is
that market segments obtained in this way are limited to linear and
orthogonal boundaries (see page 1359 in Füss and Koller 2016). While
prediction accuracy reportedly improves with tree-based segmentation of
the market, it might be desirable to define market segments more
flexibly, so that they are not constrained to rectangular shapes.
Secondly, estimates of a regression tree are the mean of the values
contained in the volume of a leaf, which means they are constants for
each leaf. In a geographical application the leaves are mutually
exclusive and collectively exhaustive partitions of geographical space.
Using the residuals in the second step of the modelling strategy induces
spatial autocorrelation, since all properties in the same segment will
be given estimated residuals that are constants in each market segment.
The issue here is that by introducing spatial autocorrelation in the
second step some of the spatial information about location is obscured
since there is zero spatial variation in the estimated residuals for a
given market segment.

We address these two issues by using interactive basis functions
(Antonio Paez et al. 2019) to induce non-orthogonal and non-linear
decision boundaries in our models of market segments. Further, by moving
the analysis of market segments to the first stage of the analysis, we
obtain market segments with good homogeneity properties, and any spatial
autocorrelation is dealt with by means of the spatial econometric model
in the second step. The modelling strategy is described in more detail
next.

# Modeling Strategy and Methods

## Modeling Strategy

We propose a two-stage modelling strategy, as follows:

1.  Estimate a first stage classification tree using the prices and the
    coordinates of the observations only (similar to trend surface
    analysis, see Unwin 1978).

- Map the regions $R_m$ that result: these are the $m=1,\cdots,M$
  submarkets.
- Overlay the observations on the tree-based regions and create a set of
  $m$ indicator variables for submarket membership: $I_m=I(y_i\in R_m)$;
  when the argument of the indicator function is true (i.e., when
  observation $y_i$ is in $R_m$) then $I_m=1$, otherwise $I_m=0$.

2.  Estimate a second-stage hedonic price model that incorporates the
    indicator variables for submarkets obtained in first stage including
    spatial interaction effects and other relevant covariates.

Note that the modeling strategy proposed here differs from the one
proposed by Füss and Koller (2016) in that the market areas are
identified by these authors based on the residuals of a preliminary
regression, whereas we identify them based on the prices directly. It is
worth noting that these two strategies reflect different heuristics.
Identification of market areas based on the prices implies that market
areas are formed based on unitary properties before properties are
assessed as bundles of attributes. Identification of market areas based
on the residuals, on the other hand, implies that properties are first
seen as bundles of attributes and that submarkets form based on other
non-identified attributes.

## Methods

Two methodologies are combined in the modeling strategy. For first-stage
we apply the well-known algorithm of classification trees with the
objective of identify spatial submarkets. The algorithm is applied using
the variation suggested by Antonio Paez et al. (2019) to obtain
non-orthogonal and non-linear boundaries via interactive basis
functions. A short description of this method is found in the
supplementary material. For the second-stage we apply spatial
econometric methods to solve the presence of spatial autocorrelation in
the residual of the classical hedonic models. The patial econometric
models considered are also briefly described in the supplementary
material. These methods are implemented in a number of open-source R
packages. The **tree** R package (Ripley 2021) was used in first-stage
and **spsur** (Lopez, Minguez, and Mur 2020) and **spatialreg** (Bivand
et al. 2013) in second-stage to estimate spatial regression models.
Finally, with the objective of evaluate the forecasting accuracies of
the different models and avoid overfitting the data set is split in
training and test subsamples. The training subsample is used to obtain
the model and the test subsample to evaluate the forecasting. The R
package **spatialreg** is used to get the out-of-sample predictions is a
spatial econometric framework.

# Data

The empirical examples to follow correspond to large cities in Spain.
The real estate market is one of the most important sectors of the
Spanish economy, and the largest urban areas in Spain are important
points of reference for the real estate market in the country. The three
largest markets are Madrid (the national capital with 3.2 million
inhabitants), Barcelona (1.6 million), and Valencia (0.8 million
inhabitants). The focus of our application is on property prices in
these cities. Micro-data from official sources are not available in
Spain; instead, we draw our data from an online real estate database,
Idealista.com (the leading real estate portal in Spain).

The data are documented and prepared for sharing publicly in the form of
an open data product (Arribas-Bel et al. 2021) under the structure of a
R package free available from a repository and a data paper describe the
full data set. The database is for postings during 2018, and the
analysis uses the last quarter of the year. We use the asking price as a
proxy for the selling price; this is common practice in many real estate
studies (e.g., López, Chasco, and Gallo 2015; Chasco, Le Gallo, and
López 2018). For the three data sets we consider the most frequent type
of property in Spain, namely the flat (hereon termed “houses”); this
excludes other types of properties, such as duplex, chalets, and attics,
which conform separate real estate markets.

The data sets used in the analysis correspond to the last quarter of
2018, and include a total of $n=$ 44,270 for Madrid, $n=$ 23,334 for
Barcelona, and $n=$ 14,018 for Valencia. The distribution of prices
displays a long tail in all three cities, and following conventional
practice it is log-transformed. The coordinates are converted from
latitude and longitude to northing and easting in meters, and then
rescaled and centered using the corresponding city’s Central Business
District as a false origin. These transformations have no impact on the
analysis, and rescaling and centering of the coordinates is necessary
for the correct implementation of the interactive basis functions in
decision trees (see Antonio Paez et al. 2019, 188–89).

For this research we select thirteen explanatory variables. Of these,
ten attributes are data provided by Idealista.com and represent key
structural attributes of the properties. These are whether the property
is a studio (a small type of bachelor apartment), whether it is on the
top floor of the building, and its built area, number of rooms, number
of baths, and presence of a terrace. In addition, there are variables
for elevator in the building, air conditioner, swimming pool, and
parking spaces. We augment these attributes with locational variables
derived from the coordinates of the property, including distance to
nearest major transit station (metro), distance to the city center
(central business district; CBD), and distance to major avenues. These
locational attributes are frequently advertised by real estate agents
and often capitalized in housing prices. Table gives the definitions of
these variables and the descriptive statistics of the data.

# Empirical Examples

## Experimental Design

Each city’s data set is split into a training sample and a testing
sample using a 7:3 proportion. The training samples are used to estimate
the models and the testing samples are used to assess the out-of-sample
performance of the models.

We consider four models. First is a Base Model:

The second is a base model with market segments (Base Model + MS):

The third is a spatial lag model (Spatial Model):

And finally, the most general is a spatial lag model with market
segments (Spatial Model + MS):

Please note that all models nest in the Spatial Model + MS depending on
what restrictions are placed on the parameters. In the Base Model:

In the Base Model + MS:

And in the Spatial Model:

The weights in the spatial weight matrices are calculated using the
inverse of the distance between neighboring observations, so that closer
observations receive a higher weight. To avoid increasing the density of
the matrices \[which has computational and also estimation effects\], we
combine this criterion with a cutoff of $k=6$ nearest neighbors. Given
the distribution of distances in the sample, beyond these neighbors the
inverse distance results in extremely small contributions to the
autocorrelation effect. With respect to the interactive basis functions
for the decision trees, we consider the following functions (see
supplementary material) with $u$ and $v$ as the planar coordinates of
the observations, easting and northing respectively:

We first look at the estimated models before discussing the in- and
out-of-sample predictive performance of the models.

## Modelling Results

The first model in each of Tables , , and is the Base Model. The fit of
these models is reasonably high: the adjusted coefficients of
determination are $R^2=$ 0.796, $R^2=$ 0.777, and $R^2=$ 0.795 for
Barcelona, Madrid, and Valencia, respectively. These models are
relatively naive in that they disregard both the possibility of spatial
autocorrelation and spatial heterogeneity (in the form of spatial market
sub-segments). They do provide a useful benchmark to compare the
proposed modelling strategy.

The first stage of the modelling strategy is to train a decision tree on
the property values using only the coordinates of the observations. The
spatial market sub-segments derived from the decision trees are shown in
Figure . It can be seen there that the algorithm detects seven market
sub-segments in Barcelona, nine market sub-segments in Madrid, and eight
in Valencia. These submarkets are compact, mutually exclusive, and
collectively exhaustive. The smallest market segment is found in
Valencia and has 331 recorded transactions; the largest market segment,
in contrast, has 7,816 recorded transactions and is found in Madrid. The
maps in the figure show how the use of interactive basis functions leads
to non-orthogonal/non-linear boundaries for the sub-markets. In the case
of Barcelona, there are some distinctive diagonal shapes reminiscent of
the street pattern in the city. In Madrid there is a clear distinction
given by the M-30 orbital that surrounds the central almond of the city;
in addition, there is Paseo de la Castellana, a major north-south avenue
that crosses the city. This avenue divides two zones in the north that
tend to include more expensive real estate, whereas the south tends to
be lower income and less expensive. In Valencia, the sub-markets
identify several zones in the historical center of the city, and then
larger regional patterns depending on proximity to the waterfront to the
west of the city.

![Spatial market segments according to Stage 1 classification tree.
Barcelona (upper-left), Madrid (upper-right), and Valencia
(botton-center)](README_files/figure-gfm/unnamed-chunk-2-1.png)

The spatial market sub-segments are coded as dummy variables in the data
sets before re-estimating the Base Model with market segments (Base
Model + MS). The second model reported in Tables , , and shows that the
market segments tend to be highly significant, and also improve the fit
of the model. In the case of Barcelona, the adjusted coefficient of
determination changes to $R^2=$ 0.821, for a modest increase of 3.19%.
The introduction of the market segments into the Base Model for Madrid
results in an adjusted coefficient of determination of $R^2=$ 0.878,
which represents a change of 13.09% relative to the adjusted coefficient
of determination of the Base Model. In Valencia, the Base Model with
market segments has an adjusted coefficient of determination of $R^2=$
0.83, for an increase with respect to the Base Model of 4.49%.

It is well-known that spatial heterogeneity and association can co-exist
(e.g., Bourassa, Cantoni, and Hoesh 2007; A. Paez, Uchida, and Miyamoto
2001). Sub-market identification can assist with spatial heterogeneity,
but a process of spatial association could result from the common
heuristic of comparative sales used by real estate agents. This process
is appropriately represented by a spatial lag model. The third model
reported in Tables , , and is the Spatial Model, that is the Base Model
with a spatial lag (i.e., Equation ). Spatial lag models, being
non-linear, lack the coefficient of determination of linear regression.
Instead, their goodness of fit is evaluated using likelihood measures.
It can be seen that there is a substantial improvement in this regard in
all three cities.
<!-- ^[Note that the log-likelihood of spatial models can be positive.] -->
The spatial lag parameter $\rho$ represents the proportion of the mean
of the neighboring prices that is reflected in the price of the property
at $i$. In Barcelona, this parameter suggests that approximately 32.16%
of the mean of the price of the $k=6$ nearest neighbors is reflected in
the price at $i$. This “comparative sales” effect is markedly stronger
in Madrid, where it amounts to 47.89% of the mean price of the
neighbors. In Valencia, this effect is 40.31%. The spatial lag parameter
is significant in all three cases, and the results suggest that
comparisons with other properties play a larger role in the
determination of prices in Madrid.

The last model that we consider for these case studies is a spatial lag
model with market segments. This is the most general of the four models,
and we see that the combination of market segments and a spatial lag
variable gives the best fit in terms of the log-likelihood, and also
reduces the size of the spatial lag coefficient, shifting some of the
spatial effect from spatial autocorrelation to spatial heterogeneity.

At this point, it is important to note that the coefficients of models
with spatial lags cannot be interpreted as marginal effects due to the
ripple effects of lagging variables (i.e., the multiplier effect of the
lag). Instead, the direct, indirect, and total impacts need to be
considered. The impacts of our best models (spatial models with market
segments) are presented in Tables , , and .

<!--
Spatial autocorrelation and spatial heterogeneity can interact in complex ways, and while these two effects cannot be distinguished a priori, the improved fit suggests that spatial heterogeneity (i.e., the spatial market segments) are a better interpretation of the data than spatial autocorrelation alone. There is an additional way in which the two effects interact: spatial heterogeneity reduces the strength of spatial autocorrelation, but in turn spatial autocorrelation can influence the effect of the spatial market segments through the multiplier effect. As seen in Tables \ref{tab:model-impacts-barcelona}, \ref{tab:model-impacts-madrid}, and \ref{tab:model-impacts-valencia}, the total effect of each market segment is greater than the direct effect alone.
-->
<table>
<caption>
Models Barcelona (Dependent Variable is log of Price)
</caption>
<thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;" colspan="1">
</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Base Model

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Base Model + MS

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Spatial Model

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Spatial Model + MS

</div>

</th>
</tr>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
</tr>
</thead>
<tbody>
<tr grouplength="12">
<td colspan="9" style="border-bottom: 1px solid;">
<strong>Property attributes</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
(Intercept)
</td>
<td style="text-align:center;">
11.9207
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
11.5877
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
7.9308
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
8.4258
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
CONSTRUCTEDAREA
</td>
<td style="text-align:center;">
0.0058
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0053
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0048
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0048
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ROOMNUMBER
</td>
<td style="text-align:center;">
0.0233
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0255
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0239
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0243
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
BATHNUMBER
</td>
<td style="text-align:center;">
0.1351
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1164
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1026
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1004
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASTERRACE
</td>
<td style="text-align:center;">
0.0785
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0799
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.074
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0752
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASLIFT
</td>
<td style="text-align:center;">
0.2302
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1962
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.165
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1583
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASAIRCONDITIONING
</td>
<td style="text-align:center;">
0.1095
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1093
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1017
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1024
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASSWIMMINGPOOL
</td>
<td style="text-align:center;">
0.1572
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1508
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.129
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.127
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISSTUDIO
</td>
<td style="text-align:center;">
-0.2628
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.2568
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.237
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.2386
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISINTOPFLOOR
</td>
<td style="text-align:center;">
0.0408
</td>
<td style="text-align:center;">
0.0045
</td>
<td style="text-align:center;">
0.0476
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0441
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0456
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASPARKINGSPACE
</td>
<td style="text-align:center;">
0.1385
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0806
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0726
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0585
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CITY_CENTER
</td>
<td style="text-align:center;">
-0.1023
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0611
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0664
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0469
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr grouplength="6">
<td colspan="9" style="border-bottom: 1px solid;">
<strong>Market segments</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ2
</td>
<td style="text-align:center;">

- </td>
  <td style="text-align:center;">

  - </td>
    <td style="text-align:center;">
    0.1572
    </td>
    <td style="text-align:center;">
    0.001
    </td>
    <td style="text-align:center;">

    - </td>
      <td style="text-align:center;">

      - </td>
        <td style="text-align:center;">
        0.0885
        </td>
        <td style="text-align:center;">
        0.001
        </td>
        </tr>
        <tr>
        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
        market_segmentZ3
        </td>
        <td style="text-align:center;">

        - </td>
          <td style="text-align:center;">

          - </td>
            <td style="text-align:center;">
            0.2761
            </td>
            <td style="text-align:center;">
            0.001
            </td>
            <td style="text-align:center;">

            - </td>
              <td style="text-align:center;">

              - </td>
                <td style="text-align:center;">
                0.1697
                </td>
                <td style="text-align:center;">
                0.001
                </td>
                </tr>
                <tr>
                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                market_segmentZ4
                </td>
                <td style="text-align:center;">

                - </td>
                  <td style="text-align:center;">

                  - </td>
                    <td style="text-align:center;">
                    0.3654
                    </td>
                    <td style="text-align:center;">
                    0.001
                    </td>
                    <td style="text-align:center;">

                    - </td>
                      <td style="text-align:center;">

                      - </td>
                        <td style="text-align:center;">
                        0.2191
                        </td>
                        <td style="text-align:center;">
                        0.001
                        </td>
                        </tr>
                        <tr>
                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                        market_segmentZ5
                        </td>
                        <td style="text-align:center;">

                        - </td>
                          <td style="text-align:center;">

                          - </td>
                            <td style="text-align:center;">
                            0.4102
                            </td>
                            <td style="text-align:center;">
                            0.001
                            </td>
                            <td style="text-align:center;">

                            - </td>
                              <td style="text-align:center;">

                              - </td>
                                <td style="text-align:center;">
                                0.2381
                                </td>
                                <td style="text-align:center;">
                                0.001
                                </td>
                                </tr>
                                <tr>
                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                market_segmentZ6
                                </td>
                                <td style="text-align:center;">

                                - </td>
                                  <td style="text-align:center;">

                                  - </td>
                                    <td style="text-align:center;">
                                    0.5074
                                    </td>
                                    <td style="text-align:center;">
                                    0.001
                                    </td>
                                    <td style="text-align:center;">

                                    - </td>
                                      <td style="text-align:center;">

                                      - </td>
                                        <td style="text-align:center;">
                                        0.2739
                                        </td>
                                        <td style="text-align:center;">
                                        0.001
                                        </td>
                                        </tr>
                                        <tr>
                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                        market_segmentZ7
                                        </td>
                                        <td style="text-align:center;">

                                        - </td>
                                          <td style="text-align:center;">

                                          - </td>
                                            <td style="text-align:center;">
                                            0.5605
                                            </td>
                                            <td style="text-align:center;">
                                            0.001
                                            </td>
                                            <td style="text-align:center;">

                                            - </td>
                                              <td style="text-align:center;">

                                              - </td>
                                                <td style="text-align:center;">
                                                0.2541
                                                </td>
                                                <td style="text-align:center;">
                                                0.001
                                                </td>
                                                </tr>
                                                <tr grouplength="1">
                                                <td colspan="9" style="border-bottom: 1px solid;">
                                                <strong>Spatial lag
                                                parameter</strong>
                                                </td>
                                                </tr>
                                                <tr>
                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                rho
                                                </td>
                                                <td style="text-align:center;">

                                                - </td>
                                                  <td style="text-align:center;">

                                                  - </td>
                                                    <td style="text-align:center;">

                                                    - </td>
                                                      <td style="text-align:center;">

                                                      - </td>
                                                        <td style="text-align:center;">
                                                        0.3216
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.001
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.2647
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.001
                                                        </td>
                                                        </tr>
                                                        <tr grouplength="3">
                                                        <td colspan="9" style="border-bottom: 1px solid;">
                                                        <strong>Model
                                                        diagnostics</strong>
                                                        </td>
                                                        </tr>
                                                        <tr>
                                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                        R-squared
                                                        </td>
                                                        <td style="text-align:center;">
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.8
                                                        </td>
                                                        <td style="text-align:center;">
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.82
                                                        </td>
                                                        <td style="text-align:center;">
                                                        </td>
                                                        <td style="text-align:center;">

                                                        - </td>
                                                          <td style="text-align:center;">
                                                          </td>
                                                          <td style="text-align:center;">

                                                          - </td>
                                                            </tr>
                                                            <tr>
                                                            <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                            adj-R-squared:
                                                            </td>
                                                            <td style="text-align:center;">
                                                            </td>
                                                            <td style="text-align:center;">
                                                            0.8
                                                            </td>
                                                            <td style="text-align:center;">
                                                            </td>
                                                            <td style="text-align:center;">
                                                            0.82
                                                            </td>
                                                            <td style="text-align:center;">
                                                            </td>
                                                            <td style="text-align:center;">

                                                            - </td>
                                                              <td style="text-align:center;">
                                                              </td>
                                                              <td style="text-align:center;">

                                                              - </td>
                                                                </tr>
                                                                <tr>
                                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                log-likelihood:
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">
                                                                -781.44
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">
                                                                303.91
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">
                                                                989.45
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">
                                                                1247.72
                                                                </td>
                                                                </tr>
                                                                </tbody>
                                                                </table>

<table style="NAborder-bottom: 0;">
<caption>
Models Madrid (Dependent Variable is log of Price)
</caption>
<thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;" colspan="1">
</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Base Model

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Base Model + MS

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Spatial Model

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Spatial Model + MS

</div>

</th>
</tr>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
</tr>
</thead>
<tbody>
<tr grouplength="14">
<td colspan="9" style="border-bottom: 1px solid;">
<strong>Property attributes</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
(Intercept)
</td>
<td style="text-align:center;">
11.8006
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
11.318
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
5.9368
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
8.1051
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
CONSTRUCTEDAREA
</td>
<td style="text-align:center;">
0.0055
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0045
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0038
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0039
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ROOMNUMBER
</td>
<td style="text-align:center;">
-0.0068
</td>
<td style="text-align:center;">
0.0087
</td>
<td style="text-align:center;">
0.0345
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0223
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0368
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
BATHNUMBER
</td>
<td style="text-align:center;">
0.1653
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1163
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0999
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0958
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASTERRACE
</td>
<td style="text-align:center;">
-0.0098
</td>
<td style="text-align:center;">
0.0265
</td>
<td style="text-align:center;">
0.0459
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0202
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0436
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASLIFT
</td>
<td style="text-align:center;">
0.3809
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.2527
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.2175
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.2022
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASAIRCONDITIONING
</td>
<td style="text-align:center;">
0.1036
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0878
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0871
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0841
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASSWIMMINGPOOL
</td>
<td style="text-align:center;">
0.2119
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1961
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0895
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.129
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISSTUDIO
</td>
<td style="text-align:center;">
-0.1746
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.1827
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.1497
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.1671
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISINTOPFLOOR
</td>
<td style="text-align:center;">
0.0256
</td>
<td style="text-align:center;">
0.0565
</td>
<td style="text-align:center;">
0.0225
</td>
<td style="text-align:center;">
0.0233
</td>
<td style="text-align:center;">
0.0361
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0296
</td>
<td style="text-align:center;">
0.0012
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASPARKINGSPACE
</td>
<td style="text-align:center;">
0.0885
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1197
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0598
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0915
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_METRO
</td>
<td style="text-align:center;">
0.033
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0414
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0065
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0394
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CITY_CENTER
</td>
<td style="text-align:center;">
-0.0474
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0484
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0301
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0394
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CASTELLANA
</td>
<td style="text-align:center;">
-0.0631
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0195
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0215
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0175
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr grouplength="8">
<td colspan="9" style="border-bottom: 1px solid;">
<strong>Market segments</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ2
</td>
<td style="text-align:center;">

- </td>
  <td style="text-align:center;">

  - </td>
    <td style="text-align:center;">
    0.0663
    </td>
    <td style="text-align:center;">
    0.001
    </td>
    <td style="text-align:center;">

    - </td>
      <td style="text-align:center;">

      - </td>
        <td style="text-align:center;">
        0.047
        </td>
        <td style="text-align:center;">
        0.001
        </td>
        </tr>
        <tr>
        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
        market_segmentZ3
        </td>
        <td style="text-align:center;">

        - </td>
          <td style="text-align:center;">

          - </td>
            <td style="text-align:center;">
            0.2286
            </td>
            <td style="text-align:center;">
            0.001
            </td>
            <td style="text-align:center;">

            - </td>
              <td style="text-align:center;">

              - </td>
                <td style="text-align:center;">
                0.1643
                </td>
                <td style="text-align:center;">
                0.001
                </td>
                </tr>
                <tr>
                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                market_segmentZ4
                </td>
                <td style="text-align:center;">

                - </td>
                  <td style="text-align:center;">

                  - </td>
                    <td style="text-align:center;">
                    0.4721
                    </td>
                    <td style="text-align:center;">
                    0.001
                    </td>
                    <td style="text-align:center;">

                    - </td>
                      <td style="text-align:center;">

                      - </td>
                        <td style="text-align:center;">
                        0.3385
                        </td>
                        <td style="text-align:center;">
                        0.001
                        </td>
                        </tr>
                        <tr>
                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                        market_segmentZ5
                        </td>
                        <td style="text-align:center;">

                        - </td>
                          <td style="text-align:center;">

                          - </td>
                            <td style="text-align:center;">
                            0.4969
                            </td>
                            <td style="text-align:center;">
                            0.001
                            </td>
                            <td style="text-align:center;">

                            - </td>
                              <td style="text-align:center;">

                              - </td>
                                <td style="text-align:center;">
                                0.3501
                                </td>
                                <td style="text-align:center;">
                                0.001
                                </td>
                                </tr>
                                <tr>
                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                market_segmentZ6
                                </td>
                                <td style="text-align:center;">

                                - </td>
                                  <td style="text-align:center;">

                                  - </td>
                                    <td style="text-align:center;">
                                    0.5343
                                    </td>
                                    <td style="text-align:center;">
                                    0.001
                                    </td>
                                    <td style="text-align:center;">

                                    - </td>
                                      <td style="text-align:center;">

                                      - </td>
                                        <td style="text-align:center;">
                                        0.366
                                        </td>
                                        <td style="text-align:center;">
                                        0.001
                                        </td>
                                        </tr>
                                        <tr>
                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                        market_segmentZ7
                                        </td>
                                        <td style="text-align:center;">

                                        - </td>
                                          <td style="text-align:center;">

                                          - </td>
                                            <td style="text-align:center;">
                                            0.7173
                                            </td>
                                            <td style="text-align:center;">
                                            0.001
                                            </td>
                                            <td style="text-align:center;">

                                            - </td>
                                              <td style="text-align:center;">

                                              - </td>
                                                <td style="text-align:center;">
                                                0.4814
                                                </td>
                                                <td style="text-align:center;">
                                                0.001
                                                </td>
                                                </tr>
                                                <tr>
                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                market_segmentZ8
                                                </td>
                                                <td style="text-align:center;">

                                                - </td>
                                                  <td style="text-align:center;">

                                                  - </td>
                                                    <td style="text-align:center;">
                                                    0.7399
                                                    </td>
                                                    <td style="text-align:center;">
                                                    0.001
                                                    </td>
                                                    <td style="text-align:center;">

                                                    - </td>
                                                      <td style="text-align:center;">

                                                      - </td>
                                                        <td style="text-align:center;">
                                                        0.4986
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.001
                                                        </td>
                                                        </tr>
                                                        <tr>
                                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                        market_segmentZ9
                                                        </td>
                                                        <td style="text-align:center;">

                                                        - </td>
                                                          <td style="text-align:center;">

                                                          - </td>
                                                            <td style="text-align:center;">
                                                            0.9617
                                                            </td>
                                                            <td style="text-align:center;">
                                                            0.001
                                                            </td>
                                                            <td style="text-align:center;">

                                                            - </td>
                                                              <td style="text-align:center;">

                                                              - </td>
                                                                <td style="text-align:center;">
                                                                0.6203
                                                                </td>
                                                                <td style="text-align:center;">
                                                                0.001
                                                                </td>
                                                                </tr>
                                                                <tr grouplength="1">
                                                                <td colspan="9" style="border-bottom: 1px solid;">
                                                                <strong>Spatial
                                                                lag
                                                                parameter</strong>
                                                                </td>
                                                                </tr>
                                                                <tr>
                                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                rho
                                                                </td>
                                                                <td style="text-align:center;">

                                                                - </td>
                                                                  <td style="text-align:center;">

                                                                  - </td>
                                                                    <td style="text-align:center;">

                                                                    - </td>
                                                                      <td style="text-align:center;">

                                                                      - </td>
                                                                        <td style="text-align:center;">
                                                                        0.4789
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        0.001
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        0.275
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        0.001
                                                                        </td>
                                                                        </tr>
                                                                        <tr grouplength="3">
                                                                        <td colspan="9" style="border-bottom: 1px solid;">
                                                                        <strong>Model
                                                                        diagnostics</strong>
                                                                        </td>
                                                                        </tr>
                                                                        <tr>
                                                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                        R-squared
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        0.78
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        0.88
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">

                                                                        - </td>
                                                                          <td style="text-align:center;">
                                                                          </td>
                                                                          <td style="text-align:center;">

                                                                          - </td>
                                                                            </tr>
                                                                            <tr>
                                                                            <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                            adj-R-squared:
                                                                            </td>
                                                                            <td style="text-align:center;">
                                                                            </td>
                                                                            <td style="text-align:center;">
                                                                            0.78
                                                                            </td>
                                                                            <td style="text-align:center;">
                                                                            </td>
                                                                            <td style="text-align:center;">
                                                                            0.88
                                                                            </td>
                                                                            <td style="text-align:center;">
                                                                            </td>
                                                                            <td style="text-align:center;">

                                                                            - </td>
                                                                              <td style="text-align:center;">
                                                                              </td>
                                                                              <td style="text-align:center;">

                                                                              - </td>
                                                                                </tr>
                                                                                <tr>
                                                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                                log-likelihood:
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                -12024.16
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                -2623.16
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                -4050.73
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                </td>
                                                                                <td style="text-align:center;">
                                                                                -338.92
                                                                                </td>
                                                                                </tr>
                                                                                </tbody>
                                                                                <tfoot>
                                                                                <tr>
                                                                                <td style="padding: 0; " colspan="100%">
                                                                                <span
                                                                                style="font-style: italic;">Note:
                                                                                </span>
                                                                                </td>
                                                                                </tr>
                                                                                <tr>
                                                                                <td style="padding: 0; " colspan="100%">
                                                                                <sup></sup>
                                                                                0.001
                                                                                in
                                                                                the
                                                                                p-values
                                                                                represents
                                                                                any
                                                                                value
                                                                                less
                                                                                than
                                                                                0.001
                                                                                </td>
                                                                                </tr>
                                                                                </tfoot>
                                                                                </table>

<table style="NAborder-bottom: 0;">
<caption>
Models Valencia (Dependent Variable is log of Price)
</caption>
<thead>
<tr>
<th style="empty-cells: hide;border-bottom:hidden;" colspan="1">
</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Base Model

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Base Model + MS

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Spatial Model

</div>

</th>
<th style="border-bottom:hidden;padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Spatial Model + MS

</div>

</th>
</tr>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Estimate
</th>
<th style="text-align:center;">
p-val
</th>
</tr>
</thead>
<tbody>
<tr grouplength="14">
<td colspan="9" style="border-bottom: 1px solid;">
<strong>Property attributes</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
(Intercept)
</td>
<td style="text-align:center;">
11.4979
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
10.8973
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
6.5688
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
7.1013
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
CONSTRUCTEDAREA
</td>
<td style="text-align:center;">
0.0066
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.006
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0052
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0051
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ROOMNUMBER
</td>
<td style="text-align:center;">
-0.0528
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0392
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0393
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0339
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
BATHNUMBER
</td>
<td style="text-align:center;">
0.1619
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1461
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.128
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1269
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASTERRACE
</td>
<td style="text-align:center;">
0.0832
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0872
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0723
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0756
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASLIFT
</td>
<td style="text-align:center;">
0.3022
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.3118
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.2231
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.2457
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASAIRCONDITIONING
</td>
<td style="text-align:center;">
0.1139
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1047
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.096
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.093
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASSWIMMINGPOOL
</td>
<td style="text-align:center;">
0.3738
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.3418
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1742
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1937
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISSTUDIO
</td>
<td style="text-align:center;">
-0.0519
</td>
<td style="text-align:center;">
0.1331
</td>
<td style="text-align:center;">
-0.0727
</td>
<td style="text-align:center;">
0.0206
</td>
<td style="text-align:center;">
-0.042
</td>
<td style="text-align:center;">
0.1493
</td>
<td style="text-align:center;">
-0.0589
</td>
<td style="text-align:center;">
0.0342
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISINTOPFLOOR
</td>
<td style="text-align:center;">
0.074
</td>
<td style="text-align:center;">
0.0023
</td>
<td style="text-align:center;">
0.092
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1104
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1157
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASPARKINGSPACE
</td>
<td style="text-align:center;">
0.1301
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1482
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.0961
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.1135
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CITY_CENTER
</td>
<td style="text-align:center;">
-0.1556
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.057
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0695
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0271
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_METRO
</td>
<td style="text-align:center;">
-0.1506
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.1061
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0651
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0585
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_BLASCO
</td>
<td style="text-align:center;">
-0.1263
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0806
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0679
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.0514
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr grouplength="7">
<td colspan="9" style="border-bottom: 1px solid;">
<strong>Market segments</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ2
</td>
<td style="text-align:center;">

- </td>
  <td style="text-align:center;">

  - </td>
    <td style="text-align:center;">
    0.2071
    </td>
    <td style="text-align:center;">
    0.001
    </td>
    <td style="text-align:center;">

    - </td>
      <td style="text-align:center;">

      - </td>
        <td style="text-align:center;">
        0.1018
        </td>
        <td style="text-align:center;">
        0.001
        </td>
        </tr>
        <tr>
        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
        market_segmentZ3
        </td>
        <td style="text-align:center;">

        - </td>
          <td style="text-align:center;">

          - </td>
            <td style="text-align:center;">
            0.3544
            </td>
            <td style="text-align:center;">
            0.001
            </td>
            <td style="text-align:center;">

            - </td>
              <td style="text-align:center;">

              - </td>
                <td style="text-align:center;">
                0.2164
                </td>
                <td style="text-align:center;">
                0.001
                </td>
                </tr>
                <tr>
                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                market_segmentZ4
                </td>
                <td style="text-align:center;">

                - </td>
                  <td style="text-align:center;">

                  - </td>
                    <td style="text-align:center;">
                    0.4348
                    </td>
                    <td style="text-align:center;">
                    0.001
                    </td>
                    <td style="text-align:center;">

                    - </td>
                      <td style="text-align:center;">

                      - </td>
                        <td style="text-align:center;">
                        0.2514
                        </td>
                        <td style="text-align:center;">
                        0.001
                        </td>
                        </tr>
                        <tr>
                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                        market_segmentZ5
                        </td>
                        <td style="text-align:center;">

                        - </td>
                          <td style="text-align:center;">

                          - </td>
                            <td style="text-align:center;">
                            0.3271
                            </td>
                            <td style="text-align:center;">
                            0.001
                            </td>
                            <td style="text-align:center;">

                            - </td>
                              <td style="text-align:center;">

                              - </td>
                                <td style="text-align:center;">
                                0.164
                                </td>
                                <td style="text-align:center;">
                                0.001
                                </td>
                                </tr>
                                <tr>
                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                market_segmentZ6
                                </td>
                                <td style="text-align:center;">

                                - </td>
                                  <td style="text-align:center;">

                                  - </td>
                                    <td style="text-align:center;">
                                    0.6119
                                    </td>
                                    <td style="text-align:center;">
                                    0.001
                                    </td>
                                    <td style="text-align:center;">

                                    - </td>
                                      <td style="text-align:center;">

                                      - </td>
                                        <td style="text-align:center;">
                                        0.3907
                                        </td>
                                        <td style="text-align:center;">
                                        0.001
                                        </td>
                                        </tr>
                                        <tr>
                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                        market_segmentZ7
                                        </td>
                                        <td style="text-align:center;">

                                        - </td>
                                          <td style="text-align:center;">

                                          - </td>
                                            <td style="text-align:center;">
                                            0.6346
                                            </td>
                                            <td style="text-align:center;">
                                            0.001
                                            </td>
                                            <td style="text-align:center;">

                                            - </td>
                                              <td style="text-align:center;">

                                              - </td>
                                                <td style="text-align:center;">
                                                0.3635
                                                </td>
                                                <td style="text-align:center;">
                                                0.001
                                                </td>
                                                </tr>
                                                <tr>
                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                market_segmentZ8
                                                </td>
                                                <td style="text-align:center;">

                                                - </td>
                                                  <td style="text-align:center;">

                                                  - </td>
                                                    <td style="text-align:center;">
                                                    0.7518
                                                    </td>
                                                    <td style="text-align:center;">
                                                    0.001
                                                    </td>
                                                    <td style="text-align:center;">

                                                    - </td>
                                                      <td style="text-align:center;">

                                                      - </td>
                                                        <td style="text-align:center;">
                                                        0.3814
                                                        </td>
                                                        <td style="text-align:center;">
                                                        0.001
                                                        </td>
                                                        </tr>
                                                        <tr grouplength="1">
                                                        <td colspan="9" style="border-bottom: 1px solid;">
                                                        <strong>Spatial
                                                        lag
                                                        parameter</strong>
                                                        </td>
                                                        </tr>
                                                        <tr>
                                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                        rho
                                                        </td>
                                                        <td style="text-align:center;">

                                                        - </td>
                                                          <td style="text-align:center;">

                                                          - </td>
                                                            <td style="text-align:center;">

                                                            - </td>
                                                              <td style="text-align:center;">

                                                              - </td>
                                                                <td style="text-align:center;">
                                                                0.4031
                                                                </td>
                                                                <td style="text-align:center;">
                                                                0.001
                                                                </td>
                                                                <td style="text-align:center;">
                                                                0.3314
                                                                </td>
                                                                <td style="text-align:center;">
                                                                0.001
                                                                </td>
                                                                </tr>
                                                                <tr grouplength="3">
                                                                <td colspan="9" style="border-bottom: 1px solid;">
                                                                <strong>Model
                                                                diagnostics</strong>
                                                                </td>
                                                                </tr>
                                                                <tr>
                                                                <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                R-squared
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">
                                                                0.79
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">
                                                                0.83
                                                                </td>
                                                                <td style="text-align:center;">
                                                                </td>
                                                                <td style="text-align:center;">

                                                                - </td>
                                                                  <td style="text-align:center;">
                                                                  </td>
                                                                  <td style="text-align:center;">

                                                                  - </td>
                                                                    </tr>
                                                                    <tr>
                                                                    <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                    adj-R-squared:
                                                                    </td>
                                                                    <td style="text-align:center;">
                                                                    </td>
                                                                    <td style="text-align:center;">
                                                                    0.79
                                                                    </td>
                                                                    <td style="text-align:center;">
                                                                    </td>
                                                                    <td style="text-align:center;">
                                                                    0.83
                                                                    </td>
                                                                    <td style="text-align:center;">
                                                                    </td>
                                                                    <td style="text-align:center;">

                                                                    - </td>
                                                                      <td style="text-align:center;">
                                                                      </td>
                                                                      <td style="text-align:center;">

                                                                      - </td>
                                                                        </tr>
                                                                        <tr>
                                                                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">
                                                                        log-likelihood:
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        -2282.65
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        -1343.2
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        -830.93
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        </td>
                                                                        <td style="text-align:center;">
                                                                        -449.46
                                                                        </td>
                                                                        </tr>
                                                                        </tbody>
                                                                        <tfoot>
                                                                        <tr>
                                                                        <td style="padding: 0; " colspan="100%">
                                                                        <span
                                                                        style="font-style: italic;">Note:
                                                                        </span>
                                                                        </td>
                                                                        </tr>
                                                                        <tr>
                                                                        <td style="padding: 0; " colspan="100%">
                                                                        <sup></sup>
                                                                        0.001
                                                                        in
                                                                        the
                                                                        p-values
                                                                        represents
                                                                        any
                                                                        value
                                                                        less
                                                                        than
                                                                        0.001
                                                                        </td>
                                                                        </tr>
                                                                        </tfoot>
                                                                        </table>

<table style="NAborder-bottom: 0;">
<caption>
Impacts Spatial Model + MS Barcelona (Dependent Variable is log of
Price)
</caption>
<thead>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:center;">
Direct
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Indirect
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Total
</th>
<th style="text-align:center;">
p-val
</th>
</tr>
</thead>
<tbody>
<tr grouplength="11">
<td colspan="7" style="border-bottom: 1px solid;">
<strong>Property attributes</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
CONSTRUCTEDAREA
</td>
<td style="text-align:center;">
0.005
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.002
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.007
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ROOMNUMBER
</td>
<td style="text-align:center;">
0.025
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.009
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.033
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
BATHNUMBER
</td>
<td style="text-align:center;">
0.101
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.035
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.137
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASTERRACE
</td>
<td style="text-align:center;">
0.076
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.026
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.102
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASLIFT
</td>
<td style="text-align:center;">
0.160
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.055
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.215
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASAIRCONDITIONING
</td>
<td style="text-align:center;">
0.103
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.036
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.139
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASSWIMMINGPOOL
</td>
<td style="text-align:center;">
0.128
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.044
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.173
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISSTUDIO
</td>
<td style="text-align:center;">
-0.241
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.084
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.324
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISINTOPFLOOR
</td>
<td style="text-align:center;">
0.046
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.016
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.062
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASPARKINGSPACE
</td>
<td style="text-align:center;">
0.059
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.021
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.080
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CITY_CENTER
</td>
<td style="text-align:center;">
-0.047
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.016
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.064
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr grouplength="6">
<td colspan="7" style="border-bottom: 1px solid;">
<strong>Market segments</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ2
</td>
<td style="text-align:center;">
0.089
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.031
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.120
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ3
</td>
<td style="text-align:center;">
0.171
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.059
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.231
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ4
</td>
<td style="text-align:center;">
0.221
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.077
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.298
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ5
</td>
<td style="text-align:center;">
0.240
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.083
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.324
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ6
</td>
<td style="text-align:center;">
0.277
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.096
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.373
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ7
</td>
<td style="text-align:center;">
0.257
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.089
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.346
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
</tbody>
<tfoot>
<tr>
<td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span>
</td>
</tr>
<tr>
<td style="padding: 0; " colspan="100%">
<sup></sup> 0.001 in the p-values represents any value less than 0.001
</td>
</tr>
</tfoot>
</table>
<table style="NAborder-bottom: 0;">
<caption>
Impacts Spatial Model + MS Madrid (Dependent Variable is log of Price)
</caption>
<thead>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:center;">
Direct
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Indirect
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Total
</th>
<th style="text-align:center;">
p-val
</th>
</tr>
</thead>
<tbody>
<tr grouplength="13">
<td colspan="7" style="border-bottom: 1px solid;">
<strong>Property attributes</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
CONSTRUCTEDAREA
</td>
<td style="text-align:center;">
0.004
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.005
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ROOMNUMBER
</td>
<td style="text-align:center;">
0.037
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.014
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.051
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
BATHNUMBER
</td>
<td style="text-align:center;">
0.096
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.036
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.132
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASTERRACE
</td>
<td style="text-align:center;">
0.044
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.016
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.060
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASLIFT
</td>
<td style="text-align:center;">
0.203
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.076
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.279
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASAIRCONDITIONING
</td>
<td style="text-align:center;">
0.085
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.031
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.116
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASSWIMMINGPOOL
</td>
<td style="text-align:center;">
0.130
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.048
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.178
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISSTUDIO
</td>
<td style="text-align:center;">
-0.168
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.062
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.230
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISINTOPFLOOR
</td>
<td style="text-align:center;">
0.030
</td>
<td style="text-align:center;">
0.002
</td>
<td style="text-align:center;">
0.011
</td>
<td style="text-align:center;">
0.002
</td>
<td style="text-align:center;">
0.041
</td>
<td style="text-align:center;">
0.002
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASPARKINGSPACE
</td>
<td style="text-align:center;">
0.092
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.034
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.126
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_METRO
</td>
<td style="text-align:center;">
-0.040
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.015
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.054
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CITY_CENTER
</td>
<td style="text-align:center;">
-0.040
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.015
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.054
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CASTELLANA
</td>
<td style="text-align:center;">
0.018
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.007
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.024
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr grouplength="8">
<td colspan="7" style="border-bottom: 1px solid;">
<strong>Market segments</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ2
</td>
<td style="text-align:center;">
0.047
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.018
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.065
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ3
</td>
<td style="text-align:center;">
0.165
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.061
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.227
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ4
</td>
<td style="text-align:center;">
0.340
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.127
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.467
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ5
</td>
<td style="text-align:center;">
0.352
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.131
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.483
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ6
</td>
<td style="text-align:center;">
0.368
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.137
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.505
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ7
</td>
<td style="text-align:center;">
0.484
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.180
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.664
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ8
</td>
<td style="text-align:center;">
0.501
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.186
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.688
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ9
</td>
<td style="text-align:center;">
0.624
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.232
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.856
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
</tbody>
<tfoot>
<tr>
<td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span>
</td>
</tr>
<tr>
<td style="padding: 0; " colspan="100%">
<sup></sup> 0.001 in the p-values represents any value less than 0.001
</td>
</tr>
</tfoot>
</table>
<table style="NAborder-bottom: 0;">
<caption>
Impacts Spatial Model + MS Valencia (Dependent Variable is log of Price)
</caption>
<thead>
<tr>
<th style="text-align:left;">
Variable
</th>
<th style="text-align:center;">
Direct
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Indirect
</th>
<th style="text-align:center;">
p-val
</th>
<th style="text-align:center;">
Total
</th>
<th style="text-align:center;">
p-val
</th>
</tr>
</thead>
<tbody>
<tr grouplength="13">
<td colspan="7" style="border-bottom: 1px solid;">
<strong>Property attributes</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
CONSTRUCTEDAREA
</td>
<td style="text-align:center;">
0.005
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.002
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.008
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ROOMNUMBER
</td>
<td style="text-align:center;">
-0.035
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.016
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.051
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
BATHNUMBER
</td>
<td style="text-align:center;">
0.130
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.060
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.190
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASTERRACE
</td>
<td style="text-align:center;">
0.078
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.035
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.113
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASLIFT
</td>
<td style="text-align:center;">
0.252
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.115
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.367
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASAIRCONDITIONING
</td>
<td style="text-align:center;">
0.095
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.044
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.139
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASSWIMMINGPOOL
</td>
<td style="text-align:center;">
0.199
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.091
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.290
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISSTUDIO
</td>
<td style="text-align:center;">
-0.060
</td>
<td style="text-align:center;">
0.041
</td>
<td style="text-align:center;">
-0.028
</td>
<td style="text-align:center;">
0.041
</td>
<td style="text-align:center;">
-0.088
</td>
<td style="text-align:center;">
0.041
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
ISINTOPFLOOR
</td>
<td style="text-align:center;">
0.119
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.054
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.173
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
HASPARKINGSPACE
</td>
<td style="text-align:center;">
0.116
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.053
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.170
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_CITY_CENTER
</td>
<td style="text-align:center;">
-0.028
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.013
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.040
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_METRO
</td>
<td style="text-align:center;">
-0.060
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.027
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.087
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
DISTANCE_TO_BLASCO
</td>
<td style="text-align:center;">
-0.053
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.024
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
-0.077
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr grouplength="7">
<td colspan="7" style="border-bottom: 1px solid;">
<strong>Market segments</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ2
</td>
<td style="text-align:center;">
0.104
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.048
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.152
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ3
</td>
<td style="text-align:center;">
0.222
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.102
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.324
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ4
</td>
<td style="text-align:center;">
0.258
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.118
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.376
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ5
</td>
<td style="text-align:center;">
0.168
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.077
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.245
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ6
</td>
<td style="text-align:center;">
0.401
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.183
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.584
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ7
</td>
<td style="text-align:center;">
0.373
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.171
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.544
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">
market_segmentZ8
</td>
<td style="text-align:center;">
0.391
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.179
</td>
<td style="text-align:center;">
0.001
</td>
<td style="text-align:center;">
0.570
</td>
<td style="text-align:center;">
0.001
</td>
</tr>
</tbody>
<tfoot>
<tr>
<td style="padding: 0; " colspan="100%">
<span style="font-style: italic;">Note: </span>
</td>
</tr>
<tr>
<td style="padding: 0; " colspan="100%">
<sup></sup> 0.001 in the p-values represents any value less than 0.001
</td>
</tr>
</tfoot>
</table>

## Predictive Performance: Comparison of Models

Prediction is a relevant concern in hedonic price analysis. Inspection
of the results in Tables , , and suggest that the introduction of
spatial market segments leads to markedly improved model fits. The
measures of performance reported in these tables are based on the
training sample exclusively. To conclude this investigation, in this
section the predictive performance of the models is compared based on
their performance using training (in-sample) as well as testing
(out-of-sample) data sets. It is important to recall at this point that
test data were not used in the calibration of the models discussed in
the preceding sections.

The models without a spatially lagged dependent variable assume that the
process is not spatially autocorrelated and therefore prediction
requires only observations of the exogenous explanatory variables for
the property to be assessed, since the price setting mechanism does not
include information about the neighbors. In contrast, prediction with
the models with a spatial lagged dependent variable require information
regarding neighboring dependent and explanatory variables. This
increases the data requirements and increase the computational
complexity of prediction. Several approaches to spatial prediction with
models that include spatially autocorrelated components are discussed in
the literature (e.g., Goulard, Laurent, and Thomas-Agnan 2017); these
are discussed briefly next.

In case of model () two types of prediction based on the data can be
considered: in- and out-of-sample predictions. In this paper we follow
Goulard, Laurent, and Thomas-Agnan (2017) proposal, as follows: we can
reorder the observations in equation () to obtain the block matrix form
below, where the subscript $S$ denote in-sample (training) data, and the
subscript $O$ out-of-sample (testing) data:

The *best predictor* (BP) approach is:

where
${\hat Q_{SS}}={1 \over \hat \sigma^2}(I-\hat \rho {W'_{SS}})(I - \hat \rho W´_{SS})$,
$\hat \rho$ is the in-sample spatial dependence estimate parameter and
$\hat \sigma^2$ is the estimate variance.

There are four alternatives for out-of-sample prediction:

Of the four out-of-sample prediction methods we use the Best Predictor
(BP) approach. Further detail on these alternatives can be found in
Goulard, Laurent, and Thomas-Agnan (2017). These prediction methods are
implemented in the R package **spatialreg** (Bivand et al. 2013).

We use several metrics of performance for comparison. Tables , , report
the mean absolute error (mae), median absolute prediction error (mdae),
root mean squared error (rmse), mean absolute prediction error (mape),
median absolute prediction error (medape), bias, percent bias (pc_bias)
and hit rates. The latter are the proportion of predictions smaller than
a given absolute deviation in percentage. For instance, the 5% hit rate
(hit_rate_5) of the linear model for Barcelona is a 98%, therefore 98%
of all observations have an absolute percent error smaller than a 5%.

The results indicate that adding market segments and/or a spatially
lagged variable improve the linear base model. The spatial model with
market segments is comparable to or better than the spatial model
without market segments. For example, the in- and out-of-sample
predictions in Valencia perform very similarly in these two models. In
Madrid and Valencia the results of the spatial model with market segment
are superior for both the in-sample and the out-of-sample predictions.

<table>
<caption>
Model performance comparison: Barcelona
</caption>
<thead>
<tr>
<th style="text-align:left;">
estimator
</th>
<th style="text-align:left;">
split
</th>
<th style="text-align:right;">
n
</th>
<th style="text-align:right;">
mae
</th>
<th style="text-align:right;">
mdae
</th>
<th style="text-align:right;">
rmse
</th>
<th style="text-align:right;">
mape
</th>
<th style="text-align:right;">
medape
</th>
<th style="text-align:right;">
bias
</th>
<th style="text-align:right;">
pc_bias
</th>
<th style="text-align:right;">
hit_ratio_5
</th>
<th style="text-align:right;">
hit_ratio_10
</th>
</tr>
</thead>
<tbody>
<tr grouplength="2">
<td colspan="12" style="border-bottom: 1px solid;">
<strong>Base Model</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">

1.  Base LM
    </td>
    <td style="text-align:left;">
    Train
    </td>
    <td style="text-align:right;">
    16325
    </td>
    <td style="text-align:right;">
    0.191
    </td>
    <td style="text-align:right;">
    0.152
    </td>
    <td style="text-align:right;">
    0.254
    </td>
    <td style="text-align:right;">
    0.015
    </td>
    <td style="text-align:right;">
    0.012
    </td>
    <td style="text-align:right;">
    0.000
    </td>
    <td style="text-align:right;">
    0
    </td>
    <td style="text-align:right;">
    0.980
    </td>
    <td style="text-align:right;">
    0.999
    </td>
    </tr>
    <tr>
    <td style="text-align:left;padding-left: 2em;" indentlevel="1">

    1.  Base LM
        </td>
        <td style="text-align:left;">
        Test
        </td>
        <td style="text-align:right;">
        6997
        </td>
        <td style="text-align:right;">
        0.196
        </td>
        <td style="text-align:right;">
        0.152
        </td>
        <td style="text-align:right;">
        0.266
        </td>
        <td style="text-align:right;">
        0.015
        </td>
        <td style="text-align:right;">
        0.012
        </td>
        <td style="text-align:right;">
        0.002
        </td>
        <td style="text-align:right;">
        0
        </td>
        <td style="text-align:right;">
        0.977
        </td>
        <td style="text-align:right;">
        0.998
        </td>
        </tr>
        <tr grouplength="2">
        <td colspan="12" style="border-bottom: 1px solid;">
        <strong>Base Model + Market Segments</strong>
        </td>
        </tr>
        <tr>
        <td style="text-align:left;padding-left: 2em;" indentlevel="1">

        2.  LM MS
            </td>
            <td style="text-align:left;">
            Train
            </td>
            <td style="text-align:right;">
            16325
            </td>
            <td style="text-align:right;">
            0.179
            </td>
            <td style="text-align:right;">
            0.142
            </td>
            <td style="text-align:right;">
            0.238
            </td>
            <td style="text-align:right;">
            0.014
            </td>
            <td style="text-align:right;">
            0.011
            </td>
            <td style="text-align:right;">
            0.000
            </td>
            <td style="text-align:right;">
            0
            </td>
            <td style="text-align:right;">
            0.983
            </td>
            <td style="text-align:right;">
            0.999
            </td>
            </tr>
            <tr>
            <td style="text-align:left;padding-left: 2em;" indentlevel="1">

            2.  LM MS
                </td>
                <td style="text-align:left;">
                Test
                </td>
                <td style="text-align:right;">
                6997
                </td>
                <td style="text-align:right;">
                0.184
                </td>
                <td style="text-align:right;">
                0.144
                </td>
                <td style="text-align:right;">
                0.249
                </td>
                <td style="text-align:right;">
                0.014
                </td>
                <td style="text-align:right;">
                0.011
                </td>
                <td style="text-align:right;">
                0.002
                </td>
                <td style="text-align:right;">
                0
                </td>
                <td style="text-align:right;">
                0.981
                </td>
                <td style="text-align:right;">
                0.998
                </td>
                </tr>
                <tr grouplength="2">
                <td colspan="12" style="border-bottom: 1px solid;">
                <strong>Spatial Model</strong>
                </td>
                </tr>
                <tr>
                <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                3.  Spatial Model (BP)
                    </td>
                    <td style="text-align:left;">
                    Train
                    </td>
                    <td style="text-align:right;">
                    16325
                    </td>
                    <td style="text-align:right;">
                    0.162
                    </td>
                    <td style="text-align:right;">
                    0.126
                    </td>
                    <td style="text-align:right;">
                    0.218
                    </td>
                    <td style="text-align:right;">
                    0.013
                    </td>
                    <td style="text-align:right;">
                    0.010
                    </td>
                    <td style="text-align:right;">
                    0.000
                    </td>
                    <td style="text-align:right;">
                    0
                    </td>
                    <td style="text-align:right;">
                    0.986
                    </td>
                    <td style="text-align:right;">
                    0.999
                    </td>
                    </tr>
                    <tr>
                    <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                    3.  Spatial Model (BP)
                        </td>
                        <td style="text-align:left;">
                        Test
                        </td>
                        <td style="text-align:right;">
                        6997
                        </td>
                        <td style="text-align:right;">
                        0.168
                        </td>
                        <td style="text-align:right;">
                        0.130
                        </td>
                        <td style="text-align:right;">
                        0.229
                        </td>
                        <td style="text-align:right;">
                        0.013
                        </td>
                        <td style="text-align:right;">
                        0.010
                        </td>
                        <td style="text-align:right;">
                        0.002
                        </td>
                        <td style="text-align:right;">
                        0
                        </td>
                        <td style="text-align:right;">
                        0.984
                        </td>
                        <td style="text-align:right;">
                        0.999
                        </td>
                        </tr>
                        <tr grouplength="2">
                        <td colspan="12" style="border-bottom: 1px solid;">
                        <strong>Spatial Model + Market Segments</strong>
                        </td>
                        </tr>
                        <tr>
                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                        4.  Spatial Model + MS (BP)
                            </td>
                            <td style="text-align:left;">
                            Train
                            </td>
                            <td style="text-align:right;">
                            16325
                            </td>
                            <td style="text-align:right;">
                            0.161
                            </td>
                            <td style="text-align:right;">
                            0.124
                            </td>
                            <td style="text-align:right;">
                            0.216
                            </td>
                            <td style="text-align:right;">
                            0.013
                            </td>
                            <td style="text-align:right;">
                            0.010
                            </td>
                            <td style="text-align:right;">
                            0.000
                            </td>
                            <td style="text-align:right;">
                            0
                            </td>
                            <td style="text-align:right;">
                            0.987
                            </td>
                            <td style="text-align:right;">
                            0.999
                            </td>
                            </tr>
                            <tr>
                            <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                            4.  Spatial Model + MS (BP)
                                </td>
                                <td style="text-align:left;">
                                Test
                                </td>
                                <td style="text-align:right;">
                                6997
                                </td>
                                <td style="text-align:right;">
                                0.167
                                </td>
                                <td style="text-align:right;">
                                0.130
                                </td>
                                <td style="text-align:right;">
                                0.227
                                </td>
                                <td style="text-align:right;">
                                0.013
                                </td>
                                <td style="text-align:right;">
                                0.010
                                </td>
                                <td style="text-align:right;">
                                0.002
                                </td>
                                <td style="text-align:right;">
                                0
                                </td>
                                <td style="text-align:right;">
                                0.984
                                </td>
                                <td style="text-align:right;">
                                0.999
                                </td>
                                </tr>
                                </tbody>
                                </table>

<table>
<caption>
Model performance comparison: Madrid
</caption>
<thead>
<tr>
<th style="text-align:left;">
estimator
</th>
<th style="text-align:left;">
split
</th>
<th style="text-align:right;">
n
</th>
<th style="text-align:right;">
mae
</th>
<th style="text-align:right;">
mdae
</th>
<th style="text-align:right;">
rmse
</th>
<th style="text-align:right;">
mape
</th>
<th style="text-align:right;">
medape
</th>
<th style="text-align:right;">
bias
</th>
<th style="text-align:right;">
pc_bias
</th>
<th style="text-align:right;">
hit_ratio_5
</th>
<th style="text-align:right;">
hit_ratio_10
</th>
</tr>
</thead>
<tbody>
<tr grouplength="2">
<td colspan="12" style="border-bottom: 1px solid;">
<strong>Base Model</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">

1.  Base LM
    </td>
    <td style="text-align:left;">
    Train
    </td>
    <td style="text-align:right;">
    30976
    </td>
    <td style="text-align:right;">
    0.278
    </td>
    <td style="text-align:right;">
    0.229
    </td>
    <td style="text-align:right;">
    0.357
    </td>
    <td style="text-align:right;">
    0.022
    </td>
    <td style="text-align:right;">
    0.018
    </td>
    <td style="text-align:right;">
    0.000
    </td>
    <td style="text-align:right;">
    -0.001
    </td>
    <td style="text-align:right;">
    0.924
    </td>
    <td style="text-align:right;">
    0.998
    </td>
    </tr>
    <tr>
    <td style="text-align:left;padding-left: 2em;" indentlevel="1">

    1.  Base LM
        </td>
        <td style="text-align:left;">
        Test
        </td>
        <td style="text-align:right;">
        13275
        </td>
        <td style="text-align:right;">
        0.276
        </td>
        <td style="text-align:right;">
        0.227
        </td>
        <td style="text-align:right;">
        0.354
        </td>
        <td style="text-align:right;">
        0.022
        </td>
        <td style="text-align:right;">
        0.018
        </td>
        <td style="text-align:right;">
        -0.003
        </td>
        <td style="text-align:right;">
        -0.001
        </td>
        <td style="text-align:right;">
        0.928
        </td>
        <td style="text-align:right;">
        0.997
        </td>
        </tr>
        <tr grouplength="2">
        <td colspan="12" style="border-bottom: 1px solid;">
        <strong>Base Model + Market Segments</strong>
        </td>
        </tr>
        <tr>
        <td style="text-align:left;padding-left: 2em;" indentlevel="1">

        2.  LM MS
            </td>
            <td style="text-align:left;">
            Train
            </td>
            <td style="text-align:right;">
            30976
            </td>
            <td style="text-align:right;">
            0.199
            </td>
            <td style="text-align:right;">
            0.157
            </td>
            <td style="text-align:right;">
            0.263
            </td>
            <td style="text-align:right;">
            0.016
            </td>
            <td style="text-align:right;">
            0.013
            </td>
            <td style="text-align:right;">
            0.000
            </td>
            <td style="text-align:right;">
            0.000
            </td>
            <td style="text-align:right;">
            0.976
            </td>
            <td style="text-align:right;">
            0.999
            </td>
            </tr>
            <tr>
            <td style="text-align:left;padding-left: 2em;" indentlevel="1">

            2.  LM MS
                </td>
                <td style="text-align:left;">
                Test
                </td>
                <td style="text-align:right;">
                13275
                </td>
                <td style="text-align:right;">
                0.199
                </td>
                <td style="text-align:right;">
                0.156
                </td>
                <td style="text-align:right;">
                0.266
                </td>
                <td style="text-align:right;">
                0.016
                </td>
                <td style="text-align:right;">
                0.012
                </td>
                <td style="text-align:right;">
                -0.002
                </td>
                <td style="text-align:right;">
                -0.001
                </td>
                <td style="text-align:right;">
                0.975
                </td>
                <td style="text-align:right;">
                0.998
                </td>
                </tr>
                <tr grouplength="2">
                <td colspan="12" style="border-bottom: 1px solid;">
                <strong>Spatial Model</strong>
                </td>
                </tr>
                <tr>
                <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                3.  Spatial Model (BP)
                    </td>
                    <td style="text-align:left;">
                    Train
                    </td>
                    <td style="text-align:right;">
                    30976
                    </td>
                    <td style="text-align:right;">
                    0.181
                    </td>
                    <td style="text-align:right;">
                    0.136
                    </td>
                    <td style="text-align:right;">
                    0.246
                    </td>
                    <td style="text-align:right;">
                    0.014
                    </td>
                    <td style="text-align:right;">
                    0.011
                    </td>
                    <td style="text-align:right;">
                    0.000
                    </td>
                    <td style="text-align:right;">
                    0.000
                    </td>
                    <td style="text-align:right;">
                    0.978
                    </td>
                    <td style="text-align:right;">
                    0.999
                    </td>
                    </tr>
                    <tr>
                    <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                    3.  Spatial Model (BP)
                        </td>
                        <td style="text-align:left;">
                        Test
                        </td>
                        <td style="text-align:right;">
                        13275
                        </td>
                        <td style="text-align:right;">
                        0.190
                        </td>
                        <td style="text-align:right;">
                        0.143
                        </td>
                        <td style="text-align:right;">
                        0.259
                        </td>
                        <td style="text-align:right;">
                        0.015
                        </td>
                        <td style="text-align:right;">
                        0.011
                        </td>
                        <td style="text-align:right;">
                        -0.002
                        </td>
                        <td style="text-align:right;">
                        -0.001
                        </td>
                        <td style="text-align:right;">
                        0.974
                        </td>
                        <td style="text-align:right;">
                        0.999
                        </td>
                        </tr>
                        <tr grouplength="2">
                        <td colspan="12" style="border-bottom: 1px solid;">
                        <strong>Spatial Model + Market Segments</strong>
                        </td>
                        </tr>
                        <tr>
                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                        4.  Spatial Model + MS (BP)
                            </td>
                            <td style="text-align:left;">
                            Train
                            </td>
                            <td style="text-align:right;">
                            30976
                            </td>
                            <td style="text-align:right;">
                            0.172
                            </td>
                            <td style="text-align:right;">
                            0.130
                            </td>
                            <td style="text-align:right;">
                            0.233
                            </td>
                            <td style="text-align:right;">
                            0.014
                            </td>
                            <td style="text-align:right;">
                            0.010
                            </td>
                            <td style="text-align:right;">
                            0.000
                            </td>
                            <td style="text-align:right;">
                            0.000
                            </td>
                            <td style="text-align:right;">
                            0.983
                            </td>
                            <td style="text-align:right;">
                            0.999
                            </td>
                            </tr>
                            <tr>
                            <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                            4.  Spatial Model + MS (BP)
                                </td>
                                <td style="text-align:left;">
                                Test
                                </td>
                                <td style="text-align:right;">
                                13275
                                </td>
                                <td style="text-align:right;">
                                0.176
                                </td>
                                <td style="text-align:right;">
                                0.133
                                </td>
                                <td style="text-align:right;">
                                0.242
                                </td>
                                <td style="text-align:right;">
                                0.014
                                </td>
                                <td style="text-align:right;">
                                0.011
                                </td>
                                <td style="text-align:right;">
                                -0.002
                                </td>
                                <td style="text-align:right;">
                                -0.001
                                </td>
                                <td style="text-align:right;">
                                0.980
                                </td>
                                <td style="text-align:right;">
                                0.998
                                </td>
                                </tr>
                                </tbody>
                                </table>

<table>
<caption>
Model performance comparison: Valencia
</caption>
<thead>
<tr>
<th style="text-align:left;">
estimator
</th>
<th style="text-align:left;">
split
</th>
<th style="text-align:right;">
n
</th>
<th style="text-align:right;">
mae
</th>
<th style="text-align:right;">
mdae
</th>
<th style="text-align:right;">
rmse
</th>
<th style="text-align:right;">
mape
</th>
<th style="text-align:right;">
medape
</th>
<th style="text-align:right;">
bias
</th>
<th style="text-align:right;">
pc_bias
</th>
<th style="text-align:right;">
hit_ratio_5
</th>
<th style="text-align:right;">
hit_ratio_10
</th>
</tr>
</thead>
<tbody>
<tr grouplength="2">
<td colspan="12" style="border-bottom: 1px solid;">
<strong>Base Model</strong>
</td>
</tr>
<tr>
<td style="text-align:left;padding-left: 2em;" indentlevel="1">

1.  Base LM
    </td>
    <td style="text-align:left;">
    Train
    </td>
    <td style="text-align:right;">
    9802
    </td>
    <td style="text-align:right;">
    0.235
    </td>
    <td style="text-align:right;">
    0.188
    </td>
    <td style="text-align:right;">
    0.305
    </td>
    <td style="text-align:right;">
    0.020
    </td>
    <td style="text-align:right;">
    0.016
    </td>
    <td style="text-align:right;">
    0.000
    </td>
    <td style="text-align:right;">
    -0.001
    </td>
    <td style="text-align:right;">
    0.950
    </td>
    <td style="text-align:right;">
    0.998
    </td>
    </tr>
    <tr>
    <td style="text-align:left;padding-left: 2em;" indentlevel="1">

    1.  Base LM
        </td>
        <td style="text-align:left;">
        Test
        </td>
        <td style="text-align:right;">
        4201
        </td>
        <td style="text-align:right;">
        0.237
        </td>
        <td style="text-align:right;">
        0.189
        </td>
        <td style="text-align:right;">
        0.311
        </td>
        <td style="text-align:right;">
        0.020
        </td>
        <td style="text-align:right;">
        0.016
        </td>
        <td style="text-align:right;">
        -0.001
        </td>
        <td style="text-align:right;">
        -0.001
        </td>
        <td style="text-align:right;">
        0.943
        </td>
        <td style="text-align:right;">
        0.998
        </td>
        </tr>
        <tr grouplength="2">
        <td colspan="12" style="border-bottom: 1px solid;">
        <strong>Base Model + Market Segments</strong>
        </td>
        </tr>
        <tr>
        <td style="text-align:left;padding-left: 2em;" indentlevel="1">

        2.  LM MS
            </td>
            <td style="text-align:left;">
            Train
            </td>
            <td style="text-align:right;">
            9802
            </td>
            <td style="text-align:right;">
            0.212
            </td>
            <td style="text-align:right;">
            0.170
            </td>
            <td style="text-align:right;">
            0.278
            </td>
            <td style="text-align:right;">
            0.018
            </td>
            <td style="text-align:right;">
            0.014
            </td>
            <td style="text-align:right;">
            0.000
            </td>
            <td style="text-align:right;">
            -0.001
            </td>
            <td style="text-align:right;">
            0.965
            </td>
            <td style="text-align:right;">
            0.999
            </td>
            </tr>
            <tr>
            <td style="text-align:left;padding-left: 2em;" indentlevel="1">

            2.  LM MS
                </td>
                <td style="text-align:left;">
                Test
                </td>
                <td style="text-align:right;">
                4201
                </td>
                <td style="text-align:right;">
                0.216
                </td>
                <td style="text-align:right;">
                0.176
                </td>
                <td style="text-align:right;">
                0.285
                </td>
                <td style="text-align:right;">
                0.018
                </td>
                <td style="text-align:right;">
                0.015
                </td>
                <td style="text-align:right;">
                0.000
                </td>
                <td style="text-align:right;">
                -0.001
                </td>
                <td style="text-align:right;">
                0.963
                </td>
                <td style="text-align:right;">
                0.998
                </td>
                </tr>
                <tr grouplength="2">
                <td colspan="12" style="border-bottom: 1px solid;">
                <strong>Spatial Model</strong>
                </td>
                </tr>
                <tr>
                <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                3.  Spatial Model (BP)
                    </td>
                    <td style="text-align:left;">
                    Train
                    </td>
                    <td style="text-align:right;">
                    9802
                    </td>
                    <td style="text-align:right;">
                    0.185
                    </td>
                    <td style="text-align:right;">
                    0.143
                    </td>
                    <td style="text-align:right;">
                    0.247
                    </td>
                    <td style="text-align:right;">
                    0.015
                    </td>
                    <td style="text-align:right;">
                    0.012
                    </td>
                    <td style="text-align:right;">
                    0.000
                    </td>
                    <td style="text-align:right;">
                    0.000
                    </td>
                    <td style="text-align:right;">
                    0.972
                    </td>
                    <td style="text-align:right;">
                    0.999
                    </td>
                    </tr>
                    <tr>
                    <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                    3.  Spatial Model (BP)
                        </td>
                        <td style="text-align:left;">
                        Test
                        </td>
                        <td style="text-align:right;">
                        4201
                        </td>
                        <td style="text-align:right;">
                        0.194
                        </td>
                        <td style="text-align:right;">
                        0.150
                        </td>
                        <td style="text-align:right;">
                        0.258
                        </td>
                        <td style="text-align:right;">
                        0.016
                        </td>
                        <td style="text-align:right;">
                        0.013
                        </td>
                        <td style="text-align:right;">
                        0.002
                        </td>
                        <td style="text-align:right;">
                        0.000
                        </td>
                        <td style="text-align:right;">
                        0.974
                        </td>
                        <td style="text-align:right;">
                        0.999
                        </td>
                        </tr>
                        <tr grouplength="2">
                        <td colspan="12" style="border-bottom: 1px solid;">
                        <strong>Spatial Model + Market Segments</strong>
                        </td>
                        </tr>
                        <tr>
                        <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                        4.  Spatial Model + MS (BP)
                            </td>
                            <td style="text-align:left;">
                            Train
                            </td>
                            <td style="text-align:right;">
                            9802
                            </td>
                            <td style="text-align:right;">
                            0.183
                            </td>
                            <td style="text-align:right;">
                            0.143
                            </td>
                            <td style="text-align:right;">
                            0.243
                            </td>
                            <td style="text-align:right;">
                            0.015
                            </td>
                            <td style="text-align:right;">
                            0.012
                            </td>
                            <td style="text-align:right;">
                            0.000
                            </td>
                            <td style="text-align:right;">
                            0.000
                            </td>
                            <td style="text-align:right;">
                            0.976
                            </td>
                            <td style="text-align:right;">
                            0.999
                            </td>
                            </tr>
                            <tr>
                            <td style="text-align:left;padding-left: 2em;" indentlevel="1">

                            4.  Spatial Model + MS (BP)
                                </td>
                                <td style="text-align:left;">
                                Test
                                </td>
                                <td style="text-align:right;">
                                4201
                                </td>
                                <td style="text-align:right;">
                                0.191
                                </td>
                                <td style="text-align:right;">
                                0.148
                                </td>
                                <td style="text-align:right;">
                                0.253
                                </td>
                                <td style="text-align:right;">
                                0.016
                                </td>
                                <td style="text-align:right;">
                                0.012
                                </td>
                                <td style="text-align:right;">
                                0.002
                                </td>
                                <td style="text-align:right;">
                                0.000
                                </td>
                                <td style="text-align:right;">
                                0.976
                                </td>
                                <td style="text-align:right;">
                                0.999
                                </td>
                                </tr>
                                </tbody>
                                </table>

# Conclusions

Market segmentation is a topic of interest in the literature on real
estate appraisal and valuation. In addition to being conceptually sound,
numerous studies throughout the years have demonstrated that the
practice of identifying market segments for hedonic price analysis can
lead to higher quality models and enhanced performance.

The contribution of this paper has been to demonstrate a modelling
strategy to obtain flexible tree-based market segments for use in
spatial hedonic price modeling. Implementation of regression trees for
market segmentation was proposed in a recent paper by Füss and Koller
(2016). Our modelling strategy differs to the one proposed by these
authors in two respects: 1) the use of decision trees with flexible
(i.e. non-orthogonal and possibly non-linear market boundaries); and 2)
the timing of the estimations of the market segments, which in the case
of Füss and Koller (2016) is based on the residuals of an initial
regression model, whereas in our case it is done in the first step of
the modelling strategy.

The results using three large data sets from cities in Spain indicate
that modelling the market segments can improve the fit of the models, as
well as their predictive performance. The best model consistently
included a spatially lagged dependent variable and market segments. The
market segments in addition to improving the fit and the predictive
performance also reduced the magnitude of the spatial lag parameter,
thus allocating some of the spatial effect to regional heterogeneity
that would otherwise be assumed to be micro-scale information
spillovers. Overall, the results serve to demonstrate the potential of
the proposed modelling strategy to produce better models and more
accurate predictions.

One direction for future research is to investigate the temporal
stability of spatial market segments. It is well known that there are
seasonal effects in housing markets, but an open research question is
whether spatial market segments experience seasonal variations, both in
terms of their geographical extent as well as the magnitude of their
effects. Another possibility is that there are longer term trends (e.g.,
gentrification) that could affect the spatial configuration of the
market segments. Both seasonality and/or longer term trends would
require multi-year data sets, compared to the single-year data set that
we used for this research. For the time being, it is important to note
that the results presented in this paper support the argument that the
two-step method described in this paper performs well for now-casting or
relatively short term forecasts. Given the dearth of information about
seasonality and temporal stability of spatial market segments, any
attempt to use them for longer term forecasts should be done with
caution.

Finally, the study was designed as an example of reproducible research:
all code and data used in this research is publicly available which
should allow other researchers reproduce our results or expand them in
other directions.

# Supplemental material

Supplemental material for this article is
[available](https://github.com/paezha/Machine-learning-for-market-segmentation/paper).

# References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-Alonso1964location" class="csl-entry">

Alonso, William. 1964. *Location and Land Use*. Book. Cambridge: Harvard
University Press.

</div>

<div id="ref-arribas2021open" class="csl-entry">

Arribas-Bel, Dani, Mark Green, Francisco Rowe, and Alex Singleton. 2021.
“Open Data Products-a Framework for Creating Valuable Analysis Ready
Data.” Journal Article. *Journal of Geographical Systems* 23 (4):
497–514.

</div>

<div id="ref-bivand2013" class="csl-entry">

Bivand, Roger S, Edzer J Pebesma, Virgilio Gomez-Rubio, and Edzer Jan
Pebesma. 2013. *Applied Spatial Data Analysis with r*. Vol. 2. Springer.

</div>

<div id="ref-Bourassa2007spatial" class="csl-entry">

Bourassa, S. C., E. Cantoni, and M. Hoesh. 2007. “Spatial Dependence,
Housing Submarkets, and House Price Prediction.” Journal Article.
*Journal of Real Estate Finance and Economics* 35 (2): 143–60.

</div>

<div id="ref-Cellmer2019application" class="csl-entry">

Cellmer, R., K. Kobylinska, and M. Belej. 2019. “Application of
Hierarchical Spatial Autoregressive Models to Develop Land Value Maps in
Urbanized Areas.” Journal Article. *Isprs International Journal of
Geo-Information* 8 (4): 20.

</div>

<div id="ref-chasco2018" class="csl-entry">

Chasco, Coro, Julie Le Gallo, and Fernando A López. 2018. “A Scan Test
for Spatial Groupwise Heteroscedasticity in Cross-Sectional Models with
an Application on Houses Prices in Madrid.” *Regional Science and Urban
Economics* 68: 226–38.

</div>

<div id="ref-du2019techniques" class="csl-entry">

Du, Mengnan, Ninghao Liu, and Xia Hu. 2019. “Techniques for
Interpretable Machine Learning.” Journal Article. *Communications of the
ACM* 63 (1): 68–77. <https://doi.org/10.1145/3359786>.

</div>

<div id="ref-Fuss2016role" class="csl-entry">

Füss, R., and J. A. Koller. 2016. “The Role of Spatial and Temporal
Structure for Residential Rent Predictions.” Journal Article.
*International Journal of Forecasting* 32 (4): 1352–68.

</div>

<div id="ref-Galster1996william" class="csl-entry">

Galster, G. 1996. “William Grigsby and the Analysis of Housing
Sub-Markets and Filtering.” Journal Article. *Urban Studies* 33 (10):
1797–1805.

</div>

<div id="ref-goulard2017" class="csl-entry">

Goulard, Michel, Thibault Laurent, and Christine Thomas-Agnan. 2017.
“About Predictions in Spatial Autoregressive Models: Optimal and Almost
Optimal Strategies.” *Spatial Economic Analysis* 12 (2-3): 304–25.

</div>

<div id="ref-vonGraevenitz2018amenity" class="csl-entry">

Graevenitz, K. von. 2018. “The Amenity Cost of Road Noise.” Journal
Article. *Journal of Environmental Economics and Management* 90: 1–22.

</div>

<div id="ref-Helbich2013data" class="csl-entry">

Helbich, M., W. Brunauer, J. Hagenauer, and M. Leitner. 2013.
“Data-Driven Regionalization of Housing Markets.” Journal Article.
*Annals of the Association of American Geographers* 103 (4): 871–89.

</div>

<div id="ref-Helbich2016spatially" class="csl-entry">

Helbich, M., and D. A. Griffith. 2016. “Spatially Varying Coefficient
Models in Real Estate: Eigenvector Spatial Filtering and Alternative
Approaches.” Journal Article. *Computers Environment and Urban Systems*
57: 1–11.

</div>

<div id="ref-Hu2019monitoring" class="csl-entry">

Hu, L. R., S. J. He, Z. X. Han, H. Xiao, S. L. Su, M. Weng, and Z. L.
Cai. 2019. “Monitoring Housing Rental Prices Based on Social Media:an
Integrated Approach of Machine-Learning Algorithms and Hedonic Modeling
to Inform Equitable Housing Policies.” Journal Article. *Land Use
Policy* 82: 657–73.

</div>

<div id="ref-Humphreys2019superstition" class="csl-entry">

Humphreys, B. R., A. Nowak, and Y. Zhou. 2019. “Superstition and Real
Estate Prices: Transaction-Level Evidence from the US Housing Market.”
Journal Article. *Applied Economics* 51 (26): 2818–41.

</div>

<div id="ref-Hwang2009delineating" class="csl-entry">

Hwang, S., and J. C. Thill. 2009. “Delineating Urban Housing Submarkets
with Fuzzy Clustering.” Journal Article. *Environment and Planning
B-Planning & Design* 36 (5): 865–82.

</div>

<div id="ref-Islam2011addressing" class="csl-entry">

Islam, K. S., and Y. Asami. 2011. “Addressing Structural Instability in
Housing Market Segmentation of the Used Houses of Tokyo, Japan.” Book
Section. In *International Conference: Spatial Thinking and Geographic
Information Sciences 2011*, edited by Y. Asami. Vol. 21. Procedia Social
and Behavioral Sciences. Amsterdam: Elsevier Science Bv.

</div>

<div id="ref-James2013introduction" class="csl-entry">

James, Gareth, Daniela Witten, Trevor Hastie, and Robert Tibshirani.
2013. *An Introduction to Statistical Learning*. Vol. 112. Springer.

</div>

<div id="ref-knaus2021machine" class="csl-entry">

Knaus, Michael C., Michael Lechner, and Anthony Strittmatter. 2021.
“Machine Learning Estimation of Heterogeneous Causal Effects: Empirical
Monte Carlo Evidence.” Journal Article. *The Econometrics Journal* 24
(1): 134–61. <https://doi.org/10.1093/ectj/utaa014>.

</div>

<div id="ref-lopez2020" class="csl-entry">

Lopez, Fernando, Roman Minguez, and Jesus Mur. 2020. “ML Versus IV
Estimates of Spatial SUR Models: Evidence from the Case of Airbnb in
Madrid Urban Area.” *Annals of Regional Science* 64 (2): 313–47.

</div>

<div id="ref-lopez2015" class="csl-entry">

López, Fernando A, Coro Chasco, and Julie Le Gallo. 2015. “Exploring
Scan Methods to Test Spatial Structure with an Application to Housing
Prices in Madrid.” *Papers in Regional Science* 94 (2): 317–46.

</div>

<div id="ref-Montero2018estimating" class="csl-entry">

Montero, J. M., G. Fernandez-Aviles, and R. Minguez. 2018. “Estimating
Environment Impacts on Housing Prices.” Journal Article.
*Environmetrics* 29 (5-6): 12. <https://doi.org/10.1002/env.2453>.

</div>

<div id="ref-Montero2009estimating" class="csl-entry">

Montero-Lorenzo, J. M., B. Larraz-Iribas, and A. Paez. 2009. “Estimating
Commercial Property Prices: An Application of Cokriging with Housing
Prices as Ancillary Information.” Journal Article. *Journal of
Geographical Systems* 11 (4): 407–25.

</div>

<div id="ref-Morillo2017application" class="csl-entry">

Morillo, M. C., F. G. Cepeda, and S. Martinez-Cuevas. 2017. “The
Application of Spatial Analysis to Cadastral Zoning of Urban Areas: An
Example in the City of Madrid.” Journal Article. *Survey Review* 49
(353): 83–92. <https://doi.org/10.1080/00396265.2015.1113029>.

</div>

<div id="ref-murdoch2019definitions" class="csl-entry">

Murdoch, W. James, Chandan Singh, Karl Kumbier, Reza Abbasi-Asl, and Bin
Yu. 2019. “Definitions, Methods, and Applications in Interpretable
Machine Learning.” Journal Article. *Proceedings of the National Academy
of Sciences* 116 (44): 22071–80.
<https://doi.org/10.1073/pnas.1900654116>.

</div>

<div id="ref-Nowak2018homeowner" class="csl-entry">

Nowak, A., and J. Sayago-Gomez. 2018. “Homeowner Preferences After
September 11th, a Microdata Approach.” Journal Article. *Regional
Science and Urban Economics* 70: 330–51.
<https://doi.org/10.1016/j.regsciurbeco.2017.10.001>.

</div>

<div id="ref-Pace1998spatiotemporal" class="csl-entry">

Pace, R. K., R. Barry, J. M. Clapp, and M. Rodriquez. 1998.
“Spatiotemporal Autoregressive Models of Neighborhood Effects.” Journal
Article. *Journal of Real Estate Finance and Economics* 17 (1): 15–33.

</div>

<div id="ref-Pace1997spatial" class="csl-entry">

Pace, R. K., and O. W. Gilley. 1997. “Using the Spatial Configuration of
the Data to Improve Estimation.” Journal Article. *Journal of Real
Estate Finance and Economics* 14 (3): 333–40.

</div>

<div id="ref-Paez2009recent" class="csl-entry">

Paez, A. 2009. “Recent Research in Spatial Real Estate Hedonic
Analysis.” Journal Article. *Journal of Geographical Systems* 11 (4):
311–16.

</div>

<div id="ref-Paez2021open" class="csl-entry">

Paez, Antonio. 2021. “Open Spatial Sciences: An Introduction.” Journal
Article. *Journal of Geographical Systems* 23 (4): 467–76.

</div>

<div id="ref-Paez2019inducing" class="csl-entry">

Paez, Antonio, Fernando López, Manuel Ruiz, and Máximo Camacho. 2019.
“Inducing Non-Orthogonal and Non-Linear Decision Boundaries in Decision
Trees via Interactive Basis Functions.” Journal Article. *Expert Systems
with Applications* 122: 183–206.

</div>

<div id="ref-Paez2001spatial" class="csl-entry">

Paez, A., T. Uchida, and K. Miyamoto. 2001. “Spatial Association and
Heterogeneity Issues in Land Price Models.” Journal Article. *Urban
Studies* 38 (9): 1493–1508.

</div>

<div id="ref-Paez2008moving" class="csl-entry">

Páez, A., F. Long, and S. Farber. 2008. “Moving Window Approaches for
Hedonic Price Estimation: An Empirical Comparison of Modeling
Techniques.” Journal Article. *Urban Studies* 45 (8): 1565–81.

</div>

<div id="ref-Paterson2002out" class="csl-entry">

Paterson, R. W., and K. J. Boyle. 2002. “Out of Sight, Out of Mind?
Using GIS to Incorporate Visibility in Hedonic Property Value Models.”
Journal Article. *Land Economics* 78 (3): 417–25.

</div>

<div id="ref-Rapkin1953housing" class="csl-entry">

Rapkin, C., L. Winnick, and D. Blank. 1953. *Housing Market Analysis*.
Book. Washington: US Housing; Home Finance Agency.

</div>

<div id="ref-tree" class="csl-entry">

Ripley, Brian. 2021. *Tree: Classification and Regression Trees*.
<https://CRAN.R-project.org/package=tree>.

</div>

<div id="ref-Rosen1974hedonic" class="csl-entry">

Rosen, S. 1974. “Hedonic Prices and Implict Markets: Product
Differentiation in Pure Competition.” Journal Article. *Journal of
Political Economy* 82 (1): 34–55.

</div>

<div id="ref-Royuela2013heuristic" class="csl-entry">

Royuela, V., and J. C. Duque. 2013. “HouSI: Heuristic for Delimitation
of Housing Submarkets and Price Homogeneous Areas.” Journal Article.
*Computers Environment and Urban Systems* 37: 59–69.

</div>

<div id="ref-rudin2019stop" class="csl-entry">

Rudin, C. 2019. “Stop Explaining Black Box Machine Learning Models for
High Stakes Decisions and Use Interpretable Models Instead.” Journal
Article. *Nature Machine Intelligence* 1 (5): 206–15.
<https://doi.org/10.1038/s42256-019-0048-x>.

</div>

<div id="ref-Thibodeau2003marking" class="csl-entry">

Thibodeau, T. G. 2003. “Marking Single-Family Property Values to
Market.” Journal Article. *Real Estate Economics* 31 (1): 1–22.

</div>

<div id="ref-Unwin1978introduction" class="csl-entry">

Unwin, D. J. 1978. *An Introduction to Trend Surface Analysis*. Book.
Vol. no. 5. Concepts and Techniques in Modern Geography. University of
East Anglia, Norwich: Geo Abstracts.

</div>

<div id="ref-Usman2020property" class="csl-entry">

Usman, Hamza, Mohd Lizam, and Muhammad Usman Adekunle. 2020. “Property
Price Modelling, Market Segmentation and Submarket Classifications: A
Review.” Journal Article. *Real Estate Management and Valuation* 28 (3):
24–35.

</div>

<div id="ref-wager2018estimation" class="csl-entry">

Wager, Stefan, and Susan Athey. 2018. “Estimation and Inference of
Heterogeneous Treatment Effects Using Random Forests.” Journal Article.
*Journal of the American Statistical Association* 113 (523): 1228–42.
<https://doi.org/10.1080/01621459.2017.1319839>.

</div>

<div id="ref-Watkins2001definition" class="csl-entry">

Watkins, C. A. 2001. “The Definition and Identification of Housing
Submarkets.” Journal Article. *Environment and Planning A* 33 (12):
2235–53.

</div>

<div id="ref-Wheeler2014bayesian" class="csl-entry">

Wheeler, D. C., A. Paez, J. Spinney, and L. A. Waller. 2014. “A Bayesian
Approach to Hedonic Price Analysis.” Journal Article. *Papers in
Regional Science* 93 (3): 663–83.

</div>

<div id="ref-Wu2018modified" class="csl-entry">

Wu, C., X. Y. Ye, F. Ren, and Q. Y. Du. 2018. “Modified Data-Driven
Framework for Housing Market Segmentation.” Journal Article. *Journal of
Urban Planning and Development* 144 (4): 15.

</div>

<div id="ref-Yoo2012variable" class="csl-entry">

Yoo, S., J. Im, and J. E. Wagner. 2012. “Variable Selection for Hedonic
Model Using Machine Learning Approaches: A Case Study in Onondaga
County, NY.” Journal Article. *Landscape and Urban Planning* 107 (3):
293–306.

</div>

</div>

[^1]: An emergent body of research aims at increasing the
    interpretability of machine learning methods, including Du, Liu, and
    Hu (2019) and Murdoch et al. (2019), among others. This is an area
    of research that is quickly evolving, although it is not without
    critics (e.g., Rudin 2019). Currently, existing approaches depend on
    fairly strong assumptions. For example, the causal forest framework
    (Wager and Athey 2018; Knaus, Lechner, and Strittmatter 2021)
    assumes that the leaves of trees are sufficiently small to mimic a
    randomized experiment. Assuming independence is often inappropriate
    in the analysis of spatial data, and econometric techniques that
    correctly treat spatial dependencies are mature. It is possible that
    in the future interpretable machine learning techniques will address
    spatial dependencies as well, so we are advised to pay attention to
    this stream of research.
