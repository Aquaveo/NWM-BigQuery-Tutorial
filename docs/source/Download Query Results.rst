Download Query Results
======================
In this section of the tutorial, you'll learn how to add the capability to download query results in the form of CSV files.

0. Start from Previous Solution (Optional)
-------------------------------------------
If you wish to use the previous solution as a starting point:

.. code-blocK:: bash
        
    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-10-Complete

1. Setup
----------
In order to get our downloaded files, you'll need to add a few script tags in `home.html`:

.. code-block:: html

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.1.5/jszip.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/1.3.8/FileSaver.min.js"></script>

We're also going to add a way to make sure that the user has run a query before trying to download results. 
Go ahead and add these variables at the top of your `app.js` file:

.. code-block:: javascript

    var query_run = false; // Check if the query has been run with the current input selections
    var csvData;
    var variable_selected;
    var reach_id_selected;

These other variables are going to be used to populate or label your data in the csv files.

2. Add Download Button Click Listener
-------------------------------------
Next, you'll need to add a click listener to the download button in `home.js` within our main function:

.. code-block:: javascript
    
    $("#download-button").on("click", function() {
        if (query_run) {
           var zip = new JSZip();
           var folder = zip.folder(`result_data`);
        
           for (var group_name in csvData) {
              
              var csvFile = csvData[group_name].map(e => e.join(",")).join("\n");
              folder.file(`${variable_selected}_at_reach_${reach_id_selected}_${group_name}_values.csv`, csvFile);
           }
           zip.generateAsync({type:"blob"})
           .then(function(content) {
              saveAs(content, `NWM_${variable_selected}_at_reach_${reach_id_selected}_result_data.zip`)
           });
              var blob = new Blob([csvFile], { type: 'text/csv;charset=utf-8;' });
              var link = document.createElement("a");
              var url = URL.createObjectURL(blob);
              link.setAttribute("href", url);
              link.setAttribute("download", `${variable_selected}_at_reach_${reach_id_selected}_${group_name}_values.csv`);
              link.click();
              link.remove();
           
           
        
        }
        else {
           TETHYS_APP_BASE.alert("danger", "Please run the query before downloading the CSV file.");
        }
     });

This code will create a zip file with all the CSV files that were generated from the query results. 
The zip file will be downloaded when the download button is clicked.

Next, we need to make sure our query results are stored in the `csvData` variable.
Add the following code to your fetch request:

.. code-block:: javascript

    .then(data => {
            MAP_LAYOUT.update_plot(`${variable} at ${reach}`, data.data, data.graph_layout);
           $("#loading-gif-div").hide();
           // Add these lines:
            query_run = true;
            
            variable_selected = formData.get('variable_choice');
            reach_id_selected = formData.get('reach_id');
            csvData = {};
            
            var header = ["datetime", `${variable}`, "ensemble"];
   
            data.download_data.forEach(obj => {
               var time_values = obj.reference_time;
               var variable_values = obj.variable_values;
               var ensemble_values = obj.ensemble;
               csvData[obj.group_name] = [];
               csvData[obj.group_name].push(header);

               for (var i = 0; i < time_values.length; i++) {
                  csvData[obj.group_name].push([time_values[i], variable_values[i], ensemble_values[i]]);
               }
            });

Lastly, we want our query run variable to properly update when we haven't run a query. We're going to set our application up to set the query_run variable to false when any changes are made to any of the input fields.
We do that with this code in our main function:

.. code-block:: javascript
    
    $(".form-control").on("input", function() {
        query_run = false;
      });

    $(".form-control").datepicker({onSelect: function() {
        query_run = false;
    }
    });
  
We'll also want any reach selections to update the query_run variable:

.. code-block:: javascript

    if (currentStreamFeatureID != undefined) {
        $("#reach_id").val(currentStreamFeatureID);
        query_run = false;

Now all that's left is to test the download button. Run any query you'd like, then press the download button after your results appear on the graph. 

You should get a zip file download with all the CSV files that were generated from the query results corresponding to each available ensemble and min and max values.

3. Solution
------------
This concludes the Download Query Results section of the NWM BigQuery Tutorial. You can view the solution on GitHub at https://github.com/Aquaveo/tethys-bigquery/tree/Step-12-Complete or clone it as follows:

.. code-block:: bash

    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-12-Complete 