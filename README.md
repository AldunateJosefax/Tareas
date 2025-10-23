# Álbumes de Sabrina Carpenter

*Octubre 23, 2025*

El sarcasmo y el brillo, una perfecta mezcla para la creación de críticas y la autoconciencia, es así, como Sabrina Carpenter se ha transformado en un gran icóno de la música pop, en este útlimo tiempo, logrando una mezcla cruces generacionales y así también importantes mensajes. Es así, como cada álbum ha sido una propuesta innovadora, en la cual el color, la estética y procesos de la vida en particular, han sido importantes en la transmisión de su mensaje y así un punto clave en la autenticidad.

Mi acercamiento a la música de Sabrina Carpenter ha sido reciente pero bastante interesante, ya que ha simple vista se ha mostrado como una artista que sigue los escalones de la belleza y la feminidad, sin embargo, ha logrado utilizar su imagen para poder romper como muchos esterotipos y así con ideas sobre lo que significa ser una chica rubia en Disney. Es así, que uno de sus álbumes que me han llamado más la atencipon y he estado escuchando es "Man´s Best Friend" y por ello quería averiguar que tan popular ha sido, y así también descubrir si soy moda al momento de escuchar música o me encuentró un espacio menos explorado de esta.

Es así que buscaremos descubrir la popularidad de sus álbumes a partir de los datos otorgados por Spotify

### **En primer lugar,** es importante cargar todas los paquetes que utilizaremos a lo largo del trabajo.

```{r}
install.packages("tidyverse")
install.packages("readxl")
install.packages("showtext")
install.packages("sysfonts")
install.packages("dplyr")
```

Ahora haremos llamado a sus librerias correspondientes

```{r}
library(showtext)
library(sysfonts)
library(tidyverse)
library(readxl)
library(dplyr)
library(ggplot2)
```

**Importante:**

Para el gráfico necesitameros cargar una libreria en específico, para lograr el efecto que buscamos.

```{r}
devtools::install_github("clauswilke/ggtextures")
install.packages("ggtextures")
library(ggtextures)
```
