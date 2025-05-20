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

Data cleaning included:
- Remove missing or duplicate observations
– Converting date and time fields
- Filtering observations with full species IDs
- Merging habitat, climate, and bird count data

```{r}
# Example: Processing start date/time and filtering
bird_data <- bird_data %>%
  mutate(start_date = mdy(start_date),
         year = year(start_date)) %>%
  filter(!is.na(species_code))

```

## 📊 Analyze

![Total Bird Detections Per Year](https://raw.githubusercontent.com/ssagastume11/ERMN-Streamside-Bird-Monitoring/refs/heads/main/Total_Bird_Detections_Per_Year.png)
```{r}
# Total bird count by year
abundance_trends <- bird_data %>%
  group_by(year) %>%
  summarise(total_birds = sum(count, na.rm = TRUE))

# Visualization
ggplot(abundance_trends, aes(x = year, y = total_birds)) +
  geom_line(color = "forestgreen", size = 1.2) +
  geom_point(size = 2) +
  labs(title = "Trends in Total Bird Abundance (2011–2022)",
       x = "Year",
       y = "Total Bird Count") +
  theme_minimal() +
  theme(plot.caption = element_text(hjust = 0.5)) +
  labs(caption = "Source: National Park Service, ERMN Streamside Bird Monitoring Data 2011–2022")

```

---

## 📣 Share
This analysis was compiled into an RMarkdown report with online commentary and visualizations to help stakeholders interpret patterns in riparian bird populations. The graphs and data summaries are intended for ecologists, park managers, and policymakers.

---

# ✅ Act
Translated the findings into actions by summarizing the key trends:
```{r}
# Change analysis
abundance_changes <- abundance_trends %>%
  arrange(year) %>%
  mutate(change = total_birds - lag(total_birds),
         trend = case_when(
           change > 0 ~ "Increase",
           change < 0 ~ "Decrease",
           TRUE ~ "No Change"
         ))

print(abundance_changes)

```
This result highlights periods of notable increases or decreases in bird abundance, helping to direct attention to specific years or changes that require ecological interpretation or a management response.

---

# 💡 Recommendations
1. **Continue Long-Term Monitoring**: Ensure consistency in annual point counts to capture long-term ecosystem shifts.
2. **Prioritize Habitat Restoration**: Focus restoration efforts in parks or years where bird abundance dropped significantly.
3. **Engage Local Communities**: Share simplified trends and conservation insights through interpretive materials.
4. **Explore Climate & Weather Impacts**: Extend analysis to link bird trends to weather patterns or stream flow variability.
5. **Integrate With Other Monitoring**: Combine bird, vegetation, and hydrological data for a fuller ecosystem picture.
