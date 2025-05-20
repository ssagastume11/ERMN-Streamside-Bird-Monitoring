# ERMN-Streamside-Bird-Monitoring

**Author:** Sergio E. Sagastume  
**Repository:** [ERMN-Streamside-Bird-Monitoring](https://github.com/ssagastume11/ERMN-Streamside-Bird-Monitoring)

---

## 📌 Introduction

This project analyzes bird monitoring data collected along streamside habitats within the Eastern Rivers and Mountains Network (ERMN) of the U.S. National Park Service between 2011 and 2022. It explores trends in bird abundance, detection variability, and habitat use over time.

---

## 🌍 Scenario

I am acting as a data analyst supporting ecological monitoring initiatives. The ERMN team is seeking to understand long-term trends in bird populations along stream corridors across six national park units. My work will help identify shifts in avian community structure and guide future conservation strategies.

---

## ❓ Ask

**Business Task:** Identify trends in bird abundance, detection variability, and habitat use near streams from 2011 to 2022 using certified ERMN streamside bird point count data.

---

## 📦 Prepare 
**Dataset:**  ERMN Streamside Bird Monitoring Data (2011–2022).  
**Source:** [Catalog.Data.Gov](https://catalog.data.gov/dataset/ermn-streamside-bird-monitoring-data-2011-2022)

```{r}
# Load necessary libraries
library(tidyverse)
library(lubridate)
library(ggplot2)
library(scales)

# Load CSV data file
bird_data <- read_csv("ERMN Bird Data Analysis/NPS_ERMN_StreamsideBirdProtocol_2011_2022_Data_Certified.csv")

```

---

## 🧹 Process

```{r}
# Clean and merge datasets
ROMOFish_TroutData_SurveyEvent <- ROMOFish_TroutData_SurveyEvent %>%
  mutate(eventDate = ymd(startDate), year = year(eventDate))

trout_data <- ROMOFish_TroutData_Occurrence %>%
  left_join(ROMOFish_TroutData_SurveyEvent, by = "eventID") %>%
  left_join(ROMOFish_TroutData_Taxon, by = "taxonID")

# Filter clean biological records
trout_data_clean <- trout_data %>%
  filter(!is.na(length) & !is.na(mass) & length > 0 & mass > 0) %>%
  mutate(length_category = cut(length,
                               breaks = c(0, 100, 200, 300, 400),
                               labels = c("0-100mm", "101-200mm", "201-300mm", "301-400mm"),
                               right = FALSE))

```

## 📊 Analyze

![Total Trout by Species and Year](https://raw.githubusercontent.com/ssagastume11/Fisheries-Inventory-Trout-Data-ROMO-2021-2022/refs/heads/main/Total%20Trout%20by%20Species.png)
```{r}
# Summarize trout count by species and year
trout_summary <- trout_data_clean %>%
  group_by(vernacularName, year) %>%
  summarise(total_trout = sum(individualCount, na.rm = TRUE), .groups = "drop")

# Visualization
library(ggplot2)

ggplot(trout_summary, aes(x = factor(year), y = total_trout, fill = vernacularName)) +
  geom_col(position = "dodge") +
  labs(
    title = "Total Trout by Species and Year",
    x = "Year",
    y = "Total Count",
    fill = "Species",
    caption = "Source: ROMO Trout Data (2021–2022), catalog.data.gov"
  ) +
  theme_minimal()
```

---

## 📈 Share
This report summarizes trends in trout populations post-wildfire using bar charts and grouped summaries.
* Trout abundance varied significantly between 2021 and 2022.
* Streams affected by wildfires had trout with reduced average length and weight.
* Brook Trout appeared most frequently in the dataset.

---

# ✅ Act

```{r}
# Identify significant changes in trout populations between years
significant_changes <- trout_summary %>%
  pivot_wider(names_from = year, values_from = total_trout, values_fill = 0) %>%
  mutate(change = `2022` - `2021`,
         trend = case_when(
           change > 0 ~ "Increase",
           change < 0 ~ "Decrease",
           TRUE ~ "No Change"
         ))

# Display results
print(significant_changes)
```
---

# 💡 Recommendations
1. **Increase Monitoring Frequency**: Continue yearly sampling to detect ecological changes.
2. **Focus Restoration Efforts**: Allocate conservation resources to sites where trout numbers are declining.
3. **Improve Public Engagement**: Share simplified versions of findings with local communities and visitors.
4. **Protect Sensitive Habitats**: Use this data to identify and protect habitats supporting at-risk trout species.
