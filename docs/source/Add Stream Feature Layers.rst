Add Stream Feature Layers
=========================
In this section of the tutorial you will learn how to visualize ARCGIS data in your Tethys app, building on Map Layout Concepts

.. image:: /images/stream_features_configured_screenshot.png

0. Start from Previous Solution (Optional)
------------------------------------------
If you wish to use the previous solution as a starting point:

.. code-block:: bash
    
    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-2-Complete


1. Override compose_layers method of MapLayout
----------------------------------------------
Adding data to the map you created in the last section using the Tethys Map Layout feature is very simple. To do so, 
you are required to override the compose_layers function, copying its expected signatures(ie: arguments) and returning
a list of layer groups.For this tutorial, we’ll be using only ARCGIS layers. 

To begin, replace `controllers.py` with the following: 

.. code-block:: python

    from tethys_sdk.layouts import MapLayout
    from tethys_sdk.routing import controller
    from .app import NwmBigqueryTutorial as app

    @controller(name="home", app_workspace=True)
    class NWMBigQueryMap(MapLayout):
        app = app
        base_template = 'nwm_bigquery_tutorial/base.html'
        map_title = 'National Water Model BigQuery Tutorial'
        map_subtitle = 'NWM Big Query Outputs'
        basemaps = [
            'OpenStreetMap',
            'ESRI',
        ]

        def compose_layers(self, request, map_view, app_workspace, *args, **kwargs):
            # Streamflow layer
            streamflow_layer = self.build_arc_gis_layer(
                endpoint='https://mapservices.weather.noaa.gov/vector/rest/services/obs/NWM_Stream_Analysis/MapServer',
                layer_id = "streamflow",
                layer_name = "0",
                layer_title = "Streamflow",
                layer_variable="streamflow",
                visible=True,
                selectable=True
            )
        
            # Local layer
            local_layer = self.build_arc_gis_layer(
                endpoint='https://mapservices.weather.noaa.gov/vector/rest/services/obs/NWM_Stream_Analysis/MapServer',
                layer_id = "local",
                layer_name = "7",
                layer_title = "Local",
                layer_variable="local",
                visible=True,
                selectable=True
            )

            # Anomaly layer
            anomaly_layer = self.build_arc_gis_layer(
                endpoint='https://mapservices.weather.noaa.gov/vector/rest/services/obs/NWM_Stream_Analysis/MapServer',
                layer_id = "anomaly", 
                layer_name = "14", 
                layer_title = "Anomaly", 
                layer_variable="anomaly", 
                visible=True,
                selectable=True
            )

            # Add layers to map
            map_view.layers.append(streamflow_layer)
            map_view.layers.append(local_layer)
            map_view.layers.append(anomaly_layer)

            # Add layer to layer group
            layer_groups = [
                self.build_layer_group(
                    id='nwm_layers',
                    display_name='NWM Layers',
                    layer_control='checkbox',
                    layers=[streamflow_layer, local_layer, anomaly_layer],
                )
            ]

            return layer_groups

In each of our layers, you can add/change these attributes:

* **layer_name:** A unique identifier for this layer, important for identifying the layer later in other parts of the code.
* **layer_title:** What the layer should be titled (i.e. displayed as) in the left Layers panel of the application
* **layer_variable:** This is used as a way to group similar layers, but won't be used further in this tutorial.
* **visible:** Whether or not the layer is visible by default when the application loads
* **selectable:** Whether or not the individual features of the layer can be selected
* **plottable:** Whether or not the individual layers can be considered for plotting

The layer_groups list is composed using the built-in method of the MapLayout class called build_layer_groups. Appropriate arguments are provided to define 
how the layer group will be handled by the application:

* **id:** For use in code (not applicable to this tutorial beyond its definition)
* **diplay_name:** What the layer group should be titled (i.e. displayed as) in the left Layers panel of the application
* **layer_control:** What control should be used to toggle the layers on and off. You can choose checkbox for multi-layering or radio for isolated layer viewing.
* **layers:** The list of layers as created by the build_geojson_layer function that should be included in this layer group. They will be displayed together in the left Layers panel of the application.

Check your application at (http://localhost:8000/apps/nwm-bigquery-tutorial/) to see the stream features on your map. It should look something like the screenshot at the top of this section.

Along with those features, you'll notice you now have a 'Layers' panel to the left. You can pick which layers to show or hide on the map with that layers pal. In the next section, 
we'll be adding a new panel to that area with our query input fields.

2. Solution
-----------
This concludes the Add Stream Feature Layers portion of the NWM BigQuery Tutorial. You can view the solution on GitHub at https://github.com/Aquaveo/tethys-bigquery/tree/Step-3-Complete or clone it as follows:

.. code-block:: bash

    git clone https://github.com/Aquaveo/tethys-bigquery.git
    cd tethysapp-nwm_bigquery_tutorial
    git checkout Step-3-Complete 