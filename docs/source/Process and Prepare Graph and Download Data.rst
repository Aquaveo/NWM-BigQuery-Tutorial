Process and Prepare Graph and Download Data
===========================================
In this section of the tutorial, you'll be learning how to process your query results and prepare the data for graphing and displaying.

0. Solution
-----------
If you wish to use the previous solution as a starting point:

.. code-block:: bash
    
    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-7-Complete

1. Graph Data Preparation
-------------------------
Before we can graph our data, we need to process and prep the data we get from our query. To do this, 
we’ll need to add a new method. Copy this method into your `controllers.py` file in your `NWMBigQueryMap` class:

.. code-block:: python

    def get_graph_data(self, query_results, variable_choice):
        ensembles = query_results['ensemble'].unique().tolist()
        data_sets = []
        for ensemble in ensembles:
            # Only make the 0 ensemble data visible by default in our graph
            visible = True if ensemble == 0 else 'legendonly'
            filtered_rows = query_results[query_results['ensemble'] == ensemble]
           
            x_data = filtered_rows["reference_time"].tolist()
            y_data = filtered_rows["variable_value"].tolist()


            data_sets.append({'x': x_data, 'y': y_data, 'name': f'Ensemble {ensemble}', 'visible': visible})
           
        min_values = query_results.groupby('reference_time')['variable_value'].min().tolist()
        max_values = query_results.groupby('reference_time')['variable_value'].max().tolist()
       
        datetimes = query_results['reference_time'].unique().tolist()
        data_sets.append({'x': datetimes, 'y': min_values, 'name': 'Min Values', 'visible': 'legendonly', })
        data_sets.append({'x': datetimes, 'y': max_values, 'name': 'Max Values', 'visible': 'legendonly', })


        # Create a layout dictionary to customize the appearance of the graph
        layout = {
            'xaxis': {'title': 'Date/Time'},
            'yaxis': {'title': f'{variable_choice}'},
            'template': 'plotly_dark'
        }


        return data_sets, layout

Let’s go over the steps we take here:
First, we will end up plotting each ensemble separately, so we’ll need each unique ensemble value to filter by. 
Then we filter out our reference time and variable values by each ensemble:

.. code-block:: python

    ensembles = query_results['ensemble'].unique().tolist()
    data_sets = []
    for ensemble in ensembles:
        # Only make the 0 ensemble data visible by default in our graph
        visible = True if ensemble == 0 else 'legendonly'
        filtered_rows = query_results[query_results['ensemble'] == ensemble]
        
        x_data = filtered_rows["reference_time"].tolist()
        y_data = filtered_rows["variable_value"].tolist()


        data_sets.append({'x': x_data, 'y': y_data, 'name': f'Ensemble {ensemble}', 'visible': visible})

We now have a list of dictionaries with our time and variable value data grouped by ensemble.

We’ll also be displaying the min and max values at each time interval, so we’ll need to group by time and then get the 
min and max value at each time. We then append those to our overall data that will be returned:

.. code-block:: python

    min_values = query_results.groupby('reference_time')['variable_value'].min().tolist()
    max_values = query_results.groupby('reference_time')['variable_value'].max().tolist()
    
    datetimes = query_results['reference_time'].unique().tolist()
    data_sets.append({'x': datetimes, 'y': min_values, 'name': 'Min Values', 'visible': 'legendonly', })
    data_sets.append({'x': datetimes, 'y': max_values, 'name': 'Max Values', 'visible': 'legendonly', })

Finally, we will also be prepping our graph layout information. This information will be used to stylize our graph and its 
labels. We do that on these lines:

.. code-block:: python
    
    layout = {
            'xaxis': {'title': 'Date/Time'},
            'yaxis': {'title': f'{variable_choice}'},
            'template': 'plotly_dark'
        }

2. Download Data Preparation
----------------------------
Our final step in this portion of the tutorial is to process our data for downloading the graph results into a CSV file. 
The method that handles this is very similar to the graph data function. Copy this code into your `NWMBigQueryMap` class:

.. code-block:: python

    def get_download_data(self, query_results):
        ensembles = query_results['ensemble'].unique()
        data_groups = []
       
        for ensemble in ensembles:
            rows = query_results[query_results['ensemble'] == ensemble]
            new_values_dict = {"group_name": f"Ensemble {ensemble}", "reference_time": rows["reference_time"].tolist(), "variable_values": rows["variable_value"].tolist(), "ensemble": rows["ensemble"].tolist()}
            data_groups.append(new_values_dict)


        min_indices = query_results.groupby('reference_time')['variable_value'].idxmin()
        max_indices = query_results.groupby('reference_time')['variable_value'].idxmax()
        min_rows = query_results.loc[min_indices]
        max_rows = query_results.loc[max_indices]


        data_groups.append({"group_name": "Minimum Values", "reference_time": min_rows["reference_time"].tolist(), "variable_values": min_rows["variable_value"].tolist(), "ensemble": min_rows["ensemble"].tolist()})
        data_groups.append({"group_name": "Maximum Values", "reference_time": max_rows["reference_time"].tolist(), "variable_values": max_rows["variable_value"].tolist(), "ensemble": max_rows["ensemble"].tolist()})


        return data_groups

The only major difference here is our labelling. Here we add ensemble labels with our data groups so that our CSV file will provide 
more information from our query results.

3. Final Changes and Test Methods
---------------------------------
Now that we've completed our data preparation methods, let's test them both.
First, we'll need to make a quick change to our POST endpoint method

.. code-block:: python

    def post(self, request, *args, **kwargs):
        form_data = request.POST


        query_results = self.run_query(form_data)
        data, layout = self.get_graph_data(query_results, form_data.get('variable'))
        download_data = self.get_download_data(query_results)


        return JsonResponse({'message': "Success",
                             'download_data':download_data,
                             'data': data,
                             'graph_layout': layout})

Now, go back to your application and run a query to see the results of our graph and download data methods in the console
    
4. Solution
-----------
This concludes the Process and Prepare Graph and Download Data portion of the tutorial. You can view the solution on GitHub at https://github.com/Aquaveo/tethys-bigquery/tree/Step-8-Complete or clone it as follows:

.. code-block:: bash

    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-8-Complete 





