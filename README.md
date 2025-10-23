# Álbumes de Sabrina Carpenter

*Octubre 23, 2025*

El sarcasmo y el brillo, una perfecta mezcla para la creación de críticas y la autoconciencia, es así, como Sabrina Carpenter se ha transformado en un gran icóno de la música pop, en este útlimo tiempo, logrando una mezcla de cruces generacionales y así también importantes mensajes. Es así, como cada álbum ha sido una propuesta innovadora, en la cual el color, la estética y procesos de la vida en particular, han sido importantes en la transmisión de su mensaje y así un punto clave en la autenticidad.

Mi acercamiento a la música de Sabrina Carpenter ha sido reciente pero bastante interesante, ya que ha simple vista se ha mostrado como una artista que sigue los escalones de la belleza y la feminidad, sin embargo, ha logrado utilizar su imagen para poder romper como muchos esterotipos y así con ideas sobre lo que significa ser una chica rubia de Disney. Es así, que uno de sus álbumes que me han llamado más la atención y he estado escuchando es "Man´s Best Friend" y por ello quería averiguar que tan popular ha sido, y así también descubrir si soy moda al momento de escuchar música o me encuentró un espacio menos explorado de esta.

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

Para el gráfico necesitaremos cargar una libreria en específico, para lograr el efecto que buscamos.

```{r}
devtools::install_github("clauswilke/ggtextures")
install.packages("ggtextures")
library(ggtextures)
```

Una vez ya cargado todas las librerias generales, nos introduciremos en la API de Spotify para poder utilizar sus datos y así encontrar lo que estamos buscando.

Para ello, debemos cargar un paquete propio de spotify

```{r}
install.packages('spotifyr')
library(spotifyr)
```

y posterior ingresar los datos personales de cliente de spotify, para poder encontrar estos datos, fue una de los pasos más enredados, pero se debe crear una app, que te los entrega en una página, ojalá lo puedas lograr mas rápido que yo

```{r}
Sys.setenv(SPOTIFY_CLIENT_ID = 'ingresar id')
Sys.setenv(SPOTIFY_CLIENT_SECRET = 'ingresar id')

access_token <- get_spotify_access_token()
```

Por ello, es importante verificiar que ingresamos a estos datos, para ello debes ingresar `access_token` y te deberán aparecer muchos números y letras, y es ahí cuando ya sabes que ingresaste correctamente los datos.

Ahora, lo que nos interesa... encontrar a Sabrina Carpenter como artista en Spotify

```{r}
sabrina <- search_spotify("Sabrina Carpenter", type = "artist")
sabrina$id
```

ya que tenemos identificada a la cantante, nos introduciremos a conocer los álbumes, aqui se abrirá la base de datos, la cual tendrá mucha más información que lo que buscamos, pero calma, paso a paso.

```{r}
sabrina_albums <- get_artist_albums(sabrina$id[1], include_groups = "album")
unique(sabrina_albums$name)

View(sabrina_albums)
```

Como ya tenemos los álbumes, necesitamos limpiar nuestra base de datos con la información que nos interesa conocer, que seria Id (este hay que mantenerlo para que spotify lo reconozca), nombre del álbum y fecha de lanzamiento (opcional), las podemos renombrar como queramos.

```{r}
sabrina_albums <- sabrina_albums %>%
  select(id, name, release_date, total_tracks) %>%
  distinct(name, .keep_all = TRUE) %>%
  rename(
    nombre = name,
    `fecha de lanzamiento` = release_date,
    canciones = total_tracks
  )

View(sabrina_albums)
```

Teniendo los datos ya renombrados, nos hace falta todavía conocer la popularidad. Para poder entender esta, Spotify la clasifica del 0 al 100, siendo 100 el álbum más popular de ese momento y 0 un álbum con nula o poca actividad reciente.

```{r}
album_popularity <- sabrina_albums %>%
  rowwise() %>%
  mutate(
    popularidad = {
      tracks_data <- get_album_tracks(id)
      track_ids <- tracks_data$id
      track_info <- get_tracks(track_ids)
      mean(track_info$popularity, na.rm = TRUE)
    }
  ) %>%
  ungroup() %>%
  select(nombre, `fecha de lanzamiento`, canciones, popularidad) %>%
  arrange(desc(popularidad))

View(album_popularity)
```

Por comodidad, recomiendo ordenar los álbumes en la base de datos de mayor a menor popularidad, para tener una claridad de que es lo que se espera en el gráfico.

```{r}
sabrina_plot <- album_popularity %>%
  arrange(desc(popularidad)) %>%
  mutate(nombre = factor(nombre, levels = rev(unique(nombre))))
```

**Comienza la creatividad...**

Ahora, ya que tenemos la base de datos con las columnas que nos interesan y ordenadas como deseamos debemos pensar como queremos el gráfico y en función a ello, solicitar lo que se desea. Para mi caso, me inspiré en un gráfico de una autora que realizó el análisis con los álbumes de Taylos Swift y me gustó el formato (dejaré el link de su perfil al final del trabajo).

Es por ello, que previo a comenzar a armar el gráfico, hay que activar ciertas fuentes decorativas

```{r}
font_add_google("Pacifico", "pacifico")

showtext_auto()
```

Además, como a una le gusta complicarse la vida, decidí que las barras del gráfico fueran con brillos, por lo que para ello, fue la parte más complicada, ya que tuve que ir experimentando y buscando ayuda, hasta lograr el formato deseado. Para ello, se necesita descargar una imagen de interés personal, puede ser de cualquier cosa, de la cual nosotros queramos que se vean las barras de nuestro gráfico. En mi caso, decidí que fueran celestes con brillos, por lo que busqué la imagen en google y la agregué a la carpeta de mi computador donde esta agregado todo mi repositorio para poder utilizarla.

El código, consiste en img = "ingresar el nombre de la imagen"

```{r}
img = "brillo-brillo_23-2148110774.jpg"
```

**Finalmente**, lo que estabamos esperando, a armar el gráfico

```{r}
ggplot(sabrina_plot, aes(x = nombre, y = popularidad)) +
  geom_textured_col(image = img, color = "white", width = 0.8) +  
  geom_text(aes(label = round(popularidad, 1)), 
            hjust = -0.2, color = "gray40", fontface = "bold", 
            family = "pacifico", size = 4) +
  labs(
    title = "Popularidad de los álbumes de Sabrina Carpenter",
    subtitle = "Basado en la popularidad de sus canciones",
    x = NULL,
    y = "Popularidad promedio"
  ) +
  theme_minimal(base_family = "pacifico") +
  theme(
    plot.title = element_text(size = 18, color = "#0099CC", hjust = 0.1),
    plot.subtitle = element_text(size = 12, color = "gray40", hjust = 0.1),
    axis.text.y = element_text(size = 12, color = "gray30", hjust = 1),
    axis.title.y = element_text(size = 14, face = "bold", color = "black"),
    panel.grid.major.y = element_blank(),
    panel.grid.minor = element_blank(),
    plot.background = element_rect(fill = "white", color = NA)
  ) +
  coord_flip(clip = "off") + 
  scale_y_continuous(limits = c(0, 100), breaks = seq(0, 100, by = 10)) +
  expand_limits(y = max(sabrina_plot$popularidad) * 1.15)
```

Este código en específico `geom_textured_col(image = img, color = "white", width = 0.8)` , es el encargado de ingresar la imagen como las barras del gráfico, por lo que es importante que se ingrese correctamente.

También, es importante saber que con todos los datos, sea color, tipo de letra, tamaño, etc. son gustos personales, por lo que puedes jugar con ellos, de acuerdo a tu creatividad y lo que personalmente desees.

Y es así, como en conclusión, si tu albúm más escuchado como es el mio, es Man´s Best Friend, eres como la mayoria de sus oyentes, ya que es el que ha tenido más popularidad.

*Aquí dejo el link de la autora del trabajo de Taylor Swift que fue de gran inspiración* <https://github.com/aaumaitre/taylor_swift/blob/1e09029a48e6dad64e6d5488543d6d22605593bd/README.md>
