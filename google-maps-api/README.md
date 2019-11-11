# Google Maps API(s) [SPEC]

### [Google API Console](https://console.cloud.google.com/google/maps-apis)
- Used to manage settings, enable sub-api's, billing, etc.
### [Heavy inspiration for Vue implementation](https://markus.oberlehner.net/blog/using-the-google-maps-api-with-vue/)
- There are two reasons for not using a vue library:
    - "First of all, using the plugin adds some overhead in terms of bundle size and second it also adds some overhead in terms of finding out how to do more complicated stuff with the API."
    - "If we use the plugin, we might have troubles finding out how to do more advanced stuff which might not be covered by the documentation of the vue2-google-maps plugin."

In <gmaps.vue> - The GET Request

    const script = document.createElement('script');
    script.async = true;
    script.defer = true;
    script.src = `https://maps.googleapis.com/maps/api/js?key=${API_KEY}&callback=${CALLBACK_NAME}`;
    script.onerror = rejectInitPromise;
    document.querySelector('head').appendChild(script);

In <Journal.vue> (Fluff taken out)

    <template>
        <div class="center">
            <TheMap class="google_map"/>   
        </div>
    </template>

    <script>
    import TheMap from '../Map/Map'

        export default {
            name: 'journal',
            components: {
            TheMap,
        },
    };
    </script>

    </script>

    <style lang="less" scoped>
        .google_map{
            width:55%;
            margin-right:2%;
            float: left;
        }
        .google_map iframe{
            width:100%;
        }
    </style>

In <Map.vue>

    <template>
        <div class="gmap"/>
    </template>

    <script>

    // Google Library for clustering "close" markers so that it looks less messy
    // npm install @google/markerclusterer
    import MarkerClusterer from '@google/markerclusterer';

    // Vanilla Google Map 
    import gmapsInit from './gmaps';


    ...

    </script>
