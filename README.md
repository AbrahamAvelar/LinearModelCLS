## Calculate G and S as in LINK_AvelarRivasEtAl

#### Here we describe how to run scripts that fit plate measurements of aging fluorescent co-cultures to the model Ax+ST+Gt+CTt = ln(RFP/CFP)  

The goal is to run the function ModelASGC as follows:  

```markdown
[bgdataAll_ASGC, data2_ASGC]= ModelASGC(bgdata,plt,refs,OnlyMutStrain,OnlyRefStrain,medicionesminimas,datExtExponential,extraPlRefs);
```

In order to get there, first it is desirable to select only those measurements in exponential phase using ExtractExponentialPoints. First we prepare the input variables

`
    refs=[i j k]; %Integers with the number of the well in which there are competitions WTrfp+WTcfp  
    plt=[i j k]; %Integers of the plates for which you want the model to run.  
    Tiempo0=1; % 1, so it includes the first measurement of each outgrowth before the exponential time points.  
    showfig=1 % 1 if you wish to see growth curves with all of the measurements and the exponential phase time points highligthed  
    % BgDataAll is the structure with fields OD, RFP, CFP and t that comes from either LoadTecanFiles or from bgdataAll2BgDataAll  
    
ExpBgDataAll = ExtractExponentialPoints(BgDataAll, plt, showfig, refs, Tiempo0 )  
`
  
This is specially useful when you have many measurements per day (more than 3) and it will also improve the aproximation of growth rates, G.



Parameters:  
bgdata -> Structure that contains OD CFP and RFP. These structures may be the output from ReadTecanFiles, bgdataAll2BgDataAll, 

Tiene estos parámetros a tomar en cuenta y algunos scripts que preparan para optimizar el input

    

    ExpBgDataAll = ExtractExponentialPoints(BgDataAll, plt, showfig, refs, Tiempo0 )% Útil si se tienen muchos puntos por día. Mejora el cálculo de G

    odTh=.22; %qué cambio en OD es para un nuevo día
    ExpBgDataAll = calculaTiempos(ExpBgDataAll, plt, odTh);
    bgdataPrueba=BgDataAll2bgdataEGG(ExpBgDataAll,plt,'CFP','RFP',exp); %convierte a formato EGG. exp=1Son datos que provienen de 'ExtractExponentialPoints', si no provienen de esa funcion, entrnces exp=0
    
    OnlyMutStrain = [i]; % indice o indices de las posiciones dentro del plato que tienen solo la cepa referencia CFP
    OnlyRefStrain = [j]; % indice o indices de las posiciones dentro del plato que tienen solo la cepa referencia RFP (o la que sea de color de todas las mutantes)
    BgDataSinFondo = restarFondoFPs(bgdataPrueba, plt, OnlyMutStrain,OnlyRefStrain) %Quita el fondo con 

    datosExp=1
    [EXPbgdataSEGG,EXPdata2SEGG]=CalculaModeloNS_ScriptEGG(BgDataSinFondo,plt,refs,OnlyMutStrain,OnlyRefStrain,datosExp);
    
    o esta otra opción que incluye 
    medicionesminimas = 24% el número mínimo de mediciones para una competencia que se consideran aceptables,  si hay menos de este número te pone NaN
    extraPlRefs = [k]; % k=numero de plato que tiene las referencias. Si hay refereencias de un solo color en todos los platos, entonces k=0.
    [bgdataAll_CMAN, data2_CMAN]= ModelASGC(BgDataSinFondo,plt,refs,OnlyMutStrain,OnlyRefStrain,medicionesminimas,datExtExponential,extraPlRefs);

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
