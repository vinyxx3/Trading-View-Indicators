// @version=6
indicator("Volume Averages", overlay=true)

// Constants and variables
var startDate2025 = timestamp("2025-01-01T00:00:00")
var twoWeeksAgo = timenow - (1000 * 60 * 60 * 24 * 14)
var oneWeekAgo = timenow - (1000 * 60 * 60 * 24 * 7)

// Variables to store sums and counts for all candles
var float vol2025Sum = 0.0
var int vol2025Count = 0
var float volTwoWeeksSum = 0.0
var int volTwoWeeksCount = 0
var float volOneWeekSum = 0.0
var int volOneWeekCount = 0

// Variables for green candles
var float vol2025GreenSum = 0.0
var int vol2025GreenCount = 0
var float volTwoWeeksGreenSum = 0.0
var int volTwoWeeksGreenCount = 0
var float volOneWeekGreenSum = 0.0
var int volOneWeekGreenCount = 0

// Variables for red candles
var float vol2025RedSum = 0.0
var int vol2025RedCount = 0
var float volTwoWeeksRedSum = 0.0
var int volTwoWeeksRedCount = 0
var float volOneWeekRedSum = 0.0
var int volOneWeekRedCount = 0

// Create the table once - need 10 rows to accommodate all data (header + 3 time periods with 3 rows each)
var table volumeTable = table.new(position.top_right, 2, 10, bgcolor=color.new(color.black, 70), border_width=1, border_color=color.gray)

// Determine if current candle is green or red
isGreenCandle = close >= open
isRedCandle = close < open

// Update calculations with each new bar
if time >= startDate2025
    vol2025Sum := vol2025Sum + volume
    vol2025Count := vol2025Count + 1
    
    if isGreenCandle
        vol2025GreenSum := vol2025GreenSum + volume
        vol2025GreenCount := vol2025GreenCount + 1
    else if isRedCandle
        vol2025RedSum := vol2025RedSum + volume
        vol2025RedCount := vol2025RedCount + 1

if time >= twoWeeksAgo
    volTwoWeeksSum := volTwoWeeksSum + volume
    volTwoWeeksCount := volTwoWeeksCount + 1
    
    if isGreenCandle
        volTwoWeeksGreenSum := volTwoWeeksGreenSum + volume
        volTwoWeeksGreenCount := volTwoWeeksGreenCount + 1
    else if isRedCandle
        volTwoWeeksRedSum := volTwoWeeksRedSum + volume
        volTwoWeeksRedCount := volTwoWeeksRedCount + 1
    
if time >= oneWeekAgo
    volOneWeekSum := volOneWeekSum + volume
    volOneWeekCount := volOneWeekCount + 1
    
    if isGreenCandle
        volOneWeekGreenSum := volOneWeekGreenSum + volume
        volOneWeekGreenCount := volOneWeekGreenCount + 1
    else if isRedCandle
        volOneWeekRedSum := volOneWeekRedSum + volume
        volOneWeekRedCount := volOneWeekRedCount + 1

// Calculate the averages
avgVolume2025 = vol2025Count > 0 ? vol2025Sum / vol2025Count : 0
avgVolume2025Green = vol2025GreenCount > 0 ? vol2025GreenSum / vol2025GreenCount : 0
avgVolume2025Red = vol2025RedCount > 0 ? vol2025RedSum / vol2025RedCount : 0

avgVolumeTwoWeeks = volTwoWeeksCount > 0 ? volTwoWeeksSum / volTwoWeeksCount : 0
avgVolumeTwoWeeksGreen = volTwoWeeksGreenCount > 0 ? volTwoWeeksGreenSum / volTwoWeeksGreenCount : 0
avgVolumeTwoWeeksRed = volTwoWeeksRedCount > 0 ? volTwoWeeksRedSum / volTwoWeeksRedCount : 0

avgVolumeOneWeek = volOneWeekCount > 0 ? volOneWeekSum / volOneWeekCount : 0
avgVolumeOneWeekGreen = volOneWeekGreenCount > 0 ? volOneWeekGreenSum / volOneWeekGreenCount : 0
avgVolumeOneWeekRed = volOneWeekRedCount > 0 ? volOneWeekRedSum / volOneWeekRedCount : 0

// Custom function to format numbers with commas
formatWithCommas(number) =>
    string result = str.tostring(math.round(number))
    string formatted = ""
    int len = str.length(result)
    
    for i = 0 to len - 1
        if i > 0 and (len - i) % 3 == 0
            formatted := formatted + ","
        formatted := formatted + str.substring(result, i, i + 1)
    
    formatted

// Only update the table on the last bar
if barstate.islast
    // Format all values with commas
    avgVolume2025Str = formatWithCommas(avgVolume2025)
    avgVolume2025GreenStr = formatWithCommas(avgVolume2025Green)
    avgVolume2025RedStr = formatWithCommas(avgVolume2025Red)
    
    avgVolumeTwoWeeksStr = formatWithCommas(avgVolumeTwoWeeks)
    avgVolumeTwoWeeksGreenStr = formatWithCommas(avgVolumeTwoWeeksGreen)
    avgVolumeTwoWeeksRedStr = formatWithCommas(avgVolumeTwoWeeksRed)
    
    avgVolumeOneWeekStr = formatWithCommas(avgVolumeOneWeek)
    avgVolumeOneWeekGreenStr = formatWithCommas(avgVolumeOneWeekGreen)
    avgVolumeOneWeekRedStr = formatWithCommas(avgVolumeOneWeekRed)
    
    // Set table headers
    table.cell(volumeTable, 0, 0, "Time Period", text_color=color.white, bgcolor=color.new(color.blue, 30))
    table.cell(volumeTable, 1, 0, "Average Volume", text_color=color.white, bgcolor=color.new(color.blue, 30))
    
    // Group 1: 2025 to now 
    table.cell(volumeTable, 0, 1, "2025 to YTD", text_color=color.white, bgcolor=color.new(color.blue, 70))
    table.cell(volumeTable, 1, 1, avgVolume2025Str, text_color=color.white, bgcolor=color.new(color.blue, 70))
    
    // 2025 Candle data
    table.cell(volumeTable, 0, 2, "↑ Green (" + str.tostring(vol2025GreenCount) + ")", text_color=color.white, bgcolor=color.new(color.green, 70))
    table.cell(volumeTable, 1, 2, avgVolume2025GreenStr, text_color=color.white)
    
    table.cell(volumeTable, 0, 3, "↓ Red (" + str.tostring(vol2025RedCount) + ")", text_color=color.white, bgcolor=color.new(color.red, 70))
    table.cell(volumeTable, 1, 3, avgVolume2025RedStr, text_color=color.white)
    
    // Group 2: Last 2 weeks
    table.cell(volumeTable, 0, 4, "Last 2 Weeks", text_color=color.white, bgcolor=color.new(color.blue, 70))
    table.cell(volumeTable, 1, 4, avgVolumeTwoWeeksStr, text_color=color.white, bgcolor=color.new(color.blue, 70))
    
    // 2 Weeks Candle data
    table.cell(volumeTable, 0, 5, "↑ Green (" + str.tostring(volTwoWeeksGreenCount) + ")", text_color=color.white, bgcolor=color.new(color.green, 70))
    table.cell(volumeTable, 1, 5, avgVolumeTwoWeeksGreenStr, text_color=color.white)
    
    table.cell(volumeTable, 0, 6, "↓ Red (" + str.tostring(volTwoWeeksRedCount) + ")", text_color=color.white, bgcolor=color.new(color.red, 70))
    table.cell(volumeTable, 1, 6, avgVolumeTwoWeeksRedStr, text_color=color.white)
    
    // Group 3: Last 1 week 
    table.cell(volumeTable, 0, 7, "Last 1 Week", text_color=color.white, bgcolor=color.new(color.blue, 70))
    table.cell(volumeTable, 1, 7, avgVolumeOneWeekStr, text_color=color.white, bgcolor=color.new(color.blue, 70))
    
    // 1 Week Candle data
    table.cell(volumeTable, 0, 8, "↑ Green (" + str.tostring(volOneWeekGreenCount) + ")", text_color=color.white, bgcolor=color.new(color.green, 70))
    table.cell(volumeTable, 1, 8, avgVolumeOneWeekGreenStr, text_color=color.white)
    
    table.cell(volumeTable, 0, 9, "↓ Red (" + str.tostring(volOneWeekRedCount) + ")", text_color=color.white, bgcolor=color.new(color.red, 70))
    table.cell(volumeTable, 1, 9, avgVolumeOneWeekRedStr, text_color=color.white)