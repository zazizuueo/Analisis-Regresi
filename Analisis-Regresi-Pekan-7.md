\#Install Package

    library(readxl)

    ## Warning: package 'readxl' was built under R version 4.3.2

    library(ggplot2)
    library(lmtest)

    ## Warning: package 'lmtest' was built under R version 4.3.2

    ## Loading required package: zoo

    ## Warning: package 'zoo' was built under R version 4.3.2

    ## 
    ## Attaching package: 'zoo'

    ## The following objects are masked from 'package:base':
    ## 
    ##     as.Date, as.Date.numeric

\#Import Data

    df <- read_excel("D:/Dunia Stat/Semester 4/Anreg/Data_Anreg_W7.xlsx")
    head(df)

    ## # A tibble: 6 × 3
    ##      No     X     Y
    ##   <dbl> <dbl> <dbl>
    ## 1     1     2    54
    ## 2     2     5    50
    ## 3     3     7    45
    ## 4     4    10    37
    ## 5     5    14    35
    ## 6     6    19    25

## Scatter Plot

    ggplot(df, aes_string(x = "X", y = "Y")) +
      geom_point()

    ## Warning: `aes_string()` was deprecated in ggplot2 3.0.0.
    ## ℹ Please use tidy evaluation idioms with `aes()`.
    ## ℹ See also `vignette("ggplot2-in-packages")` for more information.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

![](Analisis-Regresi-Pekan-7_files/figure-markdown_strict/unnamed-chunk-3-1.png)

Scatter Plot diatas menunjukkan bahwa scatter plot tersebut tidak linier

# Membentuk Model Regresi

    model_1 = lm(Y~X, data = df)
    summary(model_1)

    ## 
    ## Call:
    ## lm(formula = Y ~ X, data = df)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -7.1628 -4.7313 -0.9253  3.7386  9.0446 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 46.46041    2.76218   16.82 3.33e-10 ***
    ## X           -0.75251    0.07502  -10.03 1.74e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 5.891 on 13 degrees of freedom
    ## Multiple R-squared:  0.8856, Adjusted R-squared:  0.8768 
    ## F-statistic: 100.6 on 1 and 13 DF,  p-value: 1.736e-07

## Uji Autokorelasi

### Durbin-Watson

Uji Durbin-Watson untuk mendeteksi adanya Autokorelasi pada residu
regresi, dengan hipotesis sebagai berikut : H0 -&gt; Tidak terdapat
autokorelasi H1 -&gt; Terdapat autokorelasi Alpha -&gt; 0.05

    uji_dw <- dwtest(model_1)

    ifelse(uji_dw$p < 0.05, "Ada Autokorelasi", "Tidak Ada Autokorelasi")

    ## [1] "Ada Autokorelasi"

Berdasarkan Uji Durbin-Watson test diatas dapat disimpulkan bahwa
terdapat Autokorelasi yang berarti sisaan tidak saling bebas.

Nilai p-value &lt; 0.05 tolak H0, maka pada taraf nyata 95% terdapat
autokorelasi, sehingga pada asumsi ini tidak terpenuhi.

## Uji Homokedastisitas

    bptest(model_1, studentize = TRUE, data = df)

    ## 
    ##  studentized Breusch-Pagan test
    ## 
    ## data:  model_1
    ## BP = 0.52819, df = 1, p-value = 0.4674

Berdasarkan uji Breush-Pagan didapatkan nilai p-value sebesar 0.4674
&gt; 0.05 sehingga gagal tolak H0. Oleh karena itu, tidak cukup bukti
untuk menolak hipotesis nol (H0). Ini menunjukkan bahwa tidak terdapat
cukup bukti untuk menyatakan bahwa varian residual dalam model tidak
konstan atau untuk menyimpulkan adanya heterokedastisitas. Dengan
demikian, asumsi homokedastisitas terpenuhi.

# Penanganan Autokorelasi

## Transformasi Data

Terdapat satu asumsi yang tidak terpenuhi (autokorelasi) sehingga
diperlukan transformasi data agar asumsi tersebut terpenuhi.

#### pada X

    xtrans <- sqrt(df$X)
    xtrans

    ##  [1] 1.414214 2.236068 2.645751 3.162278 3.741657 4.358899 5.099020 5.567764
    ##  [9] 5.830952 6.164414 6.708204 7.211103 7.280110 7.745967 8.062258

#### pada Y

    ytrans <- sqrt(df$Y)
    ytrans

    ##  [1] 7.348469 7.071068 6.708204 6.082763 5.916080 5.000000 4.472136 4.000000
    ##  [9] 4.242641 3.605551 2.828427 3.316625 2.828427 2.000000 2.449490

    model_2 = lm(ytrans~xtrans, data = df)
    summary(model_2)

    ## 
    ## Call:
    ## lm(formula = ytrans ~ xtrans, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.42765 -0.17534 -0.05753  0.21223  0.46960 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  8.71245    0.19101   45.61 9.83e-16 ***
    ## xtrans      -0.81339    0.03445  -23.61 4.64e-12 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.2743 on 13 degrees of freedom
    ## Multiple R-squared:  0.9772, Adjusted R-squared:  0.9755 
    ## F-statistic: 557.3 on 1 and 13 DF,  p-value: 4.643e-12

## Uji Autokorelasi Hasil Transformasi

### DUrbin-Watson

Uji Durbin-Watson untuk mendeteksi adanya Autokorelasi pada residu
regresi, dengan hipotesis sebagai berikut : H0 -&gt; Tidak terdapat
autokorelasi H1 -&gt; Terdapat autokorelasi Alpha -&gt; 0.05

    dwtest(model_2)

    ## 
    ##  Durbin-Watson test
    ## 
    ## data:  model_2
    ## DW = 2.6803, p-value = 0.8629
    ## alternative hypothesis: true autocorrelation is greater than 0

Hasil uji autokorelasi setelah data tersebut di transformasi mendapatkan
hasil p-value = 0.8629, dan nilai p-value lebih besar dari alpha (0.8629
&gt; 0.05) hal tersebut menandakan tidak tolak H0. Maka pada taraf nyata
95% tidak terdapat autokorelasi, sehingga pada asumsi ini terpenuhi.

## Regresi Linier Sederhana setelah Transformasi

### Scater Plot

    ggplot(df, aes_string(x = "xtrans", y = "ytrans")) +
      geom_point()

![](Analisis-Regresi-Pekan-7_files/figure-markdown_strict/unnamed-chunk-12-1.png)

Scatter Plot diatas menunjukkan bahwa scatter plot tersebut sudah
linier, berbeda dengan yang sebelumnya.

    summary(model_2)

    ## 
    ## Call:
    ## lm(formula = ytrans ~ xtrans, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.42765 -0.17534 -0.05753  0.21223  0.46960 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  8.71245    0.19101   45.61 9.83e-16 ***
    ## xtrans      -0.81339    0.03445  -23.61 4.64e-12 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.2743 on 13 degrees of freedom
    ## Multiple R-squared:  0.9772, Adjusted R-squared:  0.9755 
    ## F-statistic: 557.3 on 1 and 13 DF,  p-value: 4.643e-12

## Kesimpulan

Berdasarkan hasil analisis regresi linear sederhana dengan transformasi
yang telah dilakukan, didapatkan nilai adjusted R-squared sebesar 0.9755
atau dengan kata lain sekitar 97.55% variabilitas yang terdapat pada
variabel dependen (y) dapat dijelaskan oleh variabel independen (x).

Korelasi yang dihitung dari akar kuadrat dari nilai determinasi adalah
sebesar 0.988 (mendekati nilai 1) yang menunjukkan adanya korelasi yang
sangat kuat antara kedua variabel.

Maka model terbaik tersebut

***Y duga = 8.71245 - 0.81339X1***
