---
title       : Visualizing relational data with ggraph
description : The ggraph package brings ggplot2 like syntax to network visualizations.
--- type:VideoExercise lang:r aspect_ratio:62.5 xp:50 skills:1,4 key:1093b879d9
## Relational data and ggraph

*** =video_link
//player.vimeo.com/video/209834264

--- type:NormalExercise lang:r xp:100 skills:1,4 key:1085797f84
## Create an igraph object and plot it

We'll be using the `hagelloch.df` dataframe from the `surveillance` package to
create a graph of the transmission of a measles outbreak.

*** =instructions
- Use the patient ID number (`PN`) column and the putative source of infection
(`IFTO`) column to make an `igraph` object where the vertices are patients and
the edges show who infected who. Make sure to put these to columns in the right order!

- Plot this graph using `ggraph` with straight edges and points for nodes.

*** =hint
Make sure that the edgelist is converted to a matrix, and that you're using the `geom_edge_link()` and `geom_node_point()` functions.
*** =pre_exercise_code
```{r}
# to prevent issue w/ surveillance pkg

library(igraph)
library(ggraph)
library(dplyr)

load(url("https://github.com/nicksolomon/net_viz/blob/master/datasets/hagelloch.df.RData?raw=true"))

hagelloch.df <- hagelloch.df %>% 
  filter(IFTO != -1)
```

*** =sample_code
```{r}
my_arrow = arrow(length = unit(5, "mm"), type = "closed")
my_end_cap = circle(1, "mm")

# create an igraph object
measles_net <- ___

# plot measles_net
ggraph(___)
```

*** =solution
```{r}
my_arrow = arrow(length = unit(2, "mm"), type = "closed")
my_end_cap = circle(1, "mm")

# create an igraph object
measles_net <- hagelloch.df %>% 
  select(IFTO, PN) %>%
  as.matrix() %>% 
  graph_from_edgelist()

# plot measles_net
ggraph(measles_net) +
  geom_edge_link(arrow = my_arrow, end_cap = my_end_cap) +
  geom_node_point()
```

*** =sct
```{r}
test_error()

ex() %>% check_expr("as_edgelist(measles_net)") %>%
  check_result() %>%
  check_equal(incorrect_msg = "Make sure that your columns are in the right order.")
  
ex() %>% check_function("ggraph",
                        not_called_msg = "Plot the graph with the `ggraph()` function.") %>% 
  check_arg("graph", arg_not_specified_msg = "`measles_net` should be the first argument to `ggraph`.")

edge_state <- ex() %>% check_function("geom_edge_link")
edge_state %>% check_arg("arrow",
                         arg_not_specified_msg = "Add arrows to your plot.") %>% 
  check_equal(incorrect_msg = "Use the already defined `my_arrow`.")
edge_state %>% check_arg("end_cap",
                         arg_not_specified_msg = "Add end_caps to your plot.") %>% 
  check_equal(incorrect_msg = "Use the already defined `my_end_cap`.")

ex() %>% check_function("geom_node_point")
```

--- type:NormalExercise lang:r xp:100 skills:1,4 key:cc2b65a2f6
## Add vertex attributes and map them to aesthetics in ggraph

*** =instructions
- Use the `vertex_attr()` function from the `igraph` package to add the
vertex attributes `class` and `num_infected` to each vertex of the graph you
made in the previous exercise. The attribute `class` corresponds to the column `CL` of the `hagelloch.df` data frame. To compute the number of infected individuals, use the function `degree()` with argument `mode = "out"`.

- Plot this graph with `num_infected` mapped to node size and `class` mapped to
node color.

*** =hint
Use the familiar `ggplot` syntax to access vertex attributes when plotting.

*** =pre_exercise_code
```{r}
# to prevent issue w/ surveillance pkg
library(igraph)
library(ggraph)
library(dplyr)

load(url("https://github.com/nicksolomon/net_viz/blob/master/datasets/hagelloch.df.RData?raw=true"))
hagelloch.df <- hagelloch.df %>% 
  mutate(CL = as.character(CL))
  
```

*** =sample_code
```{r}
my_arrow = arrow(length = unit(5, "mm"), type = "closed")
my_end_cap = circle(1, "mm")

measles_net <- hagelloch.df %>% 
  select(IFTO, PN) %>%
  filter(IFTO != -1) %>% 
  as.matrix() %>% 
  graph_from_edgelist()

# add the vertex attribute class
vertex_attr(measles_net, ____) <- ____

# add the vertex attribute num_infected
vertex_attr(measles_net, ____) <- ____

# plot this graph
ggraph(____)
```

*** =solution
```{r}
my_arrow = arrow(length = unit(5, "mm"), type = "closed")
my_end_cap = circle(1, "mm")

measles_net <- hagelloch.df %>% 
  select(IFTO, PN) %>%
  filter(IFTO != -1) %>% 
  as.matrix() %>% 
  graph_from_edgelist()

# add the vertex attribute class
vertex_attr(measles_net, "class") <- hagelloch.df[["CL"]]

# add the vertex attribute num_infected
vertex_attr(measles_net, "num_infected") <- degree(measles_net, mode = "out")

# plot this graph
ggraph(measles_net) +
  geom_edge_link(arrow = my_arrow, end_cap = my_end_cap) +
  geom_node_point(aes(color = class, size = num_infected))
```

*** =sct
```{r}
test_error()

ex() %>% check_expr("as_edgelist(measles_net)") %>%
  check_result() %>%
  check_equal(incorrect_msg = "Make sure that your columns are in the right order.")
  
ex() %>% check_expr("vertex_attr(measles_net, 'class')") %>% 
  check_result(error_msg = "Make sure you have created the 'class' attribute.") %>% 
  check_equal(incorrect_msg = "Is the 'class' attribute equal to the column `CL`?")
  
ex() %>% check_expr("vertex_attr(measles_net, 'num_infected')") %>% 
  check_result(error_msg = "Make sure you have created the 'num_infected' attribute.") %>% 
  check_equal(incorrect_msg = "Is the 'num_infected' attribute equal to the out-degree of each vertex?")
  
ex() %>% check_function("ggraph",
                        not_called_msg = "Plot the graph with the `ggraph()` function.") %>% 
  check_arg("graph", arg_not_specified_msg = "`measles_net` should be the first argument to `ggraph`.")

edge_state <- ex() %>% check_function("geom_edge_link")
edge_state %>% check_arg("arrow",
                         arg_not_specified_msg = "Add arrows to your plot.") %>% 
  check_equal(incorrect_msg = "Use the already defined `my_arrow`.")
edge_state %>% check_arg("end_cap",
                         arg_not_specified_msg = "Add end_caps to your plot.") %>% 
  check_equal(incorrect_msg = "Use the already defined `my_end_cap`.")

ex() %>% check_function("geom_node_point")

aes_state <- ex() %>% check_function("aes")
aes_state %>% check_arg("color",
                        arg_not_specified_msg = "Color the vertices according to class.")

aes_state %>% check_arg("size",
                        arg_not_specified_msg = "Map vertex size to `num_infected`.")
```
