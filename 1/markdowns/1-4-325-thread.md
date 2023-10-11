
# Post \#69534248 [Link](https://stackoverflow.com/questions/69534248/)

## How can I make a discontinuous axis in R with ggplot2?

**Vote**: 6 (473/702) **Views**: 2052 (604/702) 

**Internal ID** \#1-4-325

Created at 2021-10-12 01:54:57

Tags: `r` `ggplot2`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I have a dataframe (`dat`) with two columns 1) `Month` and 2) `Value`. I would like to highlight that the x-axis is not continuous in my boxplot by interrupting the x-axis with two angled lines on the x-axis that are empty between the angled lines.
Example Data and Boxplot
```
library(ggplot2)
set.seed(321)
dat <- data.frame(matrix(ncol = 2, nrow = 18))
x <- c("Month", "Value")
colnames(dat) <- x
dat$Month <- rep(c(1,2,3,10,11,12),3)
dat$Value <- rnorm(18,20,2)

ggplot(data = dat, aes(x = factor(Month), y = Value)) +
  geom_boxplot() +
  labs(x = "Month") +
  theme_bw() +
  theme(panel.grid = element_blank(),
        text = element_text(size = 16),
        axis.text.x = element_text(size = 14, color = "black"),
        axis.text.y = element_text(size = 14, color = "black"))
```

The ideal figure would look something like below. How can I make this discontinuous axis in ggplot?
[](https://i.stack.imgur.com/gSiqt.png)


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2021-11-01 17:55:10

------------

You could make use of the extended axis guides in the `ggh4x` package. Alas, you won't easily be able to create the "separators" without a hack similar to the one [suggested by user Zhiqiang Wang](https://stackoverflow.com/a/69534417/7941188)
`guide_axis_truncated` accepts vectors to define lower and upper trunks. This also works for units, by the way, then you have to pass the vector inside the unit function (e.g., `trunc_lower = unit(c(0,.45), "npc")` !
```
library(ggplot2)
library(ggh4x)

set.seed(321)
dat <- data.frame(matrix(ncol = 2, nrow = 18))
x <- c("Month", "Value")
colnames(dat) <- x
dat$Month <- rep(c(1,2,3,10,11,12),3)
dat$Value <- rnorm(18,20,2)

# this is to make it slightly more programmatic
x1end <- 3.45
x2start <- 3.55

p <-
ggplot(data = dat, aes(x = factor(Month), y = Value)) +
  geom_boxplot() +
  labs(x = "Month") +
  theme_classic() +
  theme(axis.line = element_line(colour = "black"))

p +
  guides(x = guide_axis_truncated(
    trunc_lower = c(-Inf, x2start),
    trunc_upper = c(x1end, Inf)
  ))
```

![](https://i.imgur.com/4FM1pgh.png)
[reprex package](https://reprex.tidyverse.org)
The below is taking user Zhiqiang Wang's hack a step further. You will see I am using simple trigonometry to calculate the segment coordinates. in order to make the angle actually look as it is defined in the function, you would need to set `coord_equal`.
```
# a simple function to help make the segments
add_separators <- function(x, y = 0, angle = 45, length = .1){
  add_y <-  length * sin(angle * pi/180)
  add_x <- length * cos(angle * pi/180)
  ## making the list for your segments
  myseg <- list(x = x - add_x, xend = x + add_x, 
                y = rep(y - add_y, length(x)), yend = rep(y + add_y, length(x)))
  ## this function returns an annotate layer with your segment coordinates
  annotate("segment", 
           x = myseg$x, xend = myseg$xend,
           y = myseg$y, yend = myseg$yend) 
}

# you will need to set limits for correct positioning of your separators
# I chose 0.05 because this is the expand factor by default 
y_sep <- min(dat$Value) -0.05*(min(dat$Value))

p +
  guides(x = guide_axis_truncated(
    trunc_lower = c(-Inf, x2start),
    trunc_upper = c(x1end, Inf)
  )) +
  add_separators(x = c(x1end, x2start), y = y_sep, angle = 70) +
  # you need to set expand to 0
  scale_y_continuous(expand = c(0,0)) +
  ## to make the angle look like specified, you would need to use coord_equal()
  coord_cartesian(clip = "off", ylim = c(y_sep, NA))
```

![](https://i.imgur.com/18WV1dC.png)


------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-10-12 02:28:57

------------

I think it is possible to get what you want. It may take some work.
Here is your graph:
```
library(ggplot2)
set.seed(321)
dat <- data.frame(matrix(ncol = 2, nrow = 18))
x <- c("Month", "Value")
colnames(dat) <- x
dat$Month <- rep(c(1,2,3,10,11,12),3)
dat$Value <- rnorm(18,20,2)

p <- ggplot(data = dat, aes(x = factor(Month), y = Value)) +
  geom_boxplot() +
  labs(x = "Month") +
  theme_bw() +
  theme(panel.grid = element_blank(),
        text = element_text(size = 16),
        axis.text.x = element_text(size = 14, color = "black"),
        axis.text.y = element_text(size = 14, color = "black"))
```

Here is my effort:
```
p + annotate("segment", x = c(3.3, 3.5), xend = c(3.6, 3.8), y = c(14, 14), yend = c(15, 15))+
  coord_cartesian(clip = "off", ylim = c(15, 25))
```

Get something like this:
[](https://i.stack.imgur.com/MN7TC.png)
If you want to go further, it may take several tries to get it right:
```
p + annotate("segment", x = c(3.3, 3.5), xend = c(3.6, 3.8), y = c(14, 14), yend = c(15, 15))+
  annotate("segment", x = c(0, 3.65), xend = c(3.45, 7), y = c(14.55, 14.55), yend = c(14.55, 14.55)) +
  coord_cartesian(clip = "off", ylim = c(15, 25)) +
  theme_classic()+
  theme(axis.line.x = element_blank())
```

Just replace axis with two new lines. This is a rough idea, it may take some time to make it perfect.
[](https://i.stack.imgur.com/uys2Z.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-10-12 02:18:18

------------

You could use `facet_wrap`. If you assign the first 3 months to one group, and the other months to another, then you can produce two plots that are side by side and use a single y axis.
It's not exactly what you want, but it will show the data effectively, and highlights the fact that the x axis is not continuous.
```
dat$group[dat$Month %in% c("1", "2", "3")] <- 1
dat$group[dat$Month %in% c("10", "11", "12")] <- 2

ggplot(data = dat, aes(x = factor(Month), y = Value)) +
  geom_boxplot() +
  labs(x = "Month") +
  theme_bw() +
  theme(panel.grid = element_blank(),
        text = element_text(size = 16),
        axis.text.x = element_text(size = 14, color = "black"),
        axis.text.y = element_text(size = 14, color = "black")) +
  facet_wrap(~group, scales = "free_x")
```

`set.seed`
[](https://i.stack.imgur.com/z4yzu.png)


------------
    
    