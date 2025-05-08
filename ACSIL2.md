  ACSIL Reference Guide - Programming Concepts, Variables, Inputs, Subgraphs and Functions  body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; line-height: 1.6; color: #333; max-width: 1200px; margin: 0 auto; padding: 20px; } pre { background-color: #f6f8fa; border-radius: 5px; padding: 16px; overflow-x: auto; margin-bottom: 16px; border: 1px solid #e1e4e8; } code { font-family: SFMono-Regular, Consolas, "Liberation Mono", Menlo, monospace; font-size: 14px; } h1, h2, h3, h4 { font-weight: 600; margin-top: 24px; margin-bottom: 16px; } h1 { font-size: 2em; border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; } h2 { font-size: 1.5em; border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; } h3 { font-size: 1.25em; } h4 { font-size: 1em; } table { border-collapse: collapse; width: 100%; margin-bottom: 16px; } table, th, td { border: 1px solid #dfe2e5; } th, td { padding: 8px 16px; text-align: left; } th { background-color: #f6f8fa; } .function-signature { background-color: #f0f4f8; padding: 8px; border-left: 3px solid #3182ce; margin-bottom: 16px; } .note { background-color: #f8f9fa; border-left: 4px solid #4299e1; padding: 12px; margin-bottom: 16px; } .warning { background-color: #fff8f1; border-left: 4px solid #ed8936; padding: 12px; margin-bottom: 16px; } .example { margin-bottom: 24px; } .toc { background-color: #f8f9fa; border: 1px solid #eaecef; padding: 16px; border-radius: 5px; margin-bottom: 24px; } .toc ul { list-style-type: none; padding-left: 20px; } .back-to-top { position: fixed; bottom: 20px; right: 20px; background-color: #3182ce; color: white; padding: 10px 15px; border-radius: 5px; text-decoration: none; }

ACSIL Reference Guide
=====================

This document provides a comprehensive reference guide for the Advanced Custom Study Interface Library (ACSIL) in Sierra Chart, focusing on programming concepts, variables, inputs, subgraphs, functions, and graphics.

Table of Contents
-----------------

*   [1\. Introduction to ACSIL](#introduction)
*   [2\. Programming Concepts](#programming-concepts)
    *   [2.1 Study Structure](#study-structure)
    *   [2.2 Accessing Volume & Price Data](#volume-price-data)
    *   [2.3 Persistent Variables](#persistent-variables)
    *   [2.4 Memory Allocation](#memory-allocation)
*   [3\. Variables and Arrays](#variables-arrays)
    *   [3.1 Base Data Arrays](#base-data)
    *   [3.2 DateTime Arrays](#datetime-arrays)
    *   [3.3 Common Variables](#common-variables)
*   [4\. sc.Input Structure](#inputs)
    *   [4.1 Input Types](#input-types)
    *   [4.2 Setting Input Values](#setting-inputs)
    *   [4.3 Accessing Input Values](#accessing-inputs)
*   [5\. sc.Subgraph Structure](#subgraphs)
    *   [5.1 Subgraph Properties](#subgraph-properties)
    *   [5.2 Draw Styles](#draw-styles)
    *   [5.3 Custom Coloring](#custom-coloring)
*   [6\. ACSIL Functions](#functions)
    *   [6.1 Study Calculation Functions](#calculation-functions)
    *   [6.2 Trading Functions](#trading-functions)
    *   [6.3 Utility Functions](#utility-functions)
*   [7\. Graphics Functions](#graphics-functions)
    *   [7.1 Drawing Tools](#drawing-tools)
    *   [7.2 Custom Drawing](#custom-drawing)

1\. Introduction to ACSIL
-------------------------

ACSIL (Advanced Custom Study Interface Library) is Sierra Chart's programming interface for creating custom studies and trading systems. Based on C++, it provides extensive capabilities for technical analysis, chart visualization, and automated trading.

With ACSIL, you can create custom indicators, overlays, trading systems, and extend Sierra Chart's functionality through custom code. This documentation provides a comprehensive reference for working with ACSIL, focusing on the most up-to-date methods and functions.

2\. Programming Concepts
------------------------

### 2.1 Study Structure

Every ACSIL study follows a specific structure with two main sections: the initialization section and the calculation section.

#### Basic Study Structure

    #include "sierrachart.h"
    SCDLLName("My Custom Studies")
    
    SCSFExport scsf_MyCustomStudy(SCStudyInterfaceRef sc)
    {
        if (sc.SetDefaults)
        {
            // Initialization Section
            sc.GraphName = "My Custom Study";
            sc.StudyDescription = "This is my custom study";
            
            // Configure subgraphs
            sc.Subgraph[0].Name = "Main Line";
            sc.Subgraph[0].DrawStyle = DRAWSTYLE_LINE;
            sc.Subgraph[0].PrimaryColor = RGB(0, 255, 0);
            
            // Configure inputs
            sc.Input[0].Name = "Length";
            sc.Input[0].SetInt(14);
            
            sc.AutoLoop = 1;  // Enable automatic looping
            return;
        }
        
        // Calculation Section
        // This runs for each bar when AutoLoop = 1
        float InputValue = sc.Input[0].GetInt();
        
        // Example simple moving average calculation
        sc.SimpleMovAvg(sc.BaseDataIn[SC_LAST], sc.Subgraph[0], InputValue);
    }

**Note:** The code is split into two sections:

1.  The initialization section (when `sc.SetDefaults` is true) where you set up study properties, subgraphs, and inputs.
2.  The calculation section where actual data processing happens.

### 2.2 Accessing Volume & Price Data Per Bar

ACSIL provides several methods to access volume and price data for chart bars. This is one of the most important aspects when creating custom studies.

#### Basic Price Data Access

    // Access using BaseData arrays
    float HighPrice = sc.BaseData[SC_HIGH][sc.Index];
    float LowPrice = sc.BaseData[SC_LOW][sc.Index];
    float OpenPrice = sc.BaseData[SC_OPEN][sc.Index];
    float ClosePrice = sc.BaseData[SC_LAST][sc.Index];
    float Volume = sc.BaseData[SC_VOLUME][sc.Index];
    
    // Alternate syntax
    float HighPrice = sc.High[sc.Index];
    float LowPrice = sc.Low[sc.Index];
    float OpenPrice = sc.Open[sc.Index];
    float ClosePrice = sc.Close[sc.Index];

#### Volume at Price (Market Profile) Data

    // Access Volume at Price data 
    for (int BarIndex = sc.UpdateStartIndex; BarIndex < sc.ArraySize; BarIndex++) 
    {
        // Get number of prices with volume
        int NumberOfPricesWithVolume = sc.GetNumberOfPricesWithVolume(BarIndex);
    
        // Process each price level
        for (int PriceIndex = 0; PriceIndex < NumberOfPricesWithVolume; PriceIndex++)
        {
            // Get price and volume data at this level
            float PriceValue = 0.0f;
            float VolumeValue = 0.0f;
            int BidVolume = 0;
            int AskVolume = 0;
    
            sc.GetVolumeAtPriceForBar(BarIndex, PriceIndex, PriceValue, 
                                     VolumeValue, BidVolume, AskVolume);
            
            // Use the data values as needed
            // ...
        }
    }

#### Working with Bid/Ask Volume

    // Calculating total bid and ask volume for current bar
    int TotalBidVolume = 0;
    int TotalAskVolume = 0;
    int NumberOfPrices = sc.GetNumberOfPricesWithVolume(sc.Index);
    
    for (int PriceIndex = 0; PriceIndex < NumberOfPrices; PriceIndex++)
    {
        float Price = 0.0f;
        float Volume = 0.0f;
        int BidVol = 0;
        int AskVol = 0;
        
        sc.GetVolumeAtPriceForBar(sc.Index, PriceIndex, Price, Volume, BidVol, AskVol);
        
        TotalBidVolume += BidVol;
        TotalAskVolume += AskVol;
    }
    
    // Store results
    sc.Subgraph[0][sc.Index] = TotalBidVolume;
    sc.Subgraph[1][sc.Index] = TotalAskVolume;

#### TPO (Time Price Opportunity) Data

    // Access TPO count at price levels
    for (int PriceIndex = 0; PriceIndex < sc.GetNumberOfPricesWithTPO(sc.Index); PriceIndex++)
    {
        float Price = 0.0f;
        int TPOCount = 0;
        
        sc.GetTPOAtPriceForBar(sc.Index, PriceIndex, Price, TPOCount);
        
        // Use Price and TPOCount values
    }

### 2.3 Persistent Variables

Persistent variables allow you to store data between function calls and instances, which is crucial for tracking state or progressive calculations.

**Key Functions:**

`int sc.GetPersistentInt(int Key);   void sc.SetPersistentInt(int Key, int Value);   float sc.GetPersistentFloat(int Key);   void sc.SetPersistentFloat(int Key, float Value);   void* sc.GetPersistentPointer(int Key);   void sc.SetPersistentPointer(int Key, void* Pointer);`

#### Example: Using Persistent Variables

    // Track the highest price seen
    float& HighestPrice = sc.GetPersistentFloat(1);
    
    // First-time initialization
    if (sc.Index == 0)
        HighestPrice = sc.BaseData[SC_HIGH][0];
    
    // Update the highest price if needed
    if (sc.BaseData[SC_HIGH][sc.Index] > HighestPrice)
        HighestPrice = sc.BaseData[SC_HIGH][sc.Index];
    
    // Store the percentage from highest price
    sc.Subgraph[0][sc.Index] = 100.0f * (sc.BaseData[SC_LAST][sc.Index] / HighestPrice - 1.0f);

#### Using Persistent Pointers for Structures

    // Using persistent pointer for custom structure
    struct MyData {
        int Count;
        float SumValues;
        float LastValue;
    };
    
    SCSFExport scsf_CustomStructureExample(SCStudyInterfaceRef sc)
    {
        if (sc.SetDefaults) {
            sc.GraphName = "Persistent Structure Example";
            sc.AutoLoop = 1;
            return;
        }
        
        // Get our persistent structure
        MyData* Data = (MyData*)sc.GetPersistentPointer(1);
        
        // Allocate if not already done
        if (Data == NULL) {
            Data = (MyData*)sc.AllocateMemory(sizeof(MyData));
            sc.SetPersistentPointer(1, Data);
            
            // Initialize
            Data->Count = 0;
            Data->SumValues = 0.0f;
            Data->LastValue = 0.0f;
        }
        
        // Free memory during last call
        if (sc.LastCallToFunction) {
            sc.FreeMemory(Data);
            return;
        }
        
        // Update our structure
        Data->Count++;
        Data->SumValues += sc.Close[sc.Index];
        Data->LastValue = sc.Close[sc.Index];
        
        // Use the data
        if (Data->Count > 0) {
            sc.Subgraph[0][sc.Index] = Data->SumValues / Data->Count;
        }
    }

### 2.4 Memory Allocation

ACSIL provides functions for memory allocation and deallocation which should be used when working with dynamic memory.

**Memory Management Functions:**

`void* sc.AllocateMemory(int Size);   void sc.FreeMemory(void* Pointer);`

#### Example: Memory Allocation

    // Allocate memory for an array of 100 floats
    float* MyArray = (float*)sc.AllocateMemory(100 * sizeof(float));
    
    // Use the memory
    if (MyArray != NULL) {
        for (int i = 0; i < 100; i++) {
            MyArray[i] = i * 2.5f;
        }
    }
    
    // Free memory when done (usually on sc.LastCallToFunction)
    if (sc.LastCallToFunction) {
        sc.FreeMemory(MyArray);
    }

**Warning:** Always free allocated memory when you're done with it or when the study is removed from the chart (`sc.LastCallToFunction == true`) to prevent memory leaks.

3\. Variables and Arrays
------------------------

### 3.1 Base Data Arrays

ACSIL provides several arrays for accessing price and volume data for the chart.

Array

Description

Constants

`sc.BaseData` / `sc.BaseDataIn`

Primary chart data arrays

SC\_OPEN, SC\_HIGH, SC\_LOW, SC\_LAST, SC\_VOLUME

`sc.Open`, `sc.High`, `sc.Low`, `sc.Close`

Direct access to price arrays

N/A

`sc.Volume`

Direct access to volume array

N/A

`sc.OpenInterest`

Open Interest data

N/A

`sc.NumberOfTrades`

Number of trades in each bar

N/A

`sc.AskVolume`, `sc.BidVolume`

Ask and Bid volume data

N/A

#### Example: Accessing Base Data Arrays

    // Different ways to access price data
    float Open = sc.BaseData[SC_OPEN][sc.Index];
    float High = sc.High[sc.Index];
    float Low = sc.Low[sc.Index];
    float Close = sc.Close[sc.Index];
    float Volume = sc.Volume[sc.Index];
    
    // Calculate a simple indicator (close - open)
    sc.Subgraph[0][sc.Index] = Close - Open;
    
    // Calculate a volume weighted indicator
    if (Volume > 0) {
        sc.Subgraph[1][sc.Index] = (Close * Volume) / Volume;
    }

#### Additional Data Arrays

    // Access additional data arrays
    float UpTickVolume = sc.UpTickVolume[sc.Index];
    float DownTickVolume = sc.DownTickVolume[sc.Index];
    float BidVolume = sc.BidVolume[sc.Index];
    float AskVolume = sc.AskVolume[sc.Index];
    float OpenInterest = sc.OpenInterest[sc.Index];
    
    // Calculate Buy/Sell pressure ratio
    if (DownTickVolume > 0)
        sc.Subgraph[0][sc.Index] = UpTickVolume / DownTickVolume;
    else
        sc.Subgraph[0][sc.Index] = UpTickVolume;

### 3.2 DateTime Arrays

ACSIL provides arrays and functions to work with date and time values.

**DateTime Arrays:**

`SCDateTimeArray sc.BaseDateTimeIn   SCDateTimeArray sc.DateTimeOut`

#### Example: Working with DateTimes

    // Access DateTime for current bar
    SCDateTime CurrentBarDateTime = sc.BaseDateTimeIn[sc.Index];
    
    // Extract date components
    int Year, Month, Day;
    sc.GetDateYMD(CurrentBarDateTime, Year, Month, Day);
    
    // Extract time components
    int Hour, Minute, Second, Millisecond;
    sc.GetTimeHMS(CurrentBarDateTime, Hour, Minute, Second, Millisecond);
    
    // Example: Check if we're in a specific time range
    if (Hour >= 9 && Hour < 16) {
        // Market hours logic
        sc.Subgraph[0][sc.Index] = sc.Close[sc.Index];
    } else {
        // After hours logic
        sc.Subgraph[0][sc.Index] = 0;
    }
    
    // Create a new SCDateTime
    SCDateTime SpecificDateTime;
    sc.SetDateTime(SpecificDateTime, 2023, 6, 15, 14, 30, 0);
    
    // Compare DateTimes
    if (CurrentBarDateTime >= SpecificDateTime) {
        // Handle dates after specific date
    }

### 3.3 Common Variables

ACSIL provides several important variables for study functions.

Variable

Description

`sc.Index`

The current bar index being processed (when using AutoLoop)

`sc.ArraySize`

Total number of bars in the chart

`sc.UpdateStartIndex`

Starting index for update (important when using manual looping)

`sc.ChartNumber`

The chart number this study is applied to

`sc.StudyGraphInstanceID`

Unique ID for this study instance

`sc.SetDefaults`

True when function is called to set defaults

`sc.LastCallToFunction`

True when the study is being removed from the chart

`sc.AutoLoop`

Controls whether ACSIL auto-loops through all bars

#### Example: Using Common Variables

    // Using common variables
    if (sc.SetDefaults) {
        sc.GraphName = "Common Variables Example";
        sc.AutoLoop = 1;
        return;
    }
    
    // If this is the last call to the function (study being removed)
    if (sc.LastCallToFunction) {
        // Clean up resources
        return;
    }
    
    // Store the chart number
    sc.Subgraph[0][sc.Index] = (float)sc.ChartNumber;
    
    // Store bar count
    sc.Subgraph[1][sc.Index] = (float)sc.ArraySize;
    
    // Store percentage position in chart (0-100%)
    if (sc.ArraySize > 1)
        sc.Subgraph[2][sc.Index] = 100.0f * sc.Index / (sc.ArraySize - 1);
    else
        sc.Subgraph[2][sc.Index] = 0;

#### Important State Variables

    // Check for full recalculation
    if (sc.IsFullRecalculation) {
        // Initialize calculations from scratch
    }
    
    // Check if chart has downloaded all historical data
    if (!sc.ChartIsDownloadingHistoricalData) {
        // Safe to perform calculations requiring complete history
    }
    
    // Check if index is valid for accessing previous bar
    if (sc.Index > 0) {
        // Calculate bar-to-bar changes
        sc.Subgraph[0][sc.Index] = sc.Close[sc.Index] - sc.Close[sc.Index-1];
    }

4\. sc.Input Structure
----------------------

### 4.1 Input Types

ACSIL supports various input types that allow users to configure your study.

Input Type

Description

Setup Method

Integer

Whole number values

`sc.Input[n].SetInt(DefaultValue)`

Float

Decimal values

`sc.Input[n].SetFloat(DefaultValue)`

Yes/No

Boolean values

`sc.Input[n].SetYesNo(DefaultValue)`

Color

Color selection

`sc.Input[n].SetColor(DefaultValue)`

Date-Time

Date and Time values

`sc.Input[n].SetDateTime(DefaultValue)`

String

Text values

`sc.Input[n].SetString(DefaultString)`

Custom

Custom selections

`sc.Input[n].SetCustomInputIndex(DefaultIndex)`

### 4.2 Setting Input Values

Configure inputs in the `sc.SetDefaults` section of your study.

#### Example: Setting Different Input Types

    if (sc.SetDefaults)
    {
        // Integer input
        sc.Input[0].Name = "Period";
        sc.Input[0].SetInt(14);
        sc.Input[0].SetIntLimits(1, 200);
        
        // Float input
        sc.Input[1].Name = "Multiplier";
        sc.Input[1].SetFloat(2.0f);
        sc.Input[1].SetFloatLimits(0.1f, 10.0f);
        
        // Yes/No input
        sc.Input[2].Name = "Show Markers";
        sc.Input[2].SetYesNo(1);  // 1 = Yes, 0 = No
        
        // Color input
        sc.Input[3].Name = "Custom Color";
        sc.Input[3].SetColor(RGB(255, 0, 0));  // Red
        
        // Date-Time input
        sc.Input[4].Name = "Start Date";
        SCDateTime DefaultDate;
        sc.SetDateTime(DefaultDate, 2023, 1, 1, 0, 0, 0);
        sc.Input[4].SetDateTime(DefaultDate);
        
        // String input
        sc.Input[5].Name = "Custom Text";
        sc.Input[5].SetString("Default text");
        
        // Custom input (dropdown)
        sc.Input[6].Name = "Moving Average Type";
        sc.Input[6].SetCustomInputIndex(0);
        sc.Input[6].SetCustomInputStrings("Simple;Exponential;Weighted;Hull;VWAP");
    }

### 4.3 Accessing Input Values

Access input values in the calculation section of your study.

#### Example: Accessing Input Values

    // Get input values and use them
    int Period = sc.Input[0].GetInt();
    float Multiplier = sc.Input[1].GetFloat();
    bool ShowMarkers = sc.Input[2].GetYesNo();
    COLORREF CustomColor = sc.Input[3].GetColor();
    SCDateTime StartDate = sc.Input[4].GetDateTime();
    SCString CustomText = sc.Input[5].GetString();
    int MAType = sc.Input[6].GetIndex();
    
    // Use the values in calculations
    if (sc.BaseDateTimeIn[sc.Index] >= StartDate) {
        // Apply indicator calculations based on selected MA type
        switch (MAType) {
            case 0:  // Simple MA
                sc.SimpleMovAvg(sc.Close, sc.Subgraph[0], Period);
                break;
            case 1:  // Exponential MA
                sc.ExponentialMovAvg(sc.Close, sc.Subgraph[0], Period);
                break;
            case 2:  // Weighted MA
                sc.WeightedMovAvg(sc.Close, sc.Subgraph[0], Period);
                break;
            case 3:  // Hull MA
                sc.HullMovingAverage(sc.Close, sc.Subgraph[0], Period);
                break;
            case 4:  // VWAP
                if (sc.Volume[sc.Index] > 0) {
                    // Calculate VWAP
                    // (simplified for example)
                    sc.Subgraph[0][sc.Index] = sc.Close[sc.Index];
                }
                break;
        }
        
        // Apply multiplier if needed
        sc.Subgraph[0][sc.Index] *= Multiplier;
        
        // Add markers if enabled
        if (ShowMarkers) {
            sc.Subgraph[1][sc.Index] = sc.Subgraph[0][sc.Index];
            sc.Subgraph[1].DataColor[sc.Index] = CustomColor;
        }
    }

5\. sc.Subgraph Structure
-------------------------

### 5.1 Subgraph Properties

Subgraphs are the primary way to output data in ACSIL. Each subgraph represents a data series that can be displayed on the chart.

Property

Description

`sc.Subgraph[n].Name`

Name displayed in the legend

`sc.Subgraph[n].DrawStyle`

How the subgraph is displayed (line, histogram, etc.)

`sc.Subgraph[n].PrimaryColor`

Main color for the subgraph

`sc.Subgraph[n].SecondaryColor`

Secondary color (e.g., for fills)

`sc.Subgraph[n].LineWidth`

Width of lines in pixels

`sc.Subgraph[n].LineStyle`

Style of line (solid, dotted, etc.)

`sc.Subgraph[n].Data`

Array containing the actual data values

`sc.Subgraph[n].DataColor`

Array for per-bar coloring

#### Example: Setting Subgraph Properties

    if (sc.SetDefaults)
    {
        // Configure the main subgraph as a line
        sc.Subgraph[0].Name = "Main Line";
        sc.Subgraph[0].DrawStyle = DRAWSTYLE_LINE;
        sc.Subgraph[0].PrimaryColor = RGB(0, 255, 0);  // Green
        sc.Subgraph[0].LineWidth = 2;
        sc.Subgraph[0].LineStyle = LINESTYLE_SOLID;
        
        // Configure second subgraph as histogram
        sc.Subgraph[1].Name = "Histogram";
        sc.Subgraph[1].DrawStyle = DRAWSTYLE_HISTOGRAM;
        sc.Subgraph[1].PrimaryColor = RGB(0, 0, 255);  // Blue
        sc.Subgraph[1].SecondaryColor = RGB(255, 0, 0);  // Red for negative values
        sc.Subgraph[1].LineWidth = 3;
        
        // Configure third subgraph as markers
        sc.Subgraph[2].Name = "Markers";
        sc.Subgraph[2].DrawStyle = DRAWSTYLE_MARKER;
        sc.Subgraph[2].PrimaryColor = RGB(255, 0, 0);  // Red
        sc.Subgraph[2].LineWidth = 2;  // Size of markers
    }

### 5.2 Draw Styles

ACSIL supports various draw styles for displaying data on the chart.

Draw Style

Description

`DRAWSTYLE_LINE`

Continuous line connecting points

`DRAWSTYLE_DASH`

Dashed line connecting points

`DRAWSTYLE_DOT`

Dotted line connecting points

`DRAWSTYLE_DASHDOT`

Line with dash dot pattern

`DRAWSTYLE_DASHDOTDOT`

Line with dash dot dot pattern

`DRAWSTYLE_HISTOGRAM`

Vertical bars from zero line to data point

`DRAWSTYLE_POINT`

Small points at data points

`DRAWSTYLE_MARKER`

Symbols at data points

`DRAWSTYLE_IGNORE`

Data is calculated but not displayed

#### Example: Using Different Draw Styles

    if (sc.SetDefaults)
    {
        // Line style
        sc.Subgraph[0].Name = "Line";
        sc.Subgraph[0].DrawStyle = DRAWSTYLE_LINE;
        sc.Subgraph[0].PrimaryColor = RGB(0, 128, 0);
        
        // Dashed line
        sc.Subgraph[1].Name = "Dashed";
        sc.Subgraph[1].DrawStyle = DRAWSTYLE_DASH;
        sc.Subgraph[1].PrimaryColor = RGB(128, 0, 0);
        
        // Histogram
        sc.Subgraph[2].Name = "Histogram";
        sc.Subgraph[2].DrawStyle = DRAWSTYLE_HISTOGRAM;
        sc.Subgraph[2].PrimaryColor = RGB(0, 0, 255);
        sc.Subgraph[2].SecondaryColor = RGB(255, 0, 0);
        
        // Marker
        sc.Subgraph[3].Name = "Markers";
        sc.Subgraph[3].DrawStyle = DRAWSTYLE_MARKER;
        sc.Subgraph[3].PrimaryColor = RGB(255, 128, 0);
        
        return;
    }
    
    // Generate some values
    sc.Subgraph[0][sc.Index] = sc.Close[sc.Index];
    sc.Subgraph[1][sc.Index] = sc.Close[sc.Index] * 0.98f;
    sc.Subgraph[2][sc.Index] = sc.Close[sc.Index] - sc.Open[sc.Index];
        
    // Add markers at key points
    if (sc.Index > 0 && sc.Close[sc.Index] > sc.Close[sc.Index - 1]) {
        sc.Subgraph[3][sc.Index] = sc.High[sc.Index];
    } else {
        sc.Subgraph[3][sc.Index] = sc.Subgraph[3].NoValue;
    }

### 5.3 Custom Coloring

You can customize the color of each bar or point in a subgraph using the DataColor array.

#### Example: Custom Bar Coloring

    // Calculate and display a moving average
    sc.SimpleMovAvg(sc.Close, sc.Subgraph[0], 20);
    
    // Color points based on relationship to close price
    for (int BarIndex = sc.UpdateStartIndex; BarIndex < sc.ArraySize; BarIndex++) {
        // Default color
        sc.Subgraph[0].DataColor[BarIndex] = sc.Subgraph[0].PrimaryColor;
        
        // Above close = green, below close = red
        if (sc.Subgraph[0][BarIndex] > sc.Close[BarIndex]) {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(0, 255, 0);  // Green
        } else if (sc.Subgraph[0][BarIndex] < sc.Close[BarIndex]) {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(255, 0, 0);  // Red
        }
    }

#### Color Intensity Based on Values

    // Color based on intensity
    for (int BarIndex = sc.UpdateStartIndex; BarIndex < sc.ArraySize; BarIndex++) {
        // Find a value between 0 and 100 for the indicator
        float NormalizedValue = sc.Subgraph[0][BarIndex];
        if (NormalizedValue > 100.0f) NormalizedValue = 100.0f;
        if (NormalizedValue < 0.0f) NormalizedValue = 0.0f;
        
        // Convert to a color intensity (0-255)
        int Intensity = (int)(NormalizedValue * 2.55f);
        
        // Create RGB color (Green to Red gradient)
        sc.Subgraph[0].DataColor[BarIndex] = RGB(Intensity, 255 - Intensity, 0);
    }

#### Multiple Color Levels

    // Set different colors based on thresholds
    for (int BarIndex = sc.UpdateStartIndex; BarIndex < sc.ArraySize; BarIndex++) {
        float Value = sc.Subgraph[0][BarIndex];
        
        // Set color based on value range
        if (Value > 80.0f) {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(255, 0, 0);  // Red - overbought
        }
        else if (Value > 60.0f) {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(255, 128, 0);  // Orange
        }
        else if (Value > 40.0f) {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(0, 0, 255);  // Blue - neutral
        }
        else if (Value > 20.0f) {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(0, 128, 255);  // Light blue
        }
        else {
            sc.Subgraph[0].DataColor[BarIndex] = RGB(0, 255, 0);  // Green - oversold
        }
    }

6\. ACSIL Functions
-------------------

### 6.1 Study Calculation Functions

ACSIL provides many built-in functions for technical analysis calculations.

#### Moving Averages

**Key Functions:**

`sc.SimpleMovAvg(Input, Output, Length);   sc.ExponentialMovAvg(Input, Output, Length);   sc.WeightedMovAvg(Input, Output, Length);   sc.HullMovingAverage(Input, Output, Length);   sc.TriangularMovAvg(Input, Output, Length);   sc.ArnaudLegouxMovingAverage(Input, Output, Length, Sigma, Offset);`

#### Example: Using Moving Averages

    // Simple Moving Average
    sc.SimpleMovAvg(sc.Close, sc.Subgraph[0], 20);
    
    // Exponential Moving Average
    sc.ExponentialMovAvg(sc.Close, sc.Subgraph[1], 20);
    
    // Weighted Moving Average
    sc.WeightedMovAvg(sc.Close, sc.Subgraph[2], 20);
    
    // Hull Moving Average
    sc.HullMovingAverage(sc.Close, sc.Subgraph[3], 20);
    
    // Arnaud Legoux Moving Average
    sc.ArnaudLegouxMovingAverage(sc.Close, sc.Subgraph[4], 20, 6.0f, 0.5f);

#### Oscillators and Momentum Indicators

**Key Functions:**

`sc.RSI(Input, Output, Length);   sc.Stochastic(BaseDataIn, SubgraphOut, KPeriod, KSlowingPeriod, DPeriod);   sc.MACD(Input, Output, FastLength, SlowLength, MACDLength);   sc.CCI(BaseDataIn, Output, Length);`

#### Example: Oscillators and Momentum

    // RSI calculation
    sc.RSI(sc.Close, sc.Subgraph[0], 14);
    
    // Stochastic calculation
    sc.Stochastic(sc.BaseDataIn, sc.Subgraph[1], 14, 3, 3);
    
    // MACD calculation
    sc.MACD(sc.Close, sc.Subgraph[2], 12, 26, 9);
    
    // CCI calculation
    sc.CCI(sc.BaseDataIn, sc.Subgraph[3], 20);

#### Bands and Channels

**Key Functions:**

`sc.BollingerBands(Input, Output, Length, StandardDeviations, MovAvgType);   sc.KeltnerChannel(BaseDataIn, Output, MALength, ATRLength, ATRMultiplier);   sc.DonchianChannel(BaseDataIn, Output, Length);`

#### Example: Bands and Channels

    // Bollinger Bands
    sc.BollingerBands(sc.Close, sc.Subgraph[0], 20, 2.0f, MOVAVGTYPE_SIMPLE);
    
    // Keltner Channel
    sc.KeltnerChannel(sc.BaseDataIn, sc.Subgraph[1], 20, 10, 2.0f);
    
    // Donchian Channel
    sc.DonchianChannel(sc.BaseDataIn, sc.Subgraph[2], 20);

### 6.2 Trading Functions

ACSIL provides functions for automated trading through your custom studies.

#### Order Submission

**Key Functions:**

`int sc.SubmitOCOOrder(s_SCNewOrder& NewOrder);   int sc.ModifyOrder(s_SCNewOrder& OrderModifications);   void sc.CancelOrder(int OrderID);   void sc.CancelAllOrders();`

#### Example: Submitting an Order

    // Create a simple trading signal
    bool BuySignal = false;
    bool SellSignal = false;
    
    // Simple crossover logic for example
    if (sc.Index > 1) {  // Need at least 2 bars of data
        // Fast MA crosses above slow MA
        if (sc.Subgraph[0][sc.Index] > sc.Subgraph[1][sc.Index] && 
            sc.Subgraph[0][sc.Index-1] <= sc.Subgraph[1][sc.Index-1]) {
            BuySignal = true;
        }
        
        // Fast MA crosses below slow MA
        if (sc.Subgraph[0][sc.Index] < sc.Subgraph[1][sc.Index] && 
            sc.Subgraph[0][sc.Index-1] >= sc.Subgraph[1][sc.Index-1]) {
            SellSignal = true;
        }
    }
    
    // Submit order on buy signal
    if (BuySignal) {
        s_SCNewOrder NewOrder;
        NewOrder.OrderType = SCT_ORDERTYPE_MARKET;
        NewOrder.OrderQuantity = 1;  // Buy 1 contract/share
        NewOrder.OrderAction = SCT_BUY;  // Buy
        int Result = sc.SubmitOCOOrder(NewOrder);
        
        // Check result
        if (Result > 0) {
            // Order submitted successfully, Result is the OrderID
            sc.AddMessageToLog("Buy order submitted. OrderID: " + SCString(Result), 1);
        }
    }
    
    // Submit order on sell signal
    if (SellSignal) {
        s_SCNewOrder NewOrder;
        NewOrder.OrderType = SCT_ORDERTYPE_MARKET;
        NewOrder.OrderQuantity = 1;  // Sell 1 contract/share
        NewOrder.OrderAction = SCT_SELL;  // Sell
        int Result = sc.SubmitOCOOrder(NewOrder);
        
        // Check result
        if (Result > 0) {
            // Order submitted successfully
            sc.AddMessageToLog("Sell order submitted. OrderID: " + SCString(Result), 1);
        }
    }

#### Position Management

**Key Functions:**

`void sc.GetTradePosition(s_SCPositionData& PositionData);   void sc.FlattenPosition();   void sc.FlattenAndCancelAllOrders();`

#### Example: Managing Positions

    // Get current position
    s_SCPositionData PositionData;
    sc.GetTradePosition(PositionData);
    
    // Display position information
    sc.Subgraph[0][sc.Index] = PositionData.PositionQuantity;
    sc.Subgraph[1][sc.Index] = PositionData.AveragePrice;
    sc.Subgraph[2][sc.Index] = PositionData.OpenProfitLoss;
    
    // Example: Close position if profit target reached
    if (PositionData.PositionQuantity > 0 && PositionData.OpenProfitLoss > 500.0f) {
        sc.FlattenPosition();
        sc.AddMessageToLog("Position closed at profit target", 1);
    }
    
    // Example: Close position if stop loss hit
    if (PositionData.PositionQuantity > 0 && PositionData.OpenProfitLoss < -300.0f) {
        sc.FlattenPosition();
        sc.AddMessageToLog("Position closed at stop loss", 1);
    }

#### Order Tracking

**Key Functions:**

`int sc.GetOrderByOrderID(int OrderID, s_SCTradeOrder& TradeOrder);   int sc.GetOrderByIndex(int Index, s_SCTradeOrder& TradeOrder);   int sc.GetOrderFillArraySize();`

#### Example: Tracking Orders

    // Track a specific order
    int OrderID = 123456;  // OrderID from previous submission
    s_SCTradeOrder TradeOrder;
    
    if (sc.GetOrderByOrderID(OrderID, TradeOrder)) {
        // Order was found, check its status
        if (TradeOrder.OrderStatusCode == SCT_OSC_FILLED) {
            sc.AddMessageToLog("Order was filled at price: " + 
                              SCString(TradeOrder.FilledPrice), 1);
        } 
        else if (TradeOrder.OrderStatusCode == SCT_OSC_WORKING) {
            sc.AddMessageToLog("Order is still working", 0);
        } 
        else if (TradeOrder.OrderStatusCode == SCT_OSC_CANCELED) {
            sc.AddMessageToLog("Order was canceled", 0);
        }
    }

### 6.3 Utility Functions

ACSIL provides various utility functions to help with common tasks.

**Key Utility Functions:**

`void sc.AddMessageToLog(SCString Message, int ShowLog = 0);   void sc.AddAlertLine(SCString Message, int ShowAlertLog = 0);   SCString sc.FormatString(float Value, int NumOfDecimalPlaces);   int sc.GetStudyIDByName(int ChartNumber, const SCString& StudyName);`

#### Example: Using Utility Functions

    // Add a message to the log
    sc.AddMessageToLog("Processing calculation", 0);
    
    // Format a float value with 2 decimal places
    float Value = 123.45678f;
    SCString FormattedValue = sc.FormatString(Value, 2);  // "123.46"
    
    // Add an alert
    if (sc.CrossOver(sc.Index, sc.Subgraph[0], sc.Subgraph[1])) {
        SCString AlertMsg = "Crossover detected at price: " + FormattedValue;
        sc.AddAlertLine(AlertMsg, 1);  // 1 = show alert log
    }
    
    // Get a study ID
    int StudyID = sc.GetStudyIDByName(sc.ChartNumber, "Moving Average");
    
    // Access another study's data
    if (StudyID != 0) {
        SCFloatArray StudyData;
        sc.GetStudyArrayUsingID(StudyID, 0, StudyData);
        
        // Use StudyData array
        if (sc.Index < StudyData.GetArraySize()) {
            float OtherStudyValue = StudyData[sc.Index];
            sc.Subgraph[3][sc.Index] = OtherStudyValue;
        }
    }

#### Date and Time Functions

**Key Date/Time Functions:**

`void sc.GetDateYMD(const SCDateTime& DateTime, int& Year, int& Month, int& Day);   void sc.GetTimeHMS(const SCDateTime& DateTime, int& Hour, int& Minute, int& Second, int& Millisecond);`

#### Example: Working with Date/Time

    // Get current bar date/time
    SCDateTime CurrentDateTime = sc.BaseDateTimeIn[sc.Index];
    
    // Extract date components
    int Year, Month, Day;
    sc.GetDateYMD(CurrentDateTime, Year, Month, Day);
    
    // Extract time components
    int Hour, Minute, Second, Millisecond;
    sc.GetTimeHMS(CurrentDateTime, Hour, Minute, Second, Millisecond);
    
    // Check if we're in regular trading hours
    bool RegularHours = (Hour >= 9 && Hour < 16);  // 9:00 AM - 4:00 PM
    
    // Use time information in the study
    if (RegularHours) {
        // Regular hours calculation
        sc.Subgraph[0][sc.Index] = sc.Close[sc.Index];
    } else {
        // After hours calculation
        sc.Subgraph[0][sc.Index] = sc.Subgraph[0].NoValue;  // Don't display
    }

7\. Graphics Functions
----------------------

### 7.1 Drawing Tools

ACSIL provides the ability to add chart drawings programmatically using the `sc.UseTool()` function.

**Key Function:**

`void sc.UseTool(s_UseTool& Tool);`

#### Example: Drawing a Horizontal Line

    // Draw a horizontal line at a specific price level
    s_UseTool Tool;
    Tool.Clear();  // Always clear the tool first
    Tool.ChartNumber = sc.ChartNumber;
    Tool.DrawingType = DRAWING_HORIZONTALLINE;
    Tool.LineNumber = 1;  // Unique identifier
    Tool.BeginValue = 100.0f;  // Price level
    Tool.Color = RGB(255, 0, 0);  // Red
    Tool.LineWidth = 2;
    Tool.LineStyle = LINESTYLE_SOLID;
    Tool.AddMethod = UTAM_ADD_OR_ADJUST;  // Replace existing with same LineNumber
    sc.UseTool(Tool);

#### Drawing a Text Label

    // Add text to the chart
    s_UseTool TextTool;
    TextTool.Clear();
    TextTool.ChartNumber = sc.ChartNumber;
    TextTool.DrawingType = DRAWING_TEXT;
    TextTool.LineNumber = 2;  // Unique identifier
    TextTool.BeginIndex = sc.Index;  // X position (bar index)
    TextTool.BeginValue = sc.High[sc.Index] + 2.0f;  // Y position (price + offset)
    TextTool.Text = "Important point!";
    TextTool.Color = RGB(0, 0, 255);  // Blue
    TextTool.FontSize = 14;
    TextTool.FontBold = 1;
    TextTool.AddMethod = UTAM_ADD_OR_ADJUST;
    sc.UseTool(TextTool);

#### Drawing a Rectangle

    // Draw a rectangle highlighting an area
    s_UseTool RectTool;
    RectTool.Clear();
    RectTool.ChartNumber = sc.ChartNumber;
    RectTool.DrawingType = DRAWING_RECTANGLEHIGHLIGHT;
    RectTool.LineNumber = 3;  // Unique identifier
    RectTool.BeginIndex = sc.Index - 5;  // Left side
    RectTool.EndIndex = sc.Index;  // Right side
    RectTool.BeginValue = sc.Low[sc.Index];  // Bottom
    RectTool.EndValue = sc.High[sc.Index];  // Top
    RectTool.Color = RGB(255, 128, 0);  // Orange outline
    RectTool.SecondaryColor = RGB(255, 255, 0);  // Yellow fill
    RectTool.LineWidth = 2;
    RectTool.TransparencyLevel = 70;  // 0-100, higher is more transparent
    RectTool.AddMethod = UTAM_ADD_OR_ADJUST;
    sc.UseTool(RectTool);

#### Drawing a Trend Line

    // Draw a trend line
    s_UseTool LineTool;
    LineTool.Clear();
    LineTool.ChartNumber = sc.ChartNumber;
    LineTool.DrawingType = DRAWING_LINE;
    LineTool.LineNumber = 4;  // Unique identifier
    LineTool.BeginIndex = sc.Index - 10;  // Start point (10 bars back)
    LineTool.EndIndex = sc.Index;  // End point (current bar)
    LineTool.BeginValue = sc.Low[sc.Index - 10];  // Y value at start
    LineTool.EndValue = sc.Close[sc.Index];  // Y value at end
    LineTool.Color = RGB(0, 255, 0);  // Green
    LineTool.LineWidth = 2;
    LineTool.LineStyle = LINESTYLE_DASH;
    LineTool.ExtendLeft = 0;  // Don't extend left
    LineTool.ExtendRight = 1;  // Extend right
    LineTool.AddMethod = UTAM_ADD_OR_ADJUST;
    sc.UseTool(LineTool);

### 7.2 Custom Drawing

ACSIL provides functions for custom drawing using GDI (Graphics Device Interface).

**Key Functions:**

`HDC sc.GetBarGraphGDIHandle();   int sc.GetGraphRegion(RECT& GraphRegion);   int sc.CoordinateToScreenPoint(float Value, int BarIndex, POINT& ScreenPoint);`

#### Example: Custom Drawing with GDI

    // Custom Drawing in Study's Region
    if (sc.IsUserAllowedForSCDLLName == false)
        return;
    
    // Only draw during final drawing passes
    if (sc.LastCallToFunction || !sc.ChartAllowsGDIDrawing || sc.IsFullRecalculation)
        return;
    
    // Get GDI handle and drawing region
    HDC GDIHandle = sc.GetBarGraphGDIHandle();
    RECT GraphRegion;
    sc.GetGraphRegion(GraphRegion);
    
    if (GDIHandle == NULL)
        return;
    
    // Choose a font and set colors
    HFONT Font = CreateFont(14, 0, 0, 0, FW_BOLD, 0, 0, 0, 0, 0, 0, 0, 0, "Arial");
    HFONT OldFont = (HFONT)SelectObject(GDIHandle, Font);
    SetTextColor(GDIHandle, RGB(0, 0, 255));  // Blue text
    SetBkMode(GDIHandle, TRANSPARENT);  // Transparent background
    
    // Calculate screen position
    POINT ScreenPoint;
    sc.CoordinateToScreenPoint(sc.High[sc.ArraySize-1] + 3.0f, sc.ArraySize-1, ScreenPoint);
    
    // Draw text
    TextOut(GDIHandle, ScreenPoint.x, ScreenPoint.y, "Custom GDI Text", 15);
    
    // Create and use a pen
    HPEN Pen = CreatePen(PS_SOLID, 2, RGB(255, 0, 0));
    HPEN OldPen = (HPEN)SelectObject(GDIHandle, Pen);
    
    // Draw a line
    POINT StartPoint, EndPoint;
    sc.CoordinateToScreenPoint(sc.Low[sc.ArraySize-10], sc.ArraySize-10, StartPoint);
    sc.CoordinateToScreenPoint(sc.High[sc.ArraySize-1], sc.ArraySize-1, EndPoint);
    
    MoveToEx(GDIHandle, StartPoint.x, StartPoint.y, NULL);
    LineTo(GDIHandle, EndPoint.x, EndPoint.y);
    
    // Clean up GDI objects
    SelectObject(GDIHandle, OldPen);
    SelectObject(GDIHandle, OldFont);
    DeleteObject(Pen);
    DeleteObject(Font);

**Important:** Custom GDI drawing should only be done during the final drawing passes, not during calculation phases. Always clean up GDI objects to prevent resource leaks.

#### Drawing Custom Markers

    // Drawing a custom marker at specific points
    if (sc.IsUserAllowedForSCDLLName == false || !sc.ChartAllowsGDIDrawing || sc.LastCallToFunction)
        return;
    
    HDC GDIHandle = sc.GetBarGraphGDIHandle();
    if (GDIHandle == NULL)
        return;
    
    // Create brush and pen
    HBRUSH Brush = CreateSolidBrush(RGB(0, 255, 0));
    HPEN Pen = CreatePen(PS_SOLID, 1, RGB(0, 0, 0));
    HBRUSH OldBrush = (HBRUSH)SelectObject(GDIHandle, Brush);
    HPEN OldPen = (HPEN)SelectObject(GDIHandle, Pen);
    
    // Loop through visible bars
    for (int BarIndex = sc.UpdateStartIndex; BarIndex < sc.ArraySize; BarIndex++) {
        // Check condition for drawing marker
        if (sc.Subgraph[0][BarIndex] > 0) {
            // Convert chart coordinates to screen coordinates
            POINT ScreenPoint;
            sc.CoordinateToScreenPoint(sc.High[BarIndex] + 1.0f, BarIndex, ScreenPoint);
            
            // Draw a circle
            Ellipse(GDIHandle, 
                    ScreenPoint.x - 5, ScreenPoint.y - 5, 
                    ScreenPoint.x + 5, ScreenPoint.y + 5);
        }
    }
    
    // Clean up
    SelectObject(GDIHandle, OldBrush);
    SelectObject(GDIHandle, OldPen);
    DeleteObject(Brush);
    DeleteObject(Pen);

This documentation was created as a comprehensive reference guide for ACSIL (Advanced Custom Study Interface Library) in Sierra Chart. It includes the most up-to-date methods and functions available at the time of writing.