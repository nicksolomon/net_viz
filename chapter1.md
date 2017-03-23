---
title       : Visualizing relational data with `ggraph`
description : The `ggraph` package brings `ggplot2` like syntax to network visualizations.
attachments :
  slides_link : https://s3.amazonaws.com/assets.datacamp.com/course/teach/slides_example.pdf
--- type:NormalExercise lang:r key:1085797f84
## Create an `igraph` object and plot it

We'll be using the `hagelloch.df` dataframe from the `surveillance` package to
create a graph of the transmission of a measles outbreak.

*** =instructions
- Use the patient ID number (`PN`) column and the putative source of infection
(`IFTO`) column to make an `igraph` object where the vertices are patients and
the edges show who infected who.

- Plot this graph using `ggraph` with straight edges and points for nodes.

*** =hint
Read the docs

*** =pre_exercise_code
```{r}
# to prevent issue w/ surveillance pkg
'___BLOCK_SOLUTION_EXEC___'

library(igraph)
library(surveillance)
library(ggraph)
library(dplyr)
data("hagelloch")
```

*** =sample_code
```{r}
my_arrow = arrow(length = unit(5, "mm"), type = "closed")

# create an igraph object
measles_net <- ___

# plot measles_net
ggraph(___)
```

*** =solution
```{r}
my_arrow = arrow(length = unit(2, "mm"), type = "closed")

# create an igraph object
measles_net <- hagelloch.df %>% 
  select(PN, IFTO) %>%
  filter(IFTO != -1) %>% 
  as.matrix() %>% 
  graph_from_edgelist()

# plot measles_net
ggraph(measles_net) +
  geom_edge_link(arrow = my_arrow, end_cap = circle(1, "mm")) +
  geom_node_point()
```

*** =sct
```{r}
test_error()
```

--- type:NormalExercise lang:r key:cc2b65a2f6
## Add vertex attributes and map them to aesthetics in `ggraph`

*** =instructions
- Use the `vertex_attr()` function from the `igraph` package to add the
vertex attributes `class` and `num_infected` to each vertex of the graph you
made in the previous exercise. The attribute `class` corresponds to the column `CL` of the `hagelloch.df` data frame. To compute the number of infected individuals, use the function `degree()` with argument `mode = "in"`.

- Plot this graph with `num_infected` mapped to node size and `class` mapped to
node color.

*** =hint
Use the familiar `ggplot` syntax to access vertex attributes.

*** =pre_exercise_code
```{r}
# to prevent issue w/ surveillance pkg
'___BLOCK_SOLUTION_EXEC___'

library(igraph)
library(surveillance)
library(ggraph)
library(dplyr)

data("hagelloch")
hagelloch.df <- mutate(hagelloch.df, CL = as.character(CL))
```

*** =sample_code
```{r}
my_arrow = arrow(length = unit(5, "mm"), type = "closed")

measles_net <- hagelloch.df %>% 
  select(PN, IFTO) %>%
  filter(IFTO != -1) %>% 
  as.matrix() %>% 
  graph_from_edgelist()

# add the vertex attribute class
vertex_attr(measles_net, ____) <- ____

# add the vertex attribute num_infected
vertex_attr(measles_net, ____) <- ____

# plot this graph
ggraph(measles_net)
```

*** =solution
```{r}
my_arrow = arrow(length = unit(5, "mm"), type = "closed")

measles_net <- hagelloch.df %>% 
  select(PN, IFTO) %>%
  filter(IFTO != -1) %>% 
  as.matrix() %>% 
  graph_from_edgelist()

# add the vertex attribute class
vertex_attr(measles_net, "class") <- hagelloch.df[["CL"]]

# add the vertex attribute num_infected
vertex_attr(measles_net, "num_infected") <- degree(measles_net, mode = "in")

# plot this graph
ggraph(measles_net) +
  geom_edge_link() +
  geom_node_point(aes(color = class, size = num_infected))
```

*** =sct
```{r}
test_error()
```
