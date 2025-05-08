  ACSIL (Advanced Custom Study Interface Library) Documentation   body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; background-color: #f8f9fa; } .container { max-width: 1200px; margin: 0 auto; padding: 20px; background-color: white; box-shadow: 0 0 10px rgba(0,0,0,0.1); } h1 { font-size: 2.5rem; margin-bottom: 1rem; color: #2c3e50; border-bottom: 2px solid #3498db; padding-bottom: 0.5rem; } h2 { font-size: 2rem; margin-top: 2rem; margin-bottom: 1rem; color: #2c3e50; border-bottom: 1px solid #bdc3c7; padding-bottom: 0.3rem; } h3 { font-size: 1.5rem; margin-top: 1.5rem; margin-bottom: 0.75rem; color: #34495e; } h4 { font-size: 1.25rem; margin-top: 1.25rem; margin-bottom: 0.5rem; color: #2c3e50; } p, ul, ol { margin-bottom: 1.25rem; } ul, ol { padding-left: 1.5rem; } ul li, ol li { margin-bottom: 0.5rem; } code { font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace; background-color: #f5f5f5; padding: 0.2rem 0.4rem; border-radius: 3px; font-size: 0.9em; border: 1px solid #e1e1e8; } pre { background-color: #f8f9fa; padding: 1rem; border-radius: 5px; border: 1px solid #e1e1e8; overflow-x: auto; margin-bottom: 1.5rem; } pre code { background-color: transparent; padding: 0; border: none; font-size: 0.9rem; } .note { background-color: #e8f4f8; border-left: 4px solid #3498db; padding: 0.75rem 1rem; margin-bottom: 1.5rem; } .warning { background-color: #fef9e7; border-left: 4px solid #f39c12; padding: 0.75rem 1rem; margin-bottom: 1.5rem; } .tip { background-color: #eafaf1; border-left: 4px solid #2ecc71; padding: 0.75rem 1rem; margin-bottom: 1.5rem; } .table-container { overflow-x: auto; margin-bottom: 1.5rem; } table { width: 100%; border-collapse: collapse; } table th, table td { border: 1px solid #ddd; padding: 8px; text-align: left; } table th { background-color: #f2f2f2; font-weight: bold; } table tr:nth-child(even) { background-color: #f9f9f9; } .toc { background-color: #f8f9fa; padding: 1rem; border: 1px solid #e1e1e8; border-radius: 5px; margin-bottom: 2rem; } .toc ul { list-style-type: none; padding-left: 0; } .toc ul ul { padding-left: 1.5rem; } .toc a { text-decoration: none; color: #3498db; } .toc a:hover { text-decoration: underline; }

ACSIL (Advanced Custom Study Interface Library) Documentation
=============================================================

### Table of Contents

*   [1\. Introduction](#introduction)
*   [2\. Basic Study Structure](#basic-study-structure)
*   [3\. Key Programming Concepts](#key-programming-concepts)
    *   [3.1 Study Function Organization](#study-function-organization)
    *   [3.2 Variables, Arrays, and Structures](#variables-arrays-and-structures)
    *   [3.3 Looping Mechanisms](#looping-mechanisms)
    *   [3.4 Persistent Variables](#persistent-variables)
*   [4\. Important Functions](#important-functions)
    *   [4.1 Array Calculation Functions](#array-calculation-functions)
    *   [4.2 Trading Functions](#trading-functions)
    *   [4.3 Drawing Tools (UseTool)](#drawing-tools)
    *   [4.4 Helper Functions](#helper-functions)
*   [5\. Best Practices](#best-practices)
*   [6\. Full Examples](#examples)
*   [7\. Reference Documentation](#reference)

1\. Introduction
----------------

Sierra Chart's Advanced Custom Study Interface and Language (ACSIL) is a powerful C++-based programming interface that allows you to create custom studies, indicators, and trading systems. ACSIL provides extensive access to chart data, market depth information, and trading functionality, making it a versatile tool for both analysis and automated trading.

**Note:** ACSIL is based on C++ and compiled to native code, which provides optimal performance for your custom indicators and systems. However, only basic knowledge of C++ is required to create functional studies.

2\. Basic Study Structure
-------------------------

Every ACSIL study is contained in a C++ function that follows a specific signature pattern. The basic structure of an ACSIL study function is:

    // Required includes at the top of the file
    #include "sierrachart.h"
    
    // This defines the DLL name that will appear in Sierra Chart
    SCDLLName("My Custom Studies")
    
    // The study function follows this format
    SCSFExport scsf_MyCustomStudy(SCStudyInterfaceRef sc)
    {
        // Check if we're setting defaults
        if (sc.SetDefaults)
        {
            // Configure study defaults and settings
            sc.GraphName = "My Custom Study";
            
            // Configure subgraphs (visual outputs)
            sc.Subgraph[0].Name = "Main Line";
            sc.Subgraph[0].DrawStyle = DRAWSTYLE_LINE;
            sc.Subgraph[0].PrimaryColor = RGB(0, 255, 0);
            
            // Configure inputs
            sc.Input[0].Name = "Period";
            sc.Input[0].SetInt(14);
            
            // Enable automatic looping
            sc.AutoLoop = 1;
            
            return;
        }
        
        // Data processing code goes here
        float Period = sc.Input[0].GetInt();
        
        // Perform calculations
        sc.Subgraph[0][sc.Index] = sc.Close[sc.Index] * 1.01;  
    }

3\. Key Programming Concepts
----------------------------

### 3.1 Study Function Organization

ACSIL study functions are organized into two main sections:

1.  **Initialization Section** (executed when sc.SetDefaults is true):
    *   Define graph properties (name, description)
    *   Configure subgraphs (visual outputs)
    *   Set up inputs (parameters users can modify)
    *   Configure other study properties
2.  **Data Processing Section** (executed when sc.SetDefaults is false):
    *   Read input values and chart data
    *   Perform calculations
    *   Update output arrays
    *   Handle drawing, alerts, trading signals, etc.

### 3.2 Variables, Arrays, and Structures

ACSIL provides several important variables, arrays, and structures for accessing and manipulating data:

#### The `sc` Interface Structure

The `sc` parameter passed to your study function provides access to all Sierra Chart data, functions, and settings.

#### Important Array Types

*   `sc.BaseData[]/sc.BaseDataIn[]`: Arrays for accessing price data (open, high, low, close, volume)
*   `sc.Subgraph[]`: Arrays for outputting calculated values to be displayed on the chart
*   `sc.Input[]`: Arrays for study input parameters that users can configure
*   `sc.DateTimeIn[]/sc.DateTimeOut[]`: Arrays containing date-time values for each bar

#### Accessing Base Data

Base data arrays provide access to price and volume information:

    // Accessing price data
    float CurrentClose = sc.BaseData[SC_CLOSE][sc.Index];
    float PreviousClose = sc.BaseData[SC_CLOSE][sc.Index - 1];
    float CurrentHigh = sc.BaseData[SC_HIGH][sc.Index];
    float CurrentLow = sc.BaseData[SC_LOW][sc.Index];
    float CurrentOpen = sc.BaseData[SC_OPEN][sc.Index];
    float CurrentVolume = sc.BaseData[SC_VOLUME][sc.Index];

Shorthand alternatives:

    float CurrentClose = sc.Close[sc.Index]; // Same as sc.BaseData[SC_CLOSE][sc.Index]
    float CurrentHigh = sc.High[sc.Index];   // Same as sc.BaseData[SC_HIGH][sc.Index]
    float CurrentLow = sc.Low[sc.Index];     // Same as sc.BaseData[SC_LOW][sc.Index]
    float CurrentOpen = sc.Open[sc.Index];   // Same as sc.BaseData[SC_OPEN][sc.Index]
    float CurrentVolume = sc.Volume[sc.Index]; // Same as sc.BaseData[SC_VOLUME][sc.Index]

#### Subgraph Arrays

Subgraphs are used for outputting values to be displayed on the chart:

    // Setting output values
    sc.Subgraph[0][sc.Index] = some_calculated_value;
    
    // Subgraph also has properties for display settings
    sc.Subgraph[0].Name = "My Indicator";
    sc.Subgraph[0].DrawStyle = DRAWSTYLE_LINE;
    sc.Subgraph[0].PrimaryColor = RGB(0, 255, 0);

Each subgraph also has extra arrays for intermediate calculations:

    // Using extra arrays for calculations that don't need to be displayed
    sc.Subgraph[0].Arrays[0][sc.Index] = intermediate_value_1;
    sc.Subgraph[0].Arrays[1][sc.Index] = intermediate_value_2;

#### Input Parameters

The `sc.Input[]` array is used to define configurable inputs:

    // In sc.SetDefaults section:
    sc.Input[0].Name = "Period";
    sc.Input[0].SetInt(14);  // Default value
    
    sc.Input[1].Name = "Use High/Low";
    sc.Input[1].SetYesNo(1); // Boolean input
    
    sc.Input[2].Name = "Multiplier";
    sc.Input[2].SetFloat(1.5); // Float input
    
    // Accessing inputs in the data processing section:
    int Period = sc.Input[0].GetInt();
    bool UseHighLow = sc.Input[1].GetYesNo();
    float Multiplier = sc.Input[2].GetFloat();

### 3.3 Looping Mechanisms

ACSIL provides two methods for processing chart data: Automatic Looping and Manual Looping.

#### Automatic Looping

The preferred and simpler method. When enabled, your study function is automatically called once for each bar, with `sc.Index` indicating the current bar index.

    // Enable automatic looping in sc.SetDefaults section
    sc.AutoLoop = 1;
    
    // Later in the data processing section:
    // sc.Index is automatically set to the current bar being processed
    sc.Subgraph[0][sc.Index] = sc.Close[sc.Index] * 1.01;

#### Manual Looping

For more control, you can implement manual looping using a for loop.

    // Disable automatic looping (default if not set)
    sc.AutoLoop = 0;
    
    // In the data processing section:
    // Must use sc.UpdateStartIndex as starting point for efficiency
    for (int Index = sc.UpdateStartIndex; Index < sc.ArraySize; Index++)
    {
        float CurrentClose = sc.Close[Index];
        sc.Subgraph[0][Index] = CurrentClose * 1.01;
    }

**Tip:** Automatic looping is simpler but manual looping can be more efficient for complex calculations, as your function is only called once per chart update rather than once per bar.

### 3.4 Persistent Variables

ACSIL provides several functions for maintaining persistent variables between function calls:

#### Persistent Integers and Floats

    // Get or initialize a persistent integer (0 if not previously set)
    int& Counter = sc.GetPersistentInt(1); // 1 is the key
    
    // Increment the counter
    Counter++;
    
    // Get or initialize a persistent float
    float& MyValue = sc.GetPersistentFloat(2); // 2 is the key
    MyValue = 3.14;

#### Persistent Pointers (for complex data structures)

    // Get a persistent pointer
    MyStruct* pMyStruct = (MyStruct*)sc.GetPersistentPointer(3);
    
    // If it doesn't exist yet, allocate memory and store the pointer
    if (pMyStruct == NULL) {
        pMyStruct = new MyStruct;
        // Initialize the structure
        pMyStruct->Value = 0;
        // Store the pointer for future calls
        sc.SetPersistentPointer(3, pMyStruct);
    }
    
    // Clean up when study is removed from chart
    if (sc.LastCallToFunction) {
        if (pMyStruct != NULL) {
            delete pMyStruct;
            sc.SetPersistentPointer(3, NULL);
        }
    }

4\. Important Functions
-----------------------

### 4.1 Array Calculation Functions

ACSIL includes many built-in functions for common technical analysis calculations. These functions typically operate on arrays and are often called "Intermediate Study Calculation Functions".

#### Moving Averages

    // Simple Moving Average
    sc.SimpleMovAvg(sc.BaseData[SC_CLOSE], sc.Subgraph[0], 20);
    
    // Exponential Moving Average
    sc.ExponentialMovAvg(sc.BaseData[SC_CLOSE], sc.Subgraph[1], 20);
    
    // Weighted Moving Average
    sc.WeightedMovAvg(sc.BaseData[SC_CLOSE], sc.Subgraph[2], 20);
    
    // Adaptive Moving Average
    sc.AdaptiveMovAvg(sc.BaseData[SC_CLOSE], sc.Subgraph[3], 20, 2.0f, 30.0f);

#### Other Technical Indicators

    // RSI (Relative Strength Index)
    sc.RSI(sc.BaseData[SC_CLOSE], sc.Subgraph[0], 14);
    
    // Bollinger Bands
    sc.BollingerBands(sc.BaseData[SC_CLOSE], sc.Subgraph[0], 20, 2.0f);
    
    // MACD
    sc.MACD(sc.BaseData[SC_CLOSE], sc.Subgraph[0], 12, 26, 9);
    
    // Stochastic
    sc.Stochastic(sc.BaseData, sc.Subgraph[0], 14, 3, 3);

### 4.2 Trading Functions

ACSIL provides comprehensive support for automated trading through a variety of functions:

#### Submitting Orders

    // Define the order
    s_SCNewOrder NewOrder;
    NewOrder.OrderType = SCT_ORDERTYPE_MARKET;
    NewOrder.OrderQuantity = 1;
    NewOrder.TextTag = "MyEntry";
    
    // Submit the order
    int Result = sc.SubmitOCOOrder(NewOrder);

#### Position Management

    // Get current position
    s_SCPositionData PositionData;
    sc.GetTradePosition(PositionData);
    float PositionQty = PositionData.PositionQuantity;
    
    // Flatten (close) all positions and cancel all orders
    sc.FlattenAndCancelAllOrders();
    
    // Flatten position only
    sc.FlattenPosition();
    
    // Cancel all working orders
    sc.CancelAllOrders();

#### Order Tracking

    // Get order by ID
    s_SCTradeOrder TradeOrder;
    sc.GetOrderByOrderID(OrderID, TradeOrder);
    
    // Get attached order IDs for a parent order
    int TargetOrderID = 0, StopOrderID = 0;
    sc.GetAttachedOrderIDsForParentOrder(ParentOrderID, TargetOrderID, StopOrderID);

### 4.3 Drawing Tools (UseTool)

ACSIL allows you to add various types of drawings to your chart using the `sc.UseTool()` function with the `s_UseTool` structure:

#### Line Drawing

    s_UseTool Tool;
    Tool.Clear(); // Always clear the tool first
    Tool.ChartNumber = sc.ChartNumber;
    Tool.DrawingType = DRAWING_LINE;
    Tool.LineNumber = 1; // Unique identifier for this line
    
    // Line start point
    Tool.BeginIndex = sc.Index - 10;
    Tool.BeginValue = sc.Low[sc.Index - 10];
    
    // Line end point
    Tool.EndIndex = sc.Index;
    Tool.EndValue = sc.High[sc.Index];
    
    // Line properties
    Tool.Color = RGB(255, 0, 0);
    Tool.LineWidth = 2;
    Tool.LineStyle = LINESTYLE_SOLID;
    
    // Add or update the line
    sc.UseTool(Tool);

#### Text Drawing

    s_UseTool Tool;
    Tool.Clear();
    Tool.ChartNumber = sc.ChartNumber;
    Tool.DrawingType = DRAWING_TEXT;
    Tool.LineNumber = 2; // Unique identifier
    
    // Position
    Tool.BeginIndex = sc.Index;
    Tool.BeginValue = sc.Close[sc.Index];
    
    // Text properties
    Tool.Text = "Signal Detected!";
    Tool.Color = RGB(0, 0, 255);
    Tool.FontSize = 14;
    Tool.FontBold = 1;
    
    sc.UseTool(Tool);

#### Marker Drawing

    s_UseTool Tool;
    Tool.Clear();
    Tool.ChartNumber = sc.ChartNumber;
    Tool.DrawingType = DRAWING_MARKER;
    Tool.LineNumber = 3; // Unique identifier
    
    // Position
    Tool.BeginIndex = sc.Index;
    Tool.BeginValue = sc.High[sc.Index] + (0.1 * sc.TickSize);
    
    // Marker properties
    Tool.MarkerType = MARKER_ARROWDOWN;
    Tool.Color = RGB(255, 0, 0);
    Tool.MarkerSize = 8;
    
    sc.UseTool(Tool);

#### Rectangle Drawing

    s_UseTool Tool;
    Tool.Clear();
    Tool.ChartNumber = sc.ChartNumber;
    Tool.DrawingType = DRAWING_RECTANGLEHIGHLIGHT;
    Tool.LineNumber = 4; // Unique identifier
    
    // Rectangle coordinates
    Tool.BeginIndex = sc.Index - 5;
    Tool.BeginValue = sc.Low[sc.Index - 5];
    Tool.EndIndex = sc.Index;
    Tool.EndValue = sc.High[sc.Index];
    
    // Rectangle properties
    Tool.Color = RGB(0, 128, 0);      // Border color
    Tool.SecondaryColor = RGB(0, 255, 0); // Fill color
    Tool.LineWidth = 1;
    Tool.TransparencyLevel = 60;      // 0-100, where 100 is completely transparent
    
    sc.UseTool(Tool);

### 4.4 Helper Functions

#### Message and Alert Functions

    // Add message to log
    sc.AddMessageToLog("This is a log message", 1); // 1 means show the log
    
    // Add alert
    sc.AddAlertLine("Alert triggered: Condition met", 1); // 1 means show the alert log

#### Date and Time Functions

    // Get current datetime for a bar
    SCDateTime CurrentDateTime = sc.BaseDateTimeIn[sc.Index];
    
    // Convert to GMT timezone
    SCDateTime GMTDateTime = sc.AdjustDateTimeToGMT(CurrentDateTime);
    
    // Format datetime as string
    SCString DateTimeStr;
    CurrentDateTime.FormatDateTimeForChart(DateTimeStr);

5\. Best Practices
------------------

1.  **Initialization and Cleanup**
    
    Always check for `sc.LastCallToFunction` and clean up any allocated resources:
    
        if (sc.LastCallToFunction) {
            // Clean up resources
            if (pMyData != NULL) {
                delete pMyData;
                sc.SetPersistentPointer(1, NULL);
            }
            return;
        }
    
2.  **Efficient Calculations**
    
    For complex studies, consider using manual looping and avoid recalculating values that have already been processed:
    
        for (int Index = sc.UpdateStartIndex; Index < sc.ArraySize; Index++) {
            // Only process data that hasn't been processed yet
        }
    
3.  **Memory Management**
    
    When using dynamic memory allocation, ensure proper memory management to avoid leaks:
    
        // Allocate memory
        void* pData = sc.AllocateMemory(size);
        
        // Free memory when done
        sc.FreeMemory(pData);
    
4.  **Error Handling**
    
    Always check return values and handle potential errors:
    
        int Result = sc.GetStudyArrayUsingID(StudyID, 0, Array);
        if (Result == 0) {
            // Handle error - study not found
        }
    
5.  **Naming Conventions**
    
    Use descriptive names for your studies and variables, and follow consistent naming conventions.
    

6\. Full Examples
-----------------

### Example 1: Simple Moving Average Crossover with Alerts

    #include "sierrachart.h"
    
    SCDLLName("Example Studies")
    
    SCSFExport scsf_MovingAverageCrossover(SCStudyInterfaceRef sc)
    {
        // Set defaults and configuration
        if (sc.SetDefaults)
        {
            sc.GraphName = "Moving Average Crossover";
            sc.StudyDescription = "Generates alerts when fast MA crosses over slow MA";
            
            // Configure inputs
            sc.Input[0].Name = "Fast MA Period";
            sc.Input[0].SetInt(10);
            
            sc.Input[1].Name = "Slow MA Period";
            sc.Input[1].SetInt(20);
            
            sc.Input[2].Name = "Show Alert";
            sc.Input[2].SetYesNo(1);
            
            // Configure subgraphs
            sc.Subgraph[0].Name = "Fast MA";
            sc.Subgraph[0].DrawStyle = DRAWSTYLE_LINE;
            sc.Subgraph[0].PrimaryColor = RGB(0, 255, 0);
            sc.Subgraph[0].LineWidth = 2;
            
            sc.Subgraph[1].Name = "Slow MA";
            sc.Subgraph[1].DrawStyle = DRAWSTYLE_LINE;
            sc.Subgraph[1].PrimaryColor = RGB(255, 0, 0);
            sc.Subgraph[1].LineWidth = 2;
            
            sc.AutoLoop = 1; // Enable automatic looping
            
            return;
        }
        
        // Get input values
        int FastPeriod = sc.Input[0].GetInt();
        int SlowPeriod = sc.Input[1].GetInt();
        int ShowAlert = sc.Input[2].GetYesNo();
        
        // Calculate moving averages
        sc.SimpleMovAvg(sc.BaseData[SC_CLOSE], sc.Subgraph[0], FastPeriod);
        sc.SimpleMovAvg(sc.BaseData[SC_CLOSE], sc.Subgraph[1], SlowPeriod);
        
        // Check for crossover (requires at least 2 bars of data)
        if (sc.Index >= 1)
        {
            bool CurrentFastAboveSlow = sc.Subgraph[0][sc.Index] > sc.Subgraph[1][sc.Index];
            bool PrevFastAboveSlow = sc.Subgraph[0][sc.Index - 1] > sc.Subgraph[1][sc.Index - 1];
            
            // Bullish crossover
            if (CurrentFastAboveSlow && !PrevFastAboveSlow && ShowAlert)
            {
                // Add buy marker
                s_UseTool Tool;
                Tool.Clear();
                Tool.ChartNumber = sc.ChartNumber;
                Tool.DrawingType = DRAWING_MARKER;
                Tool.LineNumber = sc.GetBarIndex() + 8000; // Unique ID using bar index
                Tool.BeginIndex = sc.Index;
                Tool.BeginValue = sc.Low[sc.Index] - (5 * sc.TickSize);
                Tool.Color = RGB(0, 255, 0);
                Tool.MarkerType = MARKER_ARROWUP;
                Tool.MarkerSize = 8;
                sc.UseTool(Tool);
                
                // Add alert
                SCString AlertMessage;
                AlertMessage.Format("Bullish Crossover at %.2f", sc.Close[sc.Index]);
                sc.AddAlertLine(AlertMessage, 1);
            }
            // Bearish crossover
            else if (!CurrentFastAboveSlow && PrevFastAboveSlow && ShowAlert)
            {
                // Add sell marker
                s_UseTool Tool;
                Tool.Clear();
                Tool.ChartNumber = sc.ChartNumber;
                Tool.DrawingType = DRAWING_MARKER;
                Tool.LineNumber = sc.GetBarIndex() + 9000; // Unique ID using bar index
                Tool.BeginIndex = sc.Index;
                Tool.BeginValue = sc.High[sc.Index] + (5 * sc.TickSize);
                Tool.Color = RGB(255, 0, 0);
                Tool.MarkerType = MARKER_ARROWDOWN;
                Tool.MarkerSize = 8;
                sc.UseTool(Tool);
                
                // Add alert
                SCString AlertMessage;
                AlertMessage.Format("Bearish Crossover at %.2f", sc.Close[sc.Index]);
                sc.AddAlertLine(AlertMessage, 1);
            }
        }
    }

### Example 2: Volume Profile Study

    #include "sierrachart.h"
    
    SCDLLName("Example Studies")
    
    SCSFExport scsf_SimpleVolumeProfile(SCStudyInterfaceRef sc)
    {
        // Set defaults and configuration
        if (sc.SetDefaults)
        {
            sc.GraphName = "Simple Volume Profile";
            sc.StudyDescription = "Displays volume by price as horizontal bars";
            
            // Configure inputs
            sc.Input[0].Name = "Number of Price Levels";
            sc.Input[0].SetInt(20);
            
            sc.Input[1].Name = "Lookback Bars";
            sc.Input[1].SetInt(100);
            
            sc.Input[2].Name = "Volume Bar Width (Pixels)";
            sc.Input[2].SetInt(50);
            
            // Configure subgraphs
            sc.Subgraph[0].Name = "Volume Profile";
            sc.Subgraph[0].DrawStyle = DRAWSTYLE_IGNORE; // We'll draw custom bars
            
            sc.AutoLoop = 0; // Manual looping
            
            return;
        }
        
        // Get input values
        int NumLevels = sc.Input[0].GetInt();
        int LookbackBars = sc.Input[1].GetInt();
        int BarWidth = sc.Input[2].GetInt();
        
        // Only process on the last bar
        if (sc.UpdateStartIndex != sc.ArraySize - 1)
            return;
        
        // Define price range
        float HighestHigh = -FLT_MAX;
        float LowestLow = FLT_MAX;
        
        // Find highest high and lowest low
        int StartBar = max(0, sc.ArraySize - LookbackBars);
        for (int BarIndex = StartBar; BarIndex < sc.ArraySize; BarIndex++)
        {
            HighestHigh = max(HighestHigh, sc.High[BarIndex]);
            LowestLow = min(LowestLow, sc.Low[BarIndex]);
        }
        
        // Calculate price increment for each level
        float PriceIncrement = (HighestHigh - LowestLow) / NumLevels;
        
        if (PriceIncrement <= 0)
            return; // Invalid price range
        
        // Create array to hold volume at each price level
        float* VolumeAtLevel = new float[NumLevels]();
        float MaxVolume = 0;
        
        // Count volume at each price level
        for (int BarIndex = StartBar; BarIndex < sc.ArraySize; BarIndex++)
        {
            float BarHigh = sc.High[BarIndex];
            float BarLow = sc.Low[BarIndex];
            float BarVolume = sc.Volume[BarIndex];
            
            // Distribute volume across price levels the bar spans
            for (int Level = 0; Level < NumLevels; Level++)
            {
                float LevelLow = LowestLow + (Level * PriceIncrement);
                float LevelHigh = LevelLow + PriceIncrement;
                
                // Check if bar overlaps this level
                if (BarLow <= LevelHigh && BarHigh >= LevelLow)
                {
                    // Calculate volume contribution to this level
                    float OverlapHigh = min(BarHigh, LevelHigh);
                    float OverlapLow = max(BarLow, LevelLow);
                    float OverlapRatio = (OverlapHigh - OverlapLow) / (BarHigh - BarLow);
                    
                    // Add proportional volume to this level
                    VolumeAtLevel[Level] += BarVolume * OverlapRatio;
                    MaxVolume = max(MaxVolume, VolumeAtLevel[Level]);
                }
            }
        }
        
        // Draw volume bars
        for (int Level = 0; Level < NumLevels; Level++)
        {
            float LevelPrice = LowestLow + (Level * PriceIncrement) + (PriceIncrement / 2);
            float BarLength = (VolumeAtLevel[Level] / MaxVolume) * BarWidth;
            
            // Draw volume bar
            if (BarLength > 0)
            {
                s_UseTool Tool;
                Tool.Clear();
                Tool.ChartNumber = sc.ChartNumber;
                Tool.DrawingType = DRAWING_HORIZONTALLINE;
                Tool.LineNumber = 10000 + Level; // Unique ID
                
                Tool.BeginValue = LevelPrice;
                Tool.EndValue = LevelPrice;
                Tool.BeginIndex = sc.ArraySize - 1; // Draw at the last bar
                Tool.EndIndex = sc.ArraySize - 1 - (int)BarLength; // Length proportional to volume
                
                // Color based on volume (normalize to 0-255 range)
                int ColorIntensity = (int)(VolumeAtLevel[Level] * 255 / MaxVolume);
                Tool.Color = RGB(0, ColorIntensity, 255 - ColorIntensity);
                
                Tool.LineWidth = (int)(PriceIncrement / sc.TickSize) - 1; // Make bars thicker
                Tool.Region = 0; // Draw in main price graph region
                
                sc.UseTool(Tool);
            }
        }
        
        // Clean up
        delete[] VolumeAtLevel;
    }

### Example 3: Simple Trading System

    #include "sierrachart.h"
    
    SCDLLName("Example Trading Systems")
    
    SCSFExport scsf_SimpleBreakoutSystem(SCStudyInterfaceRef sc)
    {
        // Set defaults and configuration
        if (sc.SetDefaults)
        {
            sc.GraphName = "Simple Breakout System";
            sc.StudyDescription = "Trades breakouts of highest high/lowest low";
            
            // Configure inputs
            sc.Input[0].Name = "Lookback Period";
            sc.Input[0].SetInt(20);
            
            sc.Input[1].Name = "Position Size";
            sc.Input[1].SetInt(1);
            
            sc.Input[2].Name = "Stop Loss (Ticks)";
            sc.Input[2].SetInt(10);
            
            sc.Input[3].Name = "Take Profit (Ticks)";
            sc.Input[3].SetInt(20);
            
            sc.Input[4].Name = "Enable Trading";
            sc.Input[4].SetYesNo(0); // Default to disabled for safety
            
            // Configure subgraphs
            sc.Subgraph[0].Name = "Buy Level";
            sc.Subgraph[0].DrawStyle = DRAWSTYLE_DASH;
            sc.Subgraph[0].PrimaryColor = RGB(0, 255, 0);
            sc.Subgraph[0].LineWidth = 2;
            
            sc.Subgraph[1].Name = "Sell Level";
            sc.Subgraph[1].DrawStyle = DRAWSTYLE_DASH;
            sc.Subgraph[1].PrimaryColor = RGB(255, 0, 0);
            sc.Subgraph[1].LineWidth = 2;
            
            sc.AutoLoop = 1; // Enable automatic looping
            
            return;
        }
        
        // Get inputs
        int LookbackPeriod = sc.Input[0].GetInt();
        int PositionSize = sc.Input[1].GetInt();
        int StopLossTicks = sc.Input[2].GetInt();
        int TakeProfitTicks = sc.Input[3].GetInt();
        bool EnableTrading = sc.Input[4].GetYesNo();
        
        // Handle invalid inputs
        if (LookbackPeriod <= 0 || sc.Index < LookbackPeriod)
            return;
        
        // Find highest high and lowest low
        float HighestHigh = -FLT_MAX;
        float LowestLow = FLT_MAX;
        
        for (int i = 1; i <= LookbackPeriod; i++)
        {
            int BarIndex = sc.Index - i;
            if (BarIndex >= 0)
            {
                HighestHigh = max(HighestHigh, sc.High[BarIndex]);
                LowestLow = min(LowestLow, sc.Low[BarIndex]);
            }
        }
        
        // Set subgraph values for visualization
        sc.Subgraph[0][sc.Index] = HighestHigh;
        sc.Subgraph[1][sc.Index] = LowestLow;
        
        // Get current position
        s_SCPositionData PositionData;
        sc.GetTradePosition(PositionData);
        
        // Only execute trading logic on the last bar
        if (sc.Index != sc.ArraySize - 1 || !EnableTrading)
            return;
        
        // Define stop loss and take profit in ticks
        float StopLoss = StopLossTicks * sc.TickSize;
        float TakeProfit = TakeProfitTicks * sc.TickSize;
        
        // Check for breakout conditions
        bool BuySignal = sc.Close[sc.Index] > HighestHigh && 
                         sc.Close[sc.Index - 1] <= HighestHigh;
        
        bool SellSignal = sc.Close[sc.Index] < LowestLow && 
                          sc.Close[sc.Index - 1] >= LowestLow;
        
        // Execute trades
        if (PositionData.PositionQuantity == 0) // No current position
        {
            if (BuySignal)
            {
                s_SCNewOrder BuyOrder;
                BuyOrder.OrderType = SCT_ORDERTYPE_MARKET;
                BuyOrder.Price1 = 0; // Market order
                BuyOrder.OrderQuantity = PositionSize;
                BuyOrder.TextTag = "Breakout Buy";
                
                // Set attached orders (stop loss and target)
                BuyOrder.Target1Price = sc.Close[sc.Index] + TakeProfit;
                BuyOrder.Stop1Price = sc.Close[sc.Index] - StopLoss;
                
                // Submit order
                sc.SubmitOCOOrder(BuyOrder);
                
                // Log the trade
                SCString Message;
                Message.Format("BUY %d at %.2f - Target: %.2f, Stop: %.2f", 
                              PositionSize, sc.Close[sc.Index], 
                              BuyOrder.Target1Price, BuyOrder.Stop1Price);
                sc.AddMessageToLog(Message, 1);
            }
            else if (SellSignal)
            {
                s_SCNewOrder SellOrder;
                SellOrder.OrderType = SCT_ORDERTYPE_MARKET;
                SellOrder.Price1 = 0; // Market order
                SellOrder.OrderQuantity = -PositionSize; // Negative for sell
                SellOrder.TextTag = "Breakout Sell";
                
                // Set attached orders (stop loss and target)
                SellOrder.Target1Price = sc.Close[sc.Index] - TakeProfit;
                SellOrder.Stop1Price = sc.Close[sc.Index] + StopLoss;
                
                // Submit order
                sc.SubmitOCOOrder(SellOrder);
                
                // Log the trade
                SCString Message;
                Message.Format("SELL %d at %.2f - Target: %.2f, Stop: %.2f", 
                              PositionSize, sc.Close[sc.Index], 
                              SellOrder.Target1Price, SellOrder.Stop1Price);
                sc.AddMessageToLog(Message, 1);
            }
        }
        else if (PositionData.PositionQuantity > 0 && SellSignal) // Long position, sell signal
        {
            // Close existing position and enter new position
            sc.FlattenAndCancelAllOrders();
            
            // Wait for flattening to complete before entering a new position
            // In a real system, you would need to handle this asynchronously
        }
        else if (PositionData.PositionQuantity < 0 && BuySignal) // Short position, buy signal
        {
            // Close existing position and enter new position
            sc.FlattenAndCancelAllOrders();
            
            // Wait for flattening to complete before entering a new position
            // In a real system, you would need to handle this asynchronously
        }
    }

7\. Reference Documentation
---------------------------

For complete documentation on ACSIL functions and features, refer to the official Sierra Chart documentation:

*   [Advanced Custom Study/System Interface and Language (ACSIL)](https://www.sierrachart.com/index.php?page=doc/AdvancedCustomStudyInterfaceAndLanguage.php)
*   [Definitions of Advanced Custom Study/System Interface Members](https://www.sierrachart.com/index.php?page=doc/ACSIL_Members.html)
*   [ACSIL Interface Members - Functions](https://www.sierrachart.com/index.php?page=doc/ACSIL_Members_Functions.html)
*   [ACSIL Programming Concepts](https://www.sierrachart.com/index.php?page=doc/ACSILProgrammingConcepts.html)
*   [Working with ACSIL Arrays and Understanding Looping](https://www.sierrachart.com/index.php?page=doc/ACS_ArraysAndLooping.html)
*   [Using Drawing Tools from an Advanced Custom Study](https://www.sierrachart.com/index.php?page=doc/ACSILDrawingTools.html)
*   [Automated Trading From an Advanced Custom Study](https://www.sierrachart.com/index.php?page=doc/ACSILTrading.html)

**Note:** This documentation provides an overview of ACSIL but is not exhaustive. Always refer to the official Sierra Chart documentation for complete and up-to-date information.