## Example with sample data

This is a step by step tutorial to fit the model A+ST+Gt+CTt = ln(RFP/CFP) to an specific dataset. If you whish to read the general case go to the [home of the site](https://abrahamavelar.github.io/LinearModelCLS/)


You can follow this example to fit measurements from 2 plates of aging fluorescent co-cultures to the model Ax+ST+Gt+CTt = ln(RFP/CFP). 
First you should download sample data [BgDataAllTest](https://github.com/AbrahamAvelar/LinearModelCLS/).  



Next thing is to load it into your Matlab workspace  
```matlab
load BgDataAllTest
```
we see what's in this structure
```matlab
>>BgDataAllTest

BgDataAllTest = 
1x2 struct array with fields:
    OD
    CFP
    RFP
    t
```

There are two variables loaded. While 'muts' has the positions for each mutants in this exaple, you must make sure that the BgDataAllTest structure has the relevant fields OD, CFP, RFP, and t.  Your structure may have other fields but it must contain these four. If you have a structure with other names in the fields, the function [BgDataAll2bgdataEGG](https://github.com/AbrahamAvelar/Comparacion_Metodos_Envejecimiento/tree/master/Functions/CorrerModeloNS_ScriptsEGG/PrepareRawDataToCalcNS) may be useful.


Before fitting the dataset to the model, we get the exponential phase measurements.
 ```matlab
refs = muts.refs
plt = 1:2;
Tiempo0 =1;
showfig=1;
ExpBgDataAll =  ExtractExponentialPoints(BgDataAllTest, plt, showfig, refs, Tiempo0 )
```
If you used ```showfig = 1``` then one figure per plate should be printed to the screen. lines show all of the measurements and empty circles in different colour show the selected measurements that were extracted by the function.
  
Then we compute two time-related variables:  
Tdays - how many days passed from the first measurement to the start of each outgrowth, this will be the coefficients T in the equations to fit the model   
tOut - How many days passed from the moment the outgrowth was started to the moment each read occurred.
```matlab
odTh=0.22;
ExpBgDataAll = calculaTiempos(ExpBgDataAll, plt, odTh)
```
  
Then we subtract the background signal from wells that only have the WTrfp or WTcfp strain.
```matlab
%% Prepare input variables
exp=1; % We put 1 because we have a structure only with exponential points which is the output of 'ExtractExponentialPoints'
bgdataPrueba=BgDataAll2bgdataEGG(ExpBgDataAll,plt,'CFP','RFP',exp); 
OnlyMutStrain = [89]; %Index or Indexes of the reference wells that have only WTcfp
OnlyRefStrain = [96]; %Index or Indexes of the reference wells that have only WTrfp (the same FP as all of the mutants)  
% Execute background subtraction script
BgDataSinFondo = restarFondoFPs(bgdataPrueba, plt, OnlyMutStrain,OnlyRefStrain)
```


Then we are ready to call the fitting function regress which is inside the ModelASGC script.  
To do so this script first builds the predictor matrix and response vector taking into account which wells are WTrfp+WTcfp reference competitions, which have only one fluorescent strain and that they have enough valid measurements. After fitting the data to the model it organizes variables A, S, G and C and some statistics to the output structures.
```matlab
% Prepare input variables
medicionesminimas=[15]; %Smallest number of valid measurements per well to be included in the fitting function
extraPlRefs = 0; % The number of an extra plate that has references in it. Put 0 if you have references in every plate.
% Execute Linear Model fitting script
[bgdataAll_LM,data2_LM]=ModelASGC(BgDataSinFondo,plt,refs,OnlyMutStrain,OnlyRefStrain,medicionesminimas,exp,extraPlRefs)
```
bgdataAll_LM contains the coefficients A, S, G and C for each well, then you can find out whether your mutant strains increased or decreased lifespan.

In this example we show what happened to nine mutants whit 6 or 7 biological replicates each.
```matlab
%% See S to make sure it makes sense
campos = fieldnames(muts);
clf
for pl = plt
    subplot(2,1,pl)
    plot( [0 length(campos)+1], [0 0], '-', 'color', [.5 .5 .5]) 
    hold on
    for i=1:length(campos)
        thisY = bgdataAll_LM(pl).S(muts.(str2mat(campos(i)))) ;
        jitterplot( i, thisY,  .2, '.', 'k')
    end
    xlim([0 i+.5])
    ylim([-.3 .2])
    set(gca, 'xtick', [1:12], 'xticklabels', campos)
    ylabel('S, relative survivorship')
    title( strcat('Replicate', num2str(pl)) )
end
```
