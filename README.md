# 2nd Class of exercises - Forest Operations and Logistics UEF
## Carlos Martín-Cortés & Heli Kymäläinen
## 2025-04-04

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

# 3rd Class
## Carlos Martín-Cortés & Heli Kymäläinen
## 2025-04-07


```{r}
install.packages(c("sf", "readxl", "writexl", "dplyr"))
library(sf)       
library(writexl)  
library(dplyr)    



route_results <- "D:/Clases_UEF/Qgis/Results"
folders_plot <- list.files(path = route_results, pattern = "Plot", full.names = TRUE, include.dirs = TRUE)
specific_folder <- folders_plot[1]
file_shp_plot <- list.files(path = specific_folder, pattern = "^Plot.*\\.shp$", full.names = TRUE)
plot <- st_read(file_shp_plot)
table_plot <- st_drop_geometry(plot)
table_plot <- subset(table_plot, select = -fid)
columnes_plot <- colnames(table_plot)

names_routes <- list.files(path = specific_folder, pattern = "^Route.*\\.shp$")
names_routes <- gsub("\\.shp$", "", names_routes)


columnes <- c("Name", columnes_plot , names_routes)
table <- data.frame(matrix(ncol = length(columnes), nrow = 0))
colnames(table) <- columnes

for (folder in folders_plot) {
  specific_folder <- folder
  name <- basename(specific_folder)
  
  # Plot
  file_shp_plot <- list.files(path = specific_folder, pattern = "^Plot.*\\.shp$", full.names = TRUE)
  plot <- st_read(file_shp_plot)
  table_plot <- st_drop_geometry(plot)
  table_plot <- subset(table_plot, select = -fid)
  table_plot$Name  <- name
  table_plot <- table_plot[order(table_plot$Slope), ]
  
  # Route
  files_shp_route <- list.files(path = specific_folder, pattern = "^Route.*\\.shp$", full.names = TRUE)
  
  for (file in files_shp_route) {
    route_name <- tools::file_path_sans_ext(basename(file))
    route <- st_read(file)
    table_route <- st_drop_geometry(route)
    # Transfer information
    table_plot[[route_name]] <- table_route$Dist_km
  }
  table <- rbind(table,table_plot)
}

write_xlsx(table, paste0(route_results,"/table.xlsx"))
```


