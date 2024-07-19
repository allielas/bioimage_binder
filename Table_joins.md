# Queries

## Joining puncta with cells
SQL pseudoish code
```{sql}
SELECT *
FROM patches
LEFT JOIN on cell
WHERE
  cell.ObjectNumber = patches.CellNumber
AND
  cell.ImageNumber = patches.ImageNumber
```
Want to add the smaller table (cells) to the bigger table (patches)
Key to join on is the cell obj number and patch

In R:
Quin used these tables:
```{r}
Cell_Info <- data.frame(Cell[c("ImageNumber", "ObjectNumber", "AreaShape_Area", "Children_Masked_Cell_Count", "Children_Masked_Puncta_Count", "Intensity_MeanIntensity_Enhanced_MCS", "TrackObjects_Lifetime_15", "TrackObjects_ParentObjectNumber_15", "TrackObjects_Label_15", "Location_Center_X", "Location_Center_Y" )])
Cell_Info <- Cell_Info %>%
  rename(Cell_Location_Center_X = Location_Center_X,
         Cell_Location_Center_Y = Location_Center_Y,
         Cell_Intensity_MeanIntensity_Enhanced_MCS = Intensity_MeanIntensity_Enhanced_MCS,
         Cell_AreaShape_Area = AreaShape_Area,
         Cell_Number = ObjectNumber)

Patch_Info <- data.frame(Patch[c("ImageNumber", "ObjectNumber", "AreaShape_Area", "Distance_Centroid_Cell", "Intensity_MeanIntensity_Enhanced_MCS", "Location_Center_X", "Location_Center_Y", "Parent_Cell")])
Patch_Info <- Patch_Info %>%
  rename(Patch_Location_Center_X = Location_Center_X,
         Patch_Location_Center_Y = Location_Center_Y,
         Patch_Intensity_MeanIntensity_Enhanced_MCS = Intensity_MeanIntensity_Enhanced_MCS,
         Patch_AreaShape_Area = AreaShape_Area,
         Patch_Distance_Centroid_Cell = Distance_Centroid_Cell,
         Cell_Number = Parent_Cell,
         Patch_Number = ObjectNumber)
```
Note how object number and parent cell are renamed
```{r}

Cell_Patch_Merged <- Patch_Info %>%
  left_join(Cell_Info, by = c("Cell_Number", "ImageNumber"))
Cell_Patch_Image_Merged <- Cell_Patch_Merged %>%
  left_join(Image_Info, by = c("ImageNumber"))
Cell_Image_Merge <- Cell_Info %>%
  left_join(Image_Info, by = c("ImageNumber"))

Cell_Patch_Image_Merged$frame <- Cell_Patch_Image_Merged$frame - 1
Cell_Image_Merge$frame<- Cell_Image_Merge$frame - 1
```
Such that the resultant table is the same size as the previous table.
