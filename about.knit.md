---
title: "Characters By K/D/A"
---

::: {.cell}

:::

::: {.cell}

```{.r .cell-code}
data <- read_csv("data/ow_data.csv")
```

::: {.cell-output .cell-output-stderr}

```
Rows: 296 Columns: 133
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr   (3): Hero, Skill Tier, Role
dbl (130): KDA Ratio, Pick Rate, %, Win Rate, %, Eliminations / 10min, Objec...

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


:::

```{.r .cell-code}
filtered_data <- data %>%
  filter(`Skill Tier` == "All") %>%
  select(Hero, Role, `KDA Ratio`, `Pick Rate, %`, `Win Rate, %`)

# Create separate bar charts for each role
ggplot(filtered_data, aes(x = Hero, y = `KDA Ratio`, fill = Hero)) +
  geom_bar(stat = "identity") +
  facet_wrap(~ Role, scales = "free") +  # Create separate charts for each role
  labs(title = "Hero KDA Ratio Comparison by Role",
       x = "Hero",
       y = "KDA Ratio") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1, size = 6))
```

::: {.cell-output-display}
![](about_files/figure-html/unnamed-chunk-2-1.png){width=672}
:::
:::

::: {.cell}

```{.r .cell-code}
hero_groups <- data |>
  group_by(Hero, `Skill Tier`) |>
  summarise(
    mean_KDA = mean(`KDA Ratio`),
    mean_Pick_Rate = mean(`Pick Rate, %`),
    mean_Win_Rate = mean(`Win Rate, %`),
    mean_Eliminations = mean(`Eliminations / 10min`),
    mean_Objective_Kills = mean(`Objective Kills / 10min`)
  ) |>
  ungroup()
```

::: {.cell-output .cell-output-stderr}

```
`summarise()` has grouped output by 'Hero'. You can override using the
`.groups` argument.
```


:::

```{.r .cell-code}
# Define the colors for each skill tier
tier_colors <- c("All" = "#377EB8", "Bronze" = "#4DAF4A", "Silver" = "#984EA3",
                 "Gold" = "#FF7F00", "Platinum" = "#FFFF33", "Diamond" = "#A65628",
                 "Master" = "#F781BF", "Grandmaster" = "#999999")
# Create the grouped bar chart
ggplot(hero_groups, aes(x = Hero, fill = `Skill Tier`)) +
  geom_bar(aes(y = mean_KDA), position = "dodge", stat = "identity") +
  geom_bar(aes(y = mean_Pick_Rate / 10), position = "dodge", stat = "identity") +
  geom_bar(aes(y = mean_Win_Rate / 10), position = "dodge", stat = "identity") +
  geom_bar(aes(y = mean_Eliminations / 10), position = "dodge", stat = "identity") +
  geom_bar(aes(y = mean_Objective_Kills / 10), position = "dodge", stat = "identity") +
  scale_fill_manual(values = tier_colors) +
  labs(title = "Hero Performance by Skill Tier",
       y = "Average Value",
       fill = "Skill Tier",
       caption = "Data Source: YourSource") +
  theme_minimal() +
  theme(legend.position = "top",
        plot.title = element_text(hjust = 0.5),
        axis.text.x = element_text(angle = 45, hjust = 1))
```

::: {.cell-output-display}
![](about_files/figure-html/unnamed-chunk-3-1.png){width=672}
:::
:::

