# NDVI_CALCULATOR



Development of a Python tool for analysing Sentinel-2 satellite images: NDVI calculation. \
**‼️** The readme file is written in English simply because that is my personal preference. And the source code is available in `.py` on GitHub, but it was originally developed in a **Jupyter Notebook `.ipynb`**.


> Pour accèder au code source -> [NDVI_CALCULATOR](NDVI_CALCULATOR.py)


### Overview
This project analyzes vegetation dynamics using Sentinel-2 satellite imagery.
The goal is to compute and visualize the NDVI (Normalized Difference Vegetation Index) for environmental assessment.

<img width="633" height="487" alt="image" src="https://github.com/user-attachments/assets/fef5b8ee-5077-4ce8-9147-1aaf5f554b78" />


```
Satellite image taken of the city of Blois in France on Copernicus Browser.
This image has a “Max. cloud coverage:” of 0% for better visibility, which brings us to the period of February 2023.
```

### Data
Source : Copernicus Sentinel-2 \
Resolution : 10m \
Bands used : Band 4 (Red), Band 8 (NIR)

### Methodology

#### 1. Data acquisition
The images come from the Sentinel-2 satellite, which provides high-resolution multispectral images (10 m to 60 m depending on the bands). \
Two bands are used to calculate the NDVI : 
- B04 (Red): captures visible red light.
- B08 (NIR / Near Infrared): captures near-infrared light.
  
These bands are downloaded as TIFF files, the standard format for geospatial raster imagery.

#### 2. Loading and normalising bands

Each band is read using the Rasterio library, which allows for easy manipulation of geospatial raster files. \
Pixel values are converted to floats to ensure accuracy during calculation. \
Normalisation is performed by dividing the values by 10,000 to convert Sentinel-2 values (DN – Digital Number) to true reflectance (0 to 1).

```
nir /= 10000 red /= 10000
```

#### 3. Calculation of NDVI
NDVI is calculated pixel by pixel using the standard formula:

$$
\text{NDVI} = \frac{\text{NIR} - \text{Red}}{\text{NIR} + \text{Red}}
$$

- Infinite or undefined values (division by zero) are replaced by NaN.
- The values are then clipped to remain within the range [-1, 1].

```python
def calculate_ndvi(nir:int, red:int):
    nir /= 10000
    red /= 10000
    ndvi = (nir - red) / (nir + red)
    ndvi[np.isinf(ndvi)] = np.nan
    return np.clip(ndvi, -1, 1)
```
A value close to 1 indicates dense, healthy vegetation, 0 corresponds to an area without vegetation (bare ground, roads), and negative values indicate water or artificial areas.


#### 4. NDVI statistics
Overall statistics are calculated to characterize the vegetation in the image:
- Average NDVI: reflects the overall health of the vegetation
- Minimum and maximum NDVI: identifies extremes, areas that are highly degraded or densely vegetated

```python
def statistics(ndvi):
    print("NDVI moyen:", np.nanmean(ndvi))
    print("NDVI min:", np.nanmin(ndvi))
    print("NDVI max:", np.nanmax(ndvi))
```
#### 5. Visualization
The NDVI is visualized as a colored image with an “RdYlGn” (red-yellow-green) colormap:
- Red: sparse vegetation
- Green: dense, healthy vegetation \

```python
def plot_ndvi(ndvi, title="NDVI"):
    plt.figure(figsize=(8, 6))
    plt.imshow(ndvi, cmap="RdYlGn")
    plt.colorbar(label="NDVI")
    plt.title(title)
    plt.axis("off")
    plt.show()
```

### Workflow summary

1. Download Sentinel-2 B04 and B08 bands.
2. Read and normalize raster bands with Rasterio.
3. Calculate NDVI for each pixel.
4. Perform statistical analysis of the index.
5. Visualize the index for spatial interpretation.




