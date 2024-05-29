Add Click and Hover Detection on Stream features
================================================
In this section, you'll be learning a few more feature of Tethys. You'll be adding click detection to the stream 
features on the map, which will result in highlighting the selected stream. You'll then be adding hover detection on 
those highlighted streams so that the stream's information will pop up above the stream.

0. Start from Previous Solution (Optional)
-------------------------------------------
If you wish to use the previous solution as a starting point:

.. code-block:: bash

    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-10-Complete

1. Setup for Click Detection
----------------------------
First, we'll need to add a few imports. To begin, add the following code to `home.html`:

.. code-block:: html

    {% block scripts %}
        {{ block.super }}
        <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
    {% endblock %}

Next, add these lines to the top of your `main.js` file:

.. code-block:: javascript

    import Point from "https://js.arcgis.com/4.29/@arcgis/core/geometry/Point.js";
    import *  as geometryEngine from "https://js.arcgis.com/4.29/@arcgis/core/geometry/geometryEngine.js";

Next, you'll add click detection to the stream features on the map. This will allow you to highlight the selected stream and add the 
selected stream's reach id to the reach ID input field in the query parameters form.

To prepare to add click detection, we'll need to access the map itself, so we'll add a map variable in our window.onload function in our `main.js` file.

.. code-block:: javascript

    window.onload = function() {
    var map = TETHYS_MAP_VIEW.getMap();

Next, we'll add a click event listener to the map also in the window.onload function

.. code-block:: javascript 
    
    window.onload = function() {
    $("#table").on("change", function() {
        var table = $("#table").val();
        $("#forecast_offset").val(forecastOffsets[table]);
    });

    var map = TETHYS_MAP_VIEW.getMap();
    console.log(map);

For now, this listener just displays "map clicked" so we can test that your click detection is working. Go ahead and test it out!

2. Get Clicked on Stream Feature
---------------------------------


