# Renishaw WiRE WDF access for GNU Octave

Adopting MATLAB's renishaw-wire-wdf-access to GNU Octave 

https://www.mathworks.com/matlabcentral/fileexchange/161821-renishaw-wire-wdf-access

# Welcome to the Renishaw WiRE WDF access package for Matlab

This package gives access to files with Renishaw WiRE WDF data format from Renishaw Raman systems. It allows read and write access, for several parts of the WDF file, including spectral data, map data and header information. 

## License

This package is supplied dual-licensed under the terms of the Apache 2.0 license, which can be found in the accompanying LICENSE.APACHE file or at http://www.apache.org/licenses/LICENSE-2.0, and the BSD 3-Clause license, which can be found in the accompanying LICENSE.BSD file. You can choose between these licenses if you use this package.

`SPDX-License-Identifier: Apache-2.0 OR BSD-3-Clause`

## Setting up the WdfReader for use in Matlab

Ensure all the files in the repository are present in your working folder within Matlab.

| Matlab file name      | Summary|
| :-------------------- | :-------------------------------------------- |
| WdfCreate.m           | Class for creating a new WDF file             |
| WdfReader.m           | Class for access to WDF file                  |
| WdfError.m            | Custom error type for WdfReader               |
| WdfUtils.m            | Various functions for working with Wdf data   |
| WiREDataType.m        | Enumeration of data types used in WiRE        |
| WiREDataUnit.m        | Enumeration of data units used in WiRE        |
| WiREKeys.m            | Enumeration of keys used in WiRE              |
| WiREMeasurementType.m | Enumeration of measurement types used in WiRE |
| WiREScanBasicType.m   | Enumeration of scan types used in WiRE        |
| WiREScanType.m        | Enumeration of scan types used in WiRE        |

## Example Code

Once all the files are in the working directory, along with a valid WDF file, the reader can be used.

### Instantiating the WdfReader Object

To instantiate the wdf object, from "MyWDF.wdf", use

```Matlab
wdfName = 'MyWDF.wdf';
wdf = WdfReader(wdfName);
```

### WdfReader Object Properties

Once the wdf object has been created the following properties may be accessed.

`Title`
: The measurement title.

`Username`
: The name of the user who acquired the data.

`Comment`
: Optional description of the data.

`MeasurementType`
: The measurement type of the data in the file.

`ScanType`
: The scan type used to acquire the data.

`LaserWavenumber`
: The wavenumber of the laser used.

`Count`
: The actual number of spectra that are stored in the file (the number of spectra collected).

`SpectralUnits`
: a WiREDataUnit value indicating the units of measurement associated with the spectral data.

`XListType`
: a WiREDataType value indicating the type (or meaning) of the values stored in the X-list.

`XListUnits`
: a WiREDataUnit value indicating the units of measurement associated with the X-list data.

`YListType`
: a WiREDataType value indicating the type (or meaning) of the values stored in the Y-list.

 `YListUnits`
: a  WiREDataUnit value indicating the units of measurement associated with the Y-list data.

`PointsPerSpectrum`
: the number of elements in each spectrum / dataset. Equal to (XListLength * YListLength).

`DataOriginCount`
: the number of Data Origin Lists in the file.

`Capacity`
: the maximum number of spectra that can be stored in the file.

`ApplicationName`
: the name of the software used to acquire the data.

`ApplicationVersion`
: the software version used to acquire the data (as a 4-element vector: [major, minor, patch, build]).

`XListLength`
: the number of elements in the X-list.

`YListLength`
: the number of elements in the Y-list.

`AccumulationCount`
: the number of accumulations co-added for each spectrum.

The following extended properties will be initialised from the measurement and calibration blocks if present.

`LaserPower`
: the laser power used for data collection as a percentage of full power.

`PinholeIn`
: a boolean indicating whether the pinhole was in the beampath.

`Lightpath`
: description of the lightpath used.

`AreaKey`
: cell array containing details of the AreaKey used for data collection (defines the system configuration).

`FocusMode`
: a WiREFocusMode value inddicating the focus mode used for data collection.

`ExposureTime`
: the exposure time in ms.

`XBinning`
: the X Binning used on the CCD (=1 for no binning.)

`YBinning`
: the Y Binning used on the CCD (=1 for no binning.)

`UseCosmicRayRemoval`
: a boolean indicating whether cosmic ray removal was used at data collection.

`UseResponseCalibration`
: a boolean indicating whether response calibration has been applied at data collection.

`XCentre`
: the centre pixel X value in the selected unit for a static scan.

`XFirst`
: the first X value in the selected unit for an extended scan or the first pixel X value for a static scan.

`XLast`
: the last X value in the selected unit for an extended scan or the last pixel X value for a static scan.

`LaserName`
: the name of the laser used for the data collection.

`GratingName`
: the name of the grating used for the data collection.

`DetectorName`
: the name of the detector used for the data collection.

`CCDArea`
: a cell array giving details of the CCD area used for data collection.

`GratingCalibration`
: a cell array giving values of the grating calibration parameters applicable for the selected laser and grating at the time of data collection.

`SlitData`
: a cell array giving details of the slit settings used for data collection.


### Reading in Spectral Data

To read in the spectral data, use the `GetSpectra` method as follows

```Matlab
Spectra = wdf.GetSpectra(1,wdf.Count());
```

If there is a large amount of data in the file, it can be read in a chunkwise fashion as follows

```Matlab
wdf.StartChunkwiseReading();
while (wdf.AreMoreChunks())
    z = wdf.GetNextDataChunk();
    % Process the data in the chunk z
end
```

This defaults to reading in 4096 spectra, unless the file contains less than this, in which case it reads the entire data file in.

Finally, if a single spectrum is wanted, the following can be used

```Matlab
Spectrum = wdf.GetSpectra(n,n);
```

Which returns the spectrum at position `n`.


### Reading in the Spectral Axis

To read in the spectral axis, use the following

```Matlab
xList = wdf.GetXList();
```


### Reading in Map Data

To read in all the map data within a file, use the following

```Matlab
[Maps, Label] = wdf.getMapData();
```

This reads in all data blocks which are of map type (generated in Map Review in WiRE). The array `Maps` has N rows, where N is the number of maps (of any type) in the wdf file, and has M columns, where M is the number of pixels.


### Reading in PCA Loadings

To read in the loadings generated from PCA, use the following

```Matlab
[Loadings VarianceExplained] = wdf.getLoadings(m);
```

The number `m` is the analysis number. If two PCA analyses were run, `m` would either be 1 or 2, depending on which was needed. The array `Loadings` has the loadings arranged in column-wise fashion. It has N rows, where N is the number of wavenumbers in the data, and M columns where M is the number of loadings calculated originally. `VarianceExplained` is a vector listing each of the percentage variance explained values. The first entry corresponds to the first principal component, the second to the second principal component, etc.

To get the scores matrix if the Spectra are loaded in, in full, the following may be used

```Matlab
Scores = Spectra*Loadings;
```


### Reading in Spatial Coordinates

To read in the x,y position at which each spectrum in a map was collected, use the following

```Matlab
[x, y] = wdf.GetOriginCoords(1,wdf.Count());
```

To read in the z position at which each spectrum in a map was collected, use the following

```Matlab
[z] = wdf.GetOriginZCoords(1,wdf.Count());
```


### Reading in Time Coordinates

To read in the timestamp for when every spectrum in the file was collected, use the following

```Matlab
Times = wdf.GetOriginTimes(1,wdf.Count());
```


### Reading in the Cosmic Ray or Saturated Spectrum Flags

To read in the flags for cosmic rays and saturated spectra flags, use the following

```Matlab
[Saturated CosmicRay] = wdf.GetOriginFlags(IndexStart,IndexEnd)
```
This will return two arrays. `Saturated` is a list of indices of spectra that have been flagged as suffering from saturation. `CosmicRay` is a list of indices of spectra that have had cosmic rays removed. `IndexStart` and `IndexEnd` define the range of spectra to consider, all spectral indices are 1-based.


### Reading in the Current Mask Flags

To read in the current mask flags, use the following

```Matlab
[Masked] = wdf.GetOriginFlagsBit(49,IndexStart,IndexEnd)
```
This returns an array listing the indices of spectra that are flagged as masked by the "current mask" (stored in flags bit 49), `IndexStart` and `IndexEnd` define the range of spectra to consider, all spectral indices are 1-based.


### Overwriting the Spectral Axis

To overwrite the spectral axis, use the following

```Matlab
wdf.WriteXList(XList);
```
Only permissable if the new XList is the same size as the list that it is replacing.


### Writing Spectral Data Back to File

To write spectrum intensity lists back into the file (after modifying them for example), use the following

```Matlab
wdf.WriteSpectra(IndexStart, Data);
```
Which writes the spectral data in `Data` back into the spectral data block in the wdf file, starting at the index `IndexStart`. Note that indices are 1-based and the spectra in Data must be the same length as those in the file and the number of spectra being written must fit within the bounds of the file spectrum data.

Alternatively in order to also replace the XList, spectrum intensities and optionally also the spectrum timestamps use

```Matlab
wdf.WriteSpectraToFile(XList, Spectra, Timestamps);
```
Here the spectrum length and the number of spectra supplied may be smaller than those in the file and, if so, will be padded with zeros. 


### Reading in Images and per-spectrum items from the File

To read in the principal white light image, use the following

```Matlab
[Image,XCoords,YCoords]=wdf.GetWLImage();
```

To read in a per-spectrum before or after collection image use

```Matlab
[Image,XCoords,YCoords]=wdf.GetPerSpectrumImage(SpectrumIndex, BeforeImage);
```

Where `Image` is a 3d array, of size (NumberOfPixelsInX * NumberOfPixelsInY * 3) representing an RGB image. The arrays `XCoords` and `YCoords` contain the x- and y-values for the pixel positions of the white light image.

To read in a named per-spectrum item from the file use

```Matlab
[Item]=GetPerSpectrumItem(SpectrumIndex, ItemName)
```


### Accessing additional data

Advanced methods can be used to find locations of data blocks and read property sets.

```Matlab
BlockLocations = wdf.GetBlockLocations(WdfBlockID.MEASUREMENT);
PropertySet = wdf.GetPropertySet(BlockLocations(1));
```
