# Search

## Overview

BugSplat provides powerful searching and filtering tools that help users better understand the various intricacies of their crash data. Users can build complex queries via the BugSplat search controls, share the results with co-workers, and export the results for use in other tools. 

Want to find all crashes that occurred from a specific IP Address? Need to isolate all crashes that happened before a certain date? Looking for any crash that contains a specific function anywhere in the call stack? BugSplat can do it all! Crafting a search to isolate the crash data you need is straightforward and intuitive.

BugSpat's data can also be searched and filtered directly via our web-services [APIs](web-services/paging-filtering-and-grouping.md).

## Search

To use the search tool first click on the button labeled 🔍 **Search** which can be found on the Crash, Summary, and Key Crash pages.

Next, select a column to filter in the Search dropdown. After picking a column, select the desired search criteria and enter a value and click **Submit**. Repeat this process until you've found exactly what you're looking for.

In the BugSplat web application, users can filter their crash data based on almost any piece of data attributed to a crash or stack key.

![](../../.gitbook/assets/crafting-search-example.gif)

### Column Types

BugSplat's data is grouped into various columns. Each column can hold 1 of 4 data types. Each data type has slightly different options regarding how it can be searched. The types of data searchable within BugSplat are as follows:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Type</th>
      <th style="text-align:left">Options</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Text</td>
      <td style="text-align:left">
        <p></p>
        <p>equal to, not equal to, contains, does not contain, empty, not empty,
          starts with, ends with</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Number</td>
      <td style="text-align:left">equal to, not equal to, greater than, less than, empty, not empty</td>
    </tr>
    <tr>
      <td style="text-align:left">Date</td>
      <td style="text-align:left">on or before, on or after</td>
    </tr>
  </tbody>
</table>

### Sharing and Exporting Searches

Once you have the correct set of data selected, you might want to click **Copy Search**, to save the URL to your clipboard to share with a team member.

Searches can be bookmarked by your browser so that they can be accessed more quickly.

You can also export the selected data to CSV or JSON using the **Export** button so that the data can be viewed in tools such as Microsoft Excel.

![](../../.gitbook/assets/copy-export-crashes-bs.gif)

### 



