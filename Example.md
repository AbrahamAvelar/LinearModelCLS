## Example with sample data

You can follow this example to fit measurements from 2 plates of aging fluorescent co-cultures to the model Ax+ST+Gt+CTt = ln(RFP/CFP).

  
```markdown
load BgDataAllTest
```

 ```markdown
refs = muts.refs
plt = 1:2;
Tiempo0 =1;
showfig=1;
ExpBgDataAll =  ExtractExponentialPoints(BgDataAllTest, plt, showfig, refs, Tiempo0 )
```

```markdown
odTh=0.22;
ExpBgDataAll = calculaTiempos(ExpBgDataAll, plt, odTh)
```

```markdown
%% Prepare input variables
exp=1; % We put 1 because we have a structure only with exponential points which is the output of 'ExtractExponentialPoints'
bgdataPrueba=BgDataAll2bgdataEGG(ExpBgDataAll,plt,'CFP','RFP',exp); 
OnlyMutStrain = [89]; %Index or Indexes of the reference wells that have only WTcfp
OnlyRefStrain = [96]; %Index or Indexes of the reference wells that have only WTrfp (the same FP as all of the mutants)  
% Execute background subtraction script
BgDataSinFondo = restarFondoFPs(bgdataPrueba, plt, OnlyMutStrain,OnlyRefStrain)
```

```markdown
% Prepare input variables
medicionesminimas=[15]; %Smallest number of valid measurements per well to be included in the fitting function
extraPlRefs = 0; % The number of an extra plate that has references in it. Put 0 if you have references in every plate.
% Execute Linear Model fitting script
[bgdataAll_LM,data2_LM]=ModelASGC(BgDataSinFondo,plt,refs,OnlyMutStrain,OnlyRefStrain,medicionesminimas,exp,extraPlRefs)
```


```markdown
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
