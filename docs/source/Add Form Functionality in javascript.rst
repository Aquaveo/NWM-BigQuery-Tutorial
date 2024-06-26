Add Form Functionality in javascript
====================================
In this portion of the tutorial, we'll add form functionality to our query fields using javascript

0. Start From Previous Solution (Optional)
------------------------------------------
If you wish to use the previous solution as a starting point:

.. code-block:: bash
    
    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-4-Complete

1. Setup 
--------
Before we can begin working with our javascript, we'll need to include the file in our template. Go ahead and open `home.html` in the `templates` directory.
Add the following line to the bottom of the file:

.. code-block:: html

    {% block scripts %}
        {{ block.super }}
        <script type = "module" src="{% static 'nwm_bigquery_tutorial/js/app.js' %}"></script>
    {% endblock %}

2. Dynamic Changes in the Query Form
------------------------------------
Begin by creating a new file in the directory `public/js` called `app.js`. This file will contain all of our javascript code. 

As part of your queries, you'll need to include a forecast offset. This number will need to change according to what table you're querying, 
so we'll need to add a way to change this number dynamically.

Setup for forecast offset dynamic changes by adding this code to 'app.js':

.. code-block:: javascript

    const forecastOffsets = {"short_range": 1, "medium_range": 3, "medium_range_no_da": 3, "long_range": 6}

Then add this event listener to dynamically change the hidden forecast offset input fields’ value when the user makes a change in the table selector.
the $(function() { }) function portion makes sure that this code only runs once the page is loaded: 

.. code-block:: javascript
    
    $(function() {
        $("#table").on("change", function() {
            var table = $("#table").val();
            $("#forecast_offset").val(forecastOffsets[table]);
        });
    });

3. Form Submission Listener
---------------------------
Finally, we'll be adding a form submission listener. Add this code to our main function in `app.js`:

.. code-block:: javascript

    $("#query-form").on("submit", function(event) {
        event.preventDefault();
        var formData = new FormData(this);
       
        // Check if all required fields are filled in
        const requiredFields = ['reach_id', 'start_date', 'start_time', 'end_date', 'end_time', 'table', 'variable'];
        const missingFields = requiredFields.filter(field => !formData.get(field));
        if (missingFields.length > 0) {
            TETHYS_APP_BASE.alert("danger", "Make sure to fill in all required fields.");
            return;
        }
    })

This sets a listener for form submission, then prevents that submission from going through normally when the user presses the query button.
It then retrieves the input provided by the user in the form and stores that input in the variable formData. 
Lastly, it makes sure that the user has filled in each of the required fields and alerts the user if not. 
We can test and confirm the user’s input by displaying the inputs to the console with this code:

.. code-block:: javascript

    $("#query-form").on("submit", function(event) {
        event.preventDefault();
        var formData = new FormData(this);
       
        // Check if all required fields are filled in
        const requiredFields = ['reach_id', 'start_date', 'start_time', 'end_date', 'end_time', 'table', 'variable'];
        const missingFields = requiredFields.filter(field => !formData.get(field));
        if (missingFields.length > 0) {
            TETHYS_APP_BASE.alert("danger", "Make sure to fill in all required fields.");
            return;
        }
	// Add these lines to test the form submission handling
        console.log("Reach ID: ", formData.get('reach_id'));
        console.log("Start Date: ", formData.get('start_date'));
        console.log("Start Time: ", formData.get('start_time'));
        console.log("End Date: ", formData.get('end_date'));
        console.log("End Time: ", formData.get('end_time'));
        console.log("Table: ", formData.get('table'));
        console.log("Variable: ", formData.get('variable'));
        console.log("Forecast Offset: ", formData.get('forecast_offset'));
    });

Refresh your web page, and open your console. After providing some input in the form, press the query button. 
You should see your input displayed in the console. Run a few queries with different table selections to confirm 
that the forecast offset is changing as expected.

4. Solution
-----------
This concludes the Add Form Functionality in javascript portion of the NWM BigQuery Tutorial. You can view the solution on GitHub at https://github.com/Aquaveo/tethys-bigquery/tree/Step-5-Complete or clone it as follows:

.. code-block:: bash

    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-5-Complete 


