Display Graph Data in Tethys application
========================================
In this portion of the tutorial, you'll learn how to visualize data on graph in a Tethys application.

0. Solution
-----------
GIT INSTRUCTIONS HERE

1. Plot Slide
-------------
The Tethys MapLayout feature comes built in with an area in the application for displaying graphs called a plot slide. 
In order to work with this plot slide, we’ll need to make a quick change to our `NWMBigQueryMap` class as follows:

.. code-block:: python

    @controller(name="home", app_workspace=True)
    class NWMBigQueryMap(MapLayout):
        app = app
        base_template = 'nwm_bigquery_tutorial/base.html'
        template_name = 'nwm_bigquery_tutorial/home.html'
        map_title = 'National Water Model BigQuery Tutorial'
        map_subtitle = 'NWM Big Query Outputs'
        plot_slide_sheet = True # Add this line

Next, we'll test making this plot slide visible. First, open main.js. We'll add just two lines of code here:

.. code-block:: javascript
    
    document.getElementById('query-form').addEventListener('submit', function(event) {
        event.preventDefault();
        var formData = new FormData(this);
       
        // Check if all required fields are filled in
        const requiredFields = ['reach_id', 'start_date', 'start_time', 'end_date', 'end_time', 'table', 'variable'];
        const missingFields = requiredFields.filter(field => !formData.has(field));
        if (missingFields.length > 0) {
            TETHYS_APP_BASE.alert("danger", "Make sure to fill in all required fields.");
            return;
        }
	// Add these two lines:
        MAP_LAYOUT.show_plot(); 
        MAP_LAYOUT.update_plot(``, {}, {});
       
        fetch('/apps/nwm-bigquery-tutorial/', {
            method: 'POST',
            body: formData
         }).then(response => response.json())
         .then(data => {
            console.log(data);
        });
    });


`MAP_LAYOUT` is part of the built-in Tethys MapLayout javascript API. These lines simply make the plot slide visible, 
and set the graph labels and contents to blank. 

We can now test our plot slide: go to your application and refresh, run a query, and you should see something like this:

.. image:: images/plot_slide_showing_screenshot.png

2. Display Graph Data
---------------------
Now that we’ve got our plot slide displaying, we need to populate our graph with data. 
We’ll need to add onto our fetch request to do this, so change these lines in the fetch request in `main.js`:

.. code-block:: javascript
    
    fetch('/apps/nwm-bigquery-tutorial/', {
            method: 'POST',
            body: formData
         }).then(response => response.json())
         .then(data => {
            var variable = formData.get('variable');
            var reach = formData.get('reach_id');
            MAP_LAYOUT.update_plot(`${variable} at ${reach}`, data.data, data.graph_layout); // Add
         });

Now refresh your application and run a query, and you'll see your graph fill up with data like this:

.. image:: images/graph_data_showing_screenshot.png

As you can see, our data is displaying on the graph, with groups on the right side that can be hidden or shown by clicking on each one.

3. Solution
-----------
This concludes the Displaying Graph Data portion of this tutorial. 
GIT INSTRUCTIONS HERE


