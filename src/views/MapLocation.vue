<template>
    <div>
      <GoogleMap api-key="googleMapsApiKey" style="width: 100%; height: 500px" :center="center" :zoom="3">
        <Polyline :options="flightPath" />
      </GoogleMap>
    </div>
</template>
  
<script lang="ts">
  import { defineComponent } from "vue";
  import { GoogleMap, Polyline } from "vue3-google-map";
  
  export default defineComponent({
    components: { GoogleMap, Polyline },
    props: {
      pickup: {
        type: Object,
        required: true,
      },
      dropoff: {
        type: Object,
        required: true,
      },
    },
    setup( props ) {

      console.log( props.pickup );
      console.log( props.dropoff );

      const googleMapsApiKey = "AIzaSyDQ8KCxvJCfSvz5WJTt9d-62p3p-xvI0rM";
      const center = { lat: parseFloat( props.pickup.latitude ), lng: parseFloat( props.pickup.longitude ) };
      const flightPlanCoordinates = [
        { lat: parseFloat( props.dropoff.latitude ), lng: parseFloat( props.dropoff.longitude ) },
      ];
      const flightPath = {
        path: flightPlanCoordinates,
        geodesic: true,
        strokeColor: "#FF0000",
        strokeOpacity: 1.0,
        strokeWeight: 2,
      };
  
      return { center, flightPath, googleMapsApiKey };
    },
  });
  </script>
<style scoped>
div {
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>