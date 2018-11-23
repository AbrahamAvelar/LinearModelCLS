## Calculate G and S as in LINKPaper

#### Here we describe how to run scripts that fit plate measurements of aging fluorescent co-cultures to the model Ax+ST+Gt+CTt = ln(RFP/CFP)  

The goal is to run the function ModelASGC as follows:  

```markdown
[bgdataAll_ASGC, data2_ASGC]= ModelASGC(bgdata,plt,refs,OnlyMutStrain,OnlyRefStrain,medicionesminimas,datExtExponential,extraPlRefs);

```

In order to get there, we select only those measurements in exponential phase using ExtractExponentialPoints after preparing input variables.

```markdown
refs=[i j k]; %Integers with the number of the well in which there are competitions WTrfp+WTcfp  
plt=[i j k]; %Integers of the plates for which you want the model to run.  
Tiempo0=1; % 1, so it includes the first measurement of each outgrowth before the exponential time points.  
showfig=1 % 1 if you wish to see growth curves with all of the measurements and the exponential phase time points highligthed  
  %BgDataAll is the structure with fields OD, RFP, CFP and t that comes from either LoadTecanFiles or from bgdataAll2BgDataAll  
    
ExpBgDataAll = ExtractExponentialPoints(BgDataAll, plt, showfig, refs, Tiempo0 )  
```
  
This is specially useful when you have many measurements per day (more than 3) and it will also make the aproximation of G more dependent of the phase in which cells are in exponential phase.
  
The next step is to order the field variables of time in the structure ExpBgDataAll using calculaTiempos
  
```markdown
odTh=0.22; %Threshold of the change  between consecutive measurements to be identified as a new outgrowths's measurement. urve.
ExpBgDataAll = calculaTiempos(ExpBgDataAll, plt, odTh);
```
It may be a good idea to play a bit with odTh depending of the strains, media and other factors that may change the dynamics or the magnitude of the growth curve with OD600. Usually values between 0.2 and 0.4 work just fine to detect at which measurements there is a new outgrowth. 

Now we substract background fluorescences. We prepare variables and use restarFondoFPs.

```markdown
exp=1; % We put 1 because we have a structure only with exponential points which is the output of 'ExtractExponentialPoints'
bgdataPrueba=BgDataAll2bgdataEGG(ExpBgDataAll,plt,'CFP','RFP',exp); 
OnlyMutStrain = [i]; %Index or Indexes of the reference wells that have only WTcfp
OnlyRefStrain = [j]; %Index or Indexes of the reference wells that have only WTrfp (the same FP as all of the mutants)
BgDataSinFondo = restarFondoFPs(bgdataPrueba, plt, OnlyMutStrain,OnlyRefStrain)
```

Finally we are ready to run our fitting function. It will build the predictor variables matrix for each plate and then fit them to their respective response vector using Matlab's regress function.

```markdown
medicionesminimas=[i]; %Smallest number of valid measurements per well to be included in the fitting function
extraPlRefs = 0; % The number of an extra plate that has references in it. Put 0 if you have references in every plate.
[bgdataAll_LM,data2_LM]=ModelASGC(BgDataSinFondo,plt,refs,OnlyMutStrain,OnlyRefStrain,medicionesminimas,datExtExponential,extraPlRefs);
```

If you got here and the output bgdataAll_LM that contains your dataset and the vectors of solutions A, S, G and C it means you've made it to work! congratulations!


Please provide us any feedback you may have!



 %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%      

You can use the [editor on GitHub](https://github.com/AbrahamAvelar/LinearModelCLS/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/AbrahamAvelar/LinearModelCLS/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
