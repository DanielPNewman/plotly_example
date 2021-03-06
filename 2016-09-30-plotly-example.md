# Using the plotly package to give your ggplot2 plots simple reactivity to user input
Daniel P Newman  
30 September 2016  

##First make up some fake revenue data for a company with a number of shops operating in each State from 2012 to 2015:

```r
Sys.setenv("plotly_username"="DanielPNewman")
Sys.setenv("plotly_api_key"="ldbkt0fdxl")


### Install/load required packages
#List of R packages required for this analysis:
required_packages <- c("ggplot2", "stringr", "plotly", "dplyr")
#Install required_packages:
new.packages <- required_packages[!(required_packages %in% installed.packages()[,"Package"])]
if(length(new.packages)) install.packages(new.packages)
#Load required_packages:
lapply(required_packages, require, character.only = TRUE)
```

```
## Loading required package: ggplot2
```

```
## Loading required package: stringr
```

```
## Loading required package: plotly
```

```
## 
## Attaching package: 'plotly'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
```

```
## The following object is masked from 'package:graphics':
## 
##     layout
```

```
## Loading required package: dplyr
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```
## [[1]]
## [1] TRUE
## 
## [[2]]
## [1] TRUE
## 
## [[3]]
## [1] TRUE
## 
## [[4]]
## [1] TRUE
```

```r
#Set decimal points and disable scientific notation
options(digits=3, scipen=999) 

#Make up some fake data
df<-data_frame(state=rep(c("New South Wales", 
                 "Victoria", 
                 "Queensland",
                 "Western Australia",
                 "South Australia",
                 "Tasmania"), 36)) %>%
    group_by(state) %>%
    mutate(year=c(rep(2012, 9), rep(2013,9),rep(2014, 9),rep(2015, 9))) %>%
    group_by(state, year) %>%
    mutate(`store ID` = str_c("shop_#",as.character(seq_along(state)))) %>%
    group_by(state, year, `store ID`) %>%
    mutate(`Revenue ($)` =  ifelse(state=="New South Wales", sample(x=c(1000000:9000000), 1),
                            ifelse(state=="Victoria", sample(x=c(1000000:7000000), 1),
                            ifelse(state=="Queensland", sample(x=c(1000000:5000000), 1),
                            ifelse(state=="Western Australia",sample(x=c(100000:2000000), 1),
                            ifelse(state=="South Australia",sample(x=c(100000:900000), 1),       
                            ifelse(state=="Tasmania", sample(x=c(100000:2000000), 1), NA)))))))
```

##Now visualise this data using ggplot: 

```r
ggplot(df, aes(state, `Revenue ($)`, colour=state, label = `store ID`)) +
    geom_boxplot() + 
    geom_point() +
    theme(axis.title.x =  element_blank(),
          axis.text.x  =  element_blank(), 
          axis.title.y = element_text(face="bold", size=12),
          axis.text.y  = element_text(angle=0, vjust=0.5, size=11),
          legend.title = element_text(size=12, face="bold"),
          legend.text = element_text(size = 12, face = "bold"),
          plot.title = element_text(face="bold", size=14)) + 
    ggtitle("Store Revenue per State from 2012 to 2015") +
    facet_wrap(~year)
```

![](2016-09-30-plotly-example_files/figure-html/Plot1-1.png)<!-- -->

##Now make the plot reactive to the user's mouse by wrapping plotly's ggplotly() function around it:

```r
p<-ggplotly(ggplot(df, aes(state, `Revenue ($)`, colour=state, label = `store ID`)) +
    geom_boxplot() + 
    geom_point() +
    theme(axis.title.x =  element_blank(),
          axis.text.x  =  element_blank(), 
          axis.title.y = element_text(face="bold", size=12),
          axis.text.y  = element_text(angle=0, vjust=0.5, size=10),
          legend.title = element_text(size=12, face="bold"),
          legend.text = element_text(size = 12, face = "bold"))+
    facet_wrap(~year))


##Publish to plotly
# plotly_POST(p, filename = "dans_plotly_example")
```

###This type of simple plot made using plotly and ggplot2 in R are great because they have some basic "reactivity" to user input, (e.g. hover mouse over data point and lable appears with info. about data point like "store ID"" for example), but they do not need to be hosted on a server - they are simple enough to be knitted into a stand-alone HTML document. 
