# 1.https://www.sarproz.com/groups/sarproz-group/forum/topic/original-ps-method/

Hi Daniele,

I would like to compare the original PS method with the QPS mehtod using sw. I have the following questions:
1- As far as I know the main difference of the QPS method with the original PS method is the application of a phase weighting. In the QPS method, for each point, the spatial coherence is used to weight the corresponding interferometic phase. This weighting, in SARPROZ, seems to be through the matrix coherency windows and selection of the weight type (either coh or amp). So, I was wondering if I don’t use any weight does the method become the original PS method?

2- What would be the role of graph in this case? The original PS method uses the common STAR graph. Is it feasible, for example, to use a full graph and set the weighting option as none? Is this case the same as the original PS method but with a different graph?

3- In QPS method, an interferometic filtering is applied. Where in SARPROZ do we have such a phase filtering?

Thanks for your time,
Yasser

--------------

Hi Yasser,

original PSI: STAR graph, no weights, no filtering
QPS: non-STAR graph, weights (spatial coherence), filtered phase
Anyway, Sarproz gives you the choice to combine those options and do what you want. You do not need to call what you do “PSI” or “QPS”.

However, you have to be aware of the following: when you choose the option “coher” as “weight”, the software tries to read the interferograms in the INTERFEROGRAMS folder according to the loaded images graph. If at least one interferogram is not found, the sw calculates coherence and filtered phase using the window size specified in the processing options window.

This means, if you process the interferograms in advance, you have more options to choose (you can choose how to filter and how to calculate the coherence using the insar processing options).

If you do NOT choose the “coher” as “weight”, the software calculates the interferometric phase directly from the images (no need to calculate interferograms).

Let me know if this makes things clearer

-----------------

Thanks Daniele. It is now much clearer indeed. Also, I would like whether other PS approaches such as StaMPS and DePSI can be implemented. The former, as you know, employs a spatial smoothness assumption whereas the latter uses a temporal smoothness assumption. But I could not find such parameters in sw.


-----------------

Yasser,

In Sarproz you can implement almost everything. However, instead of having buttons as “PSI”, “SBAS”, or whatever, you have a series of tools/options that you can combine. Clearly, this requires understanding the operations. It’s more difficult than having predefined buttons, but I think it’s more powerful.

The 2 conditions you mention (smoothness in time, smoothness in space) are common to all multi-temporal approaches. Without them you could not solve the system. You need models/assumptions to solve the system. However, what you will never find in Sarproz is substituting the data with a model. What Sarproz does is offering models to process the data, but the data are always honored.
If you substitute data with a model, your solution will always look beautiful, but it may be fake. In Sarproz this never happens. If the model/approach you choose is not correct, the solution will not converge and the result will be bad. “Better the bitter truth than a sweet lie”: this is the Sarproz philosophy.

The time smoothness assumption is implicit in the time series module. Using a linear model (or polynomial) means assuming that the deformation is smooth in time. Sarproz allows you also to add dimensions in the deformation model (like temperature or water levels in case of dams or whatever you want). Moreover, it offers a non-parametric model (smart option) to deal with non-linear deformation.

The space smoothness assumption is implicit in the space connections of the APS graph and later in assuming a common reference point. The latter operation is possible thanks to the APS removal, estimated through a spatial graph (which means, again, assuming space smoothness).

There are particular cases in which the last operation (referring all points to a single reference, removing the APS and re-estimating the time series point by point) may fail. This is the case e.g. of strong movements. If analyzing the space derivative of the deformation still works (that is, the space graph in the APS module works), now Sarproz give you the option to stop the analysis at that point (skipping the next step) and still generate time series (export TS in the APS module).

To conclude, I believe you have all tools you need. To use them properly, you just need some reasoned experience…


# 2. https://www.sarproz.com/groups/sarproz-group/forum/topic/qps-with-dem/

Hello,

when asking how to make QPS, I got the reply that it is “APS with weights”. I understand it, but when I try to click on weights, I get a message that I cannot use the DEM and weights at the same time. Is it a way to overcome this, to make QPS in mountaineous areas?

Thank you, Ivana




Hi Ivana,

first of all, QPS is a title which we used in a paper. However, as you have seen, Sarproz does not allow straight “PS” or “QPS” approaches. In fact, one can choose among many different options:

a. Images graph. The Star graph is adopted for PS-like approaches. For other approaches, you can choose the images graph better suiting your case (MST, delaunay, small Bn’s, small Bt’s, sets of coherent interf with threshold on coherence, full graph, or your own custom graph)

b. weights: you can weight the phase with the InSAR spatial coherence or with amplitude.

c. filtered or unfiltered phase

d. wrapped or (spatially) unwrapped phase

e. deformation model (linear, polynomial, seasonal, non-parametric)

If you choose “coherence” for weights, the sw automatically loads the phase from interferograms (which, by default, are filtered and corrected for the existing external topography). This is why, if you choose to use weights, the sw does not allow you to remove the external DEM: because the software assumes that the DEM has already been removed (from the interferograms).

Please be aware of the following: if you choose to weight the analysis with the coherence, the sw automatically tries to read (from the disk) the interferograms according to the loaded images graph. If you generated some interferograms with DEM removal and some others without DEM removal, the sw at this moment cannot detect the difference and this may cause problems. [in the future we will record such settings for each interferogram]. However, if one or more interferograms are missing, the software re-estimates on a sparse-base all interferometric phases (if one or more interferograms are missing, all interferograms are ignored and re-calculated only on the selected sparse points).

Since many options are involved, my suggestion is to carry out some experiments using the small are module. When you find out the best options for your case, you can apply them in the APS estimation.

Let me know if anything is unclear.

