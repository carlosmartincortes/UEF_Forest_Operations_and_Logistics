---
title: "2nd Class of exercises - Forest Operations and Logistics UEF"
author: "Carlos Martín-Cortés & Heli Kymäläinen"
date: "2025-04-04"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# How to automatically create folders in a directory

We can use the `dir.create()` function to create folders programmatically

```{r}
# Define the directory where folders will be created
parent_directory <- "C:/Project_UEF" # Important directory separate by /

# Create the parent directory if it doesn't exist
if (!dir.exists(parent_directory)) {
  dir.create(parent_directory)
}

# Define folder names
folders <- c("data", "scripts","testing", "outputs")

# Create folders inside the parent directory
for (folder in folders) {
  dir.create(file.path(parent_directory, folder), showWarnings = FALSE)
}

# Print a message confirming folder creation
list.dirs(parent_directory, recursive = FALSE)
```

# Unzipping a file into a specific folder in R

In R, you can extract a compressed file into a specific folder using the unzip() function for ZIP files.

```{r}
direcory_zip <- 'C:/Users/carlos.martin/Downloads/Data.zip' # Only change your directory, Ctrl+C & Ctrl+V, is needed to stay into ' '

directory_data <- paste0(parent_directory,'/data/')

unzip(direcory_zip, exdir = directory_data)

```

# Automatically create folders to save solutions (for each plot)

We can use the `list.dirs()` and `grep()` functions to search for directories that include "plot" in their names.

We make a Loop for create the new folders

```{r}
# Get all subdirectories
subdirs <- list.dirs(directory_data, recursive = FALSE, full.names = TRUE)

# Filter only folders that contain the word "plot" in their name
plot_folders <- grep("Plot", basename(subdirs), value = TRUE, ignore.case = TRUE)

# Loop through the found folders and store them
for (name in plot_folders) {
  folder_create <- paste0(parent_directory,'/outputs/',name )
  if (!dir.exists(folder_create)) {
  dir.create(folder_create)
  }
}
```
