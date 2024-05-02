---
title: This will be used as the title-tag of the page head
---

# [System]{.ul}

## Sytem info

```{r eval=FALSE}
Sys.info()
#Sys.info()["nodename"]=="sce-ide-001"

Sys.getenv()
```

## Global options

```{r eval=FALSE}
#Disabling scientific notation in R
options(scipen = 999)
# Resseting default option
options(scipen=0, digits=7)
```

## Packages

### Installed packages

```{r eval=FALSE}
ip <- as.data.frame(installed.packages())
```

### Package version

```{r eval=FALSE}
.libPaths()
packageVersion("ggplot2")
installed.packages()
```

### SRtools

```{r eval=FALSE}
install.packages("devtools")
require(devtools)
devtools::install_git('http://gitwise.pharma.aventis.com/R-PACKAGER-SANOFI/SRtools.git', ref = '1.3.1', upgrade="never")
library(SRtools)
```

### Package dependencies

```{r eval=FALSE}
library(pkgnet)
result <- CreatePackageReport('SRtools',  report_path = "SRtools_pkgnet.html")
```

------------------------------------------------------------------------

# Useful scripts

## Remove all objects

In order to remove all objects from current environment

```{r eval=FALSE}
rm(list = ls())
```

## Not in

"Not in" function is the opposite to **%in%**

```{r eval=TRUE}
'%!in%' <- function(x,y)!('%in%'(x,y))

# Example
c(1,3,11) %!in% 1:10
```

## Files list

How to get list of files within folder

```{r eval=TRUE, collapse=TRUE}
list.files(path="C:/Users/FR56181/OneDrive - Sanofi/R")
list.files(path="C:/Users/FR56181/OneDrive - Sanofi/R", pattern = "^my*.*rmd")
list.files(path="C:/Users/FR56181/OneDrive - Sanofi/R", pattern = "*.html")
```

## Get unsaved script

<https://stackoverflow.com/questions/35223435/how-to-get-unsaved-script-tabs>

```{r eval=FALSE}
# reading the JSON file
dataObj <- jsonlite::fromJSON(".Rproj.user/1195A371/sdb/per/u/B0959442", simplifyVector = T)

writeLines(dataObj$contents)
```

------------------------------------------------------------------------

# Functions

## Function arguments

```{r}
myplot <- function(x,y,...){
  plot(x=x,y=y,...)
}

myplot(rnorm(100),rnorm(100),xlim=c(1,2))
```

## Arguments evaluation

```{r  eval=TRUE, highlight=TRUE, collapse=TRUE, error=FALSE}
f0 <- function(ds,anavar,byvar){
  f_anavar <- anavar
  f_byvar  <- byvar
  print(f_anavar)
  print(f_byvar)
}
## > f0(mtcars,gear,carb)
## Error in f0(mtcars, gear, carb) : object 'gear' not found

f1 <- function(ds,anavar,byvar){
  f_anavar <- deparse(substitute(anavar))
  f_byvar  <- deparse(substitute(byvar))
  print(f_anavar)
  print(f_byvar)
}
f1(mtcars,gear,carb)

f2 <- function(ds,anavar,byvar){
  f_anavar <- eval(substitute(anavar),ds ,parent.frame())
  f_byvar  <- eval(substitute(byvar) ,ds ,parent.frame())
  print(f_anavar)
  print(f_byvar)
}
f2(mtcars,gear,carb)
```

# Apply functions

## tapply

Apply a function to each cell of a ragged array, that is to each (non-empty) group of values given by a unique combination of the levels of certain factors

```{r eval=TRUE, highlight=TRUE, collapse=TRUE}
ds <- mtcars
ds$gear <- as.factor(ds$gear)
tapply(ds$wt,ds$gear, mean)
## following tapply
tapply(ds$wt,ds$gear, length) ## is almost the same as :
table(mtcars$gear)
```

# Levels

Same as format in SAS, use levels and labels in factor function

```{r}
x <- head(mtcars,5)[,1:3]
x$trt = c("Pbo","Active","Drug","Pbo","Drug")
x$new <- factor(x$trt,levels = c("Active", "Drug", "Pbo"), labels = c("Active drug", "Drug 1", "Placebo"))
print(x)

```

# Keyboard Shortcut

## Clear console

`CRTL+L`

```{r eval=FALSE}
cat("\014")  
```

# Import data {.tabset .tabset-fade .tabset-pills}

## csv

```{r}
rm(list = ls())
```

## xlsx

```{r eval=FALSE}
library(openxlsx)
mydata <- read.xlsx("mydoc.xlsx")
```

# Character string

## Regular expression

References :\
[RegExpression CheatSheet](https://rstudio.com/wp-content/uploads/2016/09/RegExCheatsheet.pdf)

Examples:

```{r eval=TRUE, collapse=TRUE}
strings <- c("a", "ab cd", "acbcd", "accb", "acccb", "accccb")

# Empty string at either edge of a word: \b (boundaries)
grep("\\bcd\\b", strings,value=TRUE) 

# Matches at least 1 time: +
grep("ac+b", strings, value = TRUE)

# Matches at most 1 time: ?
grep("ac?b", strings, value = TRUE)

# Matches exactly n times : {n}
grep("ac{2}b", strings, value = TRUE)

# Matches at least n times : {n,}
grep("ac{2,}b", strings, value = TRUE)

# Matches between n and m times : {n,m} 
grep("ac{2,3}b", strings, value = TRUE)
stringr::str_extract_all(strings, "ac{2,3}b", simplify = TRUE)
```

Recuperer les valeurs entre paranthèses dans un chaine de character\
Get values in-between parenthesis in character string

```{r eval=TRUE, collapse=TRUE}
x <- "A1 (10), A2(20), A3(100), Total(N=130)"
regmatches(x,m = gregexpr(pattern = "(?<=\\().*?(?=\\))", x, perl = TRUE))

x1 <- "A1 (10), A2(20), A3(70), Total(N=100)"
x2 <- "B1 (75), B2(15), B3(10), Total(N=100)"
x <- c(x1,x2)
regmatches(x,m = gregexpr(pattern = "(?<=\\().*?(?=\\))", x, perl = TRUE))
```

```{r eval=TRUE, collapse=TRUE}
df1 <- "Aspartate Aminotransferase(AST) (SGOT) SGOT (xxx)"
df2 <- "Log(Aspartate Aminotransferase(AST) (SGOT) SGOT (xxx))"
df <- rbind(df1,df2)
# >>> Do not keep values starting with "Log("
df[which(startsWith(df,"Log(")==FALSE),]

# >>> Remove words "(xxx)" and "(SGOT)"
words <- paste(c("\\(xxx\\)","\\(SGOT\\)"), collapse = "|")
trimws(gsub(words,"",df,perl =  TRUE) )
```

Trailing zero for Subject ID

```{r eval=TRUE, collapse=TRUE}
library("stringr")
## Add leading 0 to SUBJID
SUBJID <- stringr::str_pad("32001001", 9, pad = "0")
print(SUBJID)

```

# Data frame

## Remove all empty columns

Get rid of all NA values columns within data.frame

```{r eval=FALSE}
## Remove empty columns
rm.colNA <- function(df){
  df <- df[,colSums(is.na(df))<nrow(df)]
}
```

## Sort dataframe

```{r eval=FALSE}

df <- df[with(df, order(USUBJID, PARAMCD, AVISITN)),]

```

## dplyr

### Merge

```{r eval=FALSE}

library(dplyr)
# Regeneron data (703 genetic ID / 100 from cohort 1 / 603 from cohort 2)
sel.rgn <- rgn.ped %>%
  mutate(GENID = V1) %>%
  select(GENID)

# Create combined to avoid following Warning message:
# In left_join_impl(x, y, by$x, by$y) : joining factors with different levels, coercing to character vector

# Make sure that both factors have the same levels before merging
combined <- sort(union(levels(sel.rgn$GENID), levels(sel.key$GENID)))

# Merge data left_join(a,b,by="var")
sel.rgn.subjid <- left_join(mutate(sel.rgn, GENID=factor(GENID, levels=combined)),
                            mutate(sel.key, GENID=factor(GENID, levels=combined)), 
                            by="GENID") %>%  arrange(SUBJID)
```

# Graphs {.tabset .tabset-fade .tabset-pills}

## plot

### Plot axis

**side** integer value on which we would like to add axis. Possible values are :\
1: bottom 2: left 3: top 4: right **at** position for ticks **labels** text annotation for ticks

```{r eval=TRUE}

x <- 1:4
y <- x*x

# Plot with axes
plot(x, y)

# Plot without x axis
plot(x, y, xaxt="n")

# plot with Labels on x axis
plot(x, y, xaxt="n")
axis(side=1, at = 1:4, labels=c("Label 1","Label 2","Label 3","Label 4"))

# plot with Labels on x axis
plot(x, y, xaxt="n")
tck <- axis(1, tick=TRUE, at = 1:4, labels=FALSE)
labels=c("Label 1","Label 2","Label 3","Label 4")
text(x=tck, y=par("usr")[3], labels=labels, srt=45, xpd=TRUE, adj=c(1.3,0), cex=0.9)
#text(x=tck, y=par("usr")[3], labels=labels, srt=315, xpd=TRUE, adj=c(-0.3,1.3), cex=0.9)
#text(cex=1, x=x-.25, y=-1.25, labs, xpd=TRUE, srt=45)


```

x \<- barplot(table(mtcars\$cyl), xlab=NULL) labs \<- c("Label 1","Label 2","Label3") text(cex=1, x=x-.25, y=-1.25, labs, xpd=TRUE, srt=45)

# Excel

How to save data frame into Excel spreadshee, with defined style, and adding freeze pane and filter column.

```{r eval=FALSE}

fileout <- paste0("myfile.xlsx")
# Define Style for spreadsheet
hs1 <- openxlsx::createStyle(fgFill = "#D9D9D9", 
                             halign = "LEFT", 
                             textDecoration = "Bold",
                             border =  c("top", "bottom", "left", "right") ,
                             borderColour="black", 
                             fontColour = "black", 
                             fontName = "Calibri", 
                             fontSize = 11)

x <- head(iris)
openxlsx::write.xlsx(x,
                     fileout,
                     sheetName = "sheet1", 
                     zoom = 75, 
                     colWidths = "auto", 
                     firstRow = TRUE, 
                     borders = "all", 
                     withFilter = TRUE, 
                     headerStyle = hs1)

myfile <- openxlsx::loadWorkbook(fileout)

openxlsx::addFilter(myfile,"sheet1",rows=1,cols = 1:ncol(x))
openxlsx::freezePane(myfile, "sheet1", firstActiveRow = 1, firstActiveCol = 8)
openxlsx::saveWorkbook(myfile,fileout,overwrite=TRUE)
```

# Packages specificities

## plotly

### Assign color to treatment group (using level)

```{r eval=FALSE}
library(plotly)
library(ggplot2)

dat <- data.frame(x=runif(80),y=runif(80),
                  grp = rep(LETTERS[1:20],each = 2),stringsAsFactors = TRUE)

myColors <- c(
  "dodgerblue2", "#E31A1C", # red
  "green4",
  "#6A3D9A", # purple
  "#FF7F00", # orange
  "black", "gold1",
  "skyblue2", "#FB9A99", # lt pink
  "palegreen2",
  "#CAB2D6", # lt purple
  "#FDBF6F", # lt orange
  "gray70", "khaki2",
  "maroon", "orchid1", "deeppink1", "blue1", "steelblue4",
  "darkturquoise", "green1", "yellow4", "yellow3",
  "darkorange4", "brown")

names(myColors) <- levels(dat$grp)
myColors <- myColors[grep("", names(myColors))]

### All groups
plot_ly(dat,x = ~x) %>%
  add_trace(
    y = ~y,
    type = "scatter",
    mode = "markers",
    color = ~grp,
    colors = myColors
  )

### avec valeurs grp =  H,I,J ###
mydat1 <- dat[55:60,]

plot_ly(mydat1,x = ~x) %>%
  add_trace(
    y = ~y,
    type = "scatter",
    mode = "markers",
    color = ~grp,
    colors = myColors
  )

### avec valeurs grp =  O, P, Q ###
mydat2 <- dat[70:74,]

plot_ly(mydat2,x = ~x) %>%
  add_trace(
    y = ~y,
    type = "scatter",
    mode = "markers",
    color = ~grp,
    colors = myColors
  )
```

# WISE

## alloc_install.R

```{r eval=FALSE}
#   02 : 22-MAR-2020 :  User name   :  Upgrade with R WISE guideline recommendation
#   ====================================================================== */

#/* ======= Beginning of Users Parameters ================================= *

# -- 1. ORIGINAL ALLOC_INSTALL.R ----

repos <- structure(c(CRAN="http://mran.pharma.aventis.com/snapshots/2019-04-25/"))
options(repos=repos)
getOption("repos")

if (!file.exists(".R/LIB/devtools")){
  
  unlink(list.files(paste0(W_WISE,".R/LIB"), full.names = TRUE), recursive= TRUE)
  unlink(list.files(paste0(W_WISE,".R/LIB/affy"), full.names = TRUE), recursive= TRUE)
  
  ## REQUIREMENT PACKAGE
  
  install.packages("usethis",  quiet = FALSE, verbose = TRUE)
  install.packages("httr",  quiet = FALSE, verbose = TRUE)
  install.packages("/cm/shared/apps/R-Package/contrib/stringi/stringi_1.4.3.tar.gz",  quiet = FALSE, verbose = TRUE)
  install.packages("devtools",  quiet = FALSE, verbose = TRUE) 
  library(devtools)
  
  ## BIOCONDUCTOR (optional)
  devtools::install_git("http://gitwise.pharma.aventis.com/bioconductor/biocmanager.git") 
  
  ## Save original packages installation
  wise_pkg <- list.dirs(paste0(W_WISE,"/.R/LIB"), full.names = FALSE, recursive = FALSE)
  saveRDS(wise_pkg,paste0(MISC,"/RLIB_init_pkg.rds"))  
}

# -- 2. SET DEFAULT INSTALLED PAKAGES ----
# List of WISE initial installed packages (not to be removed)
init_pkg <- readRDS(paste0(MISC,"/RLIB_init_pkg.rds"))

rm.package = setdiff(list.dirs(paste0(W_WISE,"/.R/LIB"), full.names = FALSE, recursive = FALSE), init_pkg)
remove.packages(pkgs = rm.package, lib = paste0(W_WISE,"/.R/LIB"))

library("BiocManager")
library(devtools)

# -- 3. CRAN INSTALL.PACKAGES (PRODOPS) ----
install.packages("CRAN_pkgname")

# -- 4. BIOCONDUCTOR INSTALLATION PACKAGE ----
BiocManager::install("Bioconductor_pkgname")

# -- 5. TMTOOLS INSTALLATION PACKAGE ----
require(devtools)
devtools::install_git('http://gitwise.pharma.aventis.com/R-PACKAGER-SANOFI/SRtools.git', ref = '1.3.1', upgrade="never")


```

## Load SAS ADaM or SDTM datasets

```{r eval=FALSE}
rdbs <- "~/wise/PRODOPS/SAR231893/EFC14280/CSR_2/RDFIX_BS"  
library(SRtools)

ds <- sr.data.sasimport("~/wise/PRODOPS/SAR231893/EFC14280/CSR_2/RDFIX_BS/ds_h.sas7bdat")
adsl <- sr.data.sasimport("~/wise/PRODOPS/SAR231893/EFC14280/CSR_2/ADS/DATA/adsl.sas7bdat")

adsl.trt <- adsl[which(adsl$ACTARM != "Screen Failure"),c("USUBJID","ACTARM")]
adsl.trt <- adsl[,c("USUBJID","ACTARM")]

ic <- ds[which(ds$DSSCAT=="INFORMED CONSENT"),]
ic.trt <- merge(ic,adsl.trt,by="USUBJID")

```
